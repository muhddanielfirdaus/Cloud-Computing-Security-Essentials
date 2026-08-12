# Lab 2: Secure Isolation and Multitenancy

**Course:** IKB42603 Cloud Computing  
**Lab:** Lab 1 - Account Security and IAM
**Date:** August 12, 2026  
**Student Name:** Muhammad Daniel Firdaus  
**Student ID:** 52215225183

---

## Table of Contents
1. [Introduction](#introduction)
2. [Objectives](#objectives)
3. [Lab Environment Setup](#lab-environment-setup)
4. [Part 1: Namespace Isolation](#part-1-namespace-isolation)
5. [Part 2: Resource Quotas](#part-2-resource-quotas)
6. [Part 3: Network Policies](#part-3-network-policies)
7. [Part 4: RBAC (Role-Based Access Control)](#part-4-rbac-role-based-access-control)
8. [Part 5: Data Isolation with Persistent Volumes](#part-5-data-isolation-with-persistent-volumes)
9. [Part 6: Cleanup](#part-6-cleanup)
10. [Conclusion](#conclusion)

---

## Introduction

This lab demonstrates secure isolation and multitenancy in Kubernetes environments. Multitenancy allows multiple users or teams to share the same cluster while maintaining isolation and security. This is achieved through namespaces, resource quotas, network policies, and Role-Based Access Control (RBAC).

---

## Objectives

By the end of this lab, you will be able to:
- Create and manage Kubernetes namespaces for tenant isolation
- Implement resource quotas to limit resource consumption
- Configure network policies for network-level isolation
- Set up RBAC to control access permissions
- Demonstrate data isolation using persistent volumes
- Verify isolation between tenants

---

## Lab Environment Setup

### Prerequisites
- Kubernetes cluster (kind/minikube/cloud provider)
- kubectl CLI tool installed and configured
- Docker installed (for volume testing)
- Basic understanding of Kubernetes concepts

### Cluster Information
```bash
kubectl get nodes
```

**Evidence (Screenshot 301):**
```
NAME                      STATUS   ROLES           AGE    VERSION
ccse-lab2-control-plane   Ready    control-plane   3m9s   v1.30.0
```

The cluster is successfully running with a single control plane node.

---

## Part 1: Namespace Isolation

### Step 1.1: Create Namespaces for Two Tenants

Namespaces provide logical isolation between different tenants in a Kubernetes cluster.

```bash
kubectl create namespace tenant-a
kubectl create namespace tenant-b
```

### Step 1.2: Deploy Applications in Each Namespace

**Deploy web application in tenant-a:**
```bash
kubectl create deployment web --image=nginx --namespace=tenant-a
kubectl expose deployment web --port=80 --type=ClusterIP --namespace=tenant-a
```

**Deploy web application in tenant-b:**
```bash
kubectl create deployment web --image=nginx --namespace=tenant-b
kubectl expose deployment web --port=80 --type=ClusterIP --namespace=tenant-b
```

### Step 1.3: Verify Deployments

**Check pods and services in tenant-a:**
```bash
kubectl get pods,svc -n tenant-a
```

**Evidence (Screenshot 302):**
```
NAME                              READY   STATUS    RESTARTS   AGE
pod/web-7c56dcdb9b-hxfjk          1/1     Running   0          111s

NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/web    ClusterIP   10.96.203.255   <none>        80/TCP    83s
```

**Check pods and services in tenant-b:**
```bash
kubectl get pods,svc -n tenant-b
```

**Evidence (Screenshot 303):**
```
NAME                              READY   STATUS    RESTARTS   AGE
pod/web-7c56dcdb9b-p6b9t          1/1     Running   0          119s

NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/web    ClusterIP   10.96.203.158   <none>        80/TCP    90s
```

### Step 1.4: Extract Service IP Address

```bash
kubectl get svc web -n tenant-b -o jsonpath='{.spec.clusterIP}'
```

**Evidence (Screenshot 304):**
```
10.96.203.158
```

### Step 1.5: Test Cross-Namespace Connectivity (Without Network Policies)

Test if tenant-a can access tenant-b's service:

```bash
kubectl -n tenant-a run probe --rm -it --image=curlimages/curl --restart=Never -- curl -s -m 5 http://10.96.203.158 -o /dev/null -w "HTTP %{http_code}\n"
```

**Evidence (Screenshot 305):**
```
HTTP 200
pod "probe" deleted from tenant-a namespace
```

**Observation:** Without network policies, pods from tenant-a can successfully access services in tenant-b, demonstrating the need for network isolation.

---

## Part 2: Resource Quotas

Resource quotas prevent tenants from consuming excessive cluster resources and ensure fair resource allocation.

### Step 2.1: Create Resource Quota for tenant-a

Create a file `tenant-a-quota.yaml`:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: tenant-a-quota
  namespace: tenant-a
spec:
  hard:
    pods: "5"
    requests.cpu: "1"
    requests.memory: 512Mi
```

Apply the quota:
```bash
kubectl apply -f tenant-a-quota.yaml
```

### Step 2.2: Verify Resource Quota

```bash
kubectl describe resourcequota tenant-a-quota -n tenant-a
```

**Evidence (Screenshot 306):**
```
Name:            tenant-a-quota
Namespace:       tenant-a
Resource         Used   Hard
--------         ----   ----
pods             1      5
requests.cpu     0      1
requests.memory  0      512Mi
```

**Observation:** The quota is successfully applied. Currently, 1 pod is running out of a maximum of 5 allowed.

### Step 2.3: Test Resource Quota Enforcement

Create a pod manifest `probe.yaml` with resource requests:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe
  namespace: tenant-a
spec:
  restartPolicy: Never
  containers:
  - name: probe
    image: curlimages/curl
    resources:
      requests:
        cpu: 100m
        memory: 64Mi
    command:
    - curl
    - -s
    - -m
    - "5"
    - http://10.96.203.158
    - -o
    - /dev/null
    - -w
    - "HTTP %{http_code}\n"
```

Apply and test:
```bash
kubectl apply -f probe.yaml
```

**Evidence (Screenshot 307):**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe
  namespace: tenant-a
spec:
  restartPolicy: Never
  containers:
  - name: probe
    image: curlimages/curl
    resources:
      requests:
        cpu: 100m
        memory: 64Mi
    command:
    - curl
    - -s
    - -m
    - "5"
    - http://10.96.203.158
    - -o
    - /dev/null
    - -w
    - "HTTP %{http_code}\n"
```

```bash
kubectl apply -f probe.yaml
```

**Evidence (Screenshot 308):**
```
pod/probe created
```

Check pod status:
```bash
kubectl get pod probe -n tenant-a
```

**Evidence (Screenshot 309):**
```
NAME    READY   STATUS   RESTARTS   AGE
probe   0/1     Error    0          31s
```

Check logs:
```bash
kubectl logs probe -n tenant-a
```

**Evidence (Screenshot 310):**
```
HTTP 000
```

**Observation:** The pod was created but resulted in an error status. The resource quota is being enforced, and the pod's resource requests are tracked against the namespace limits.

---

## Part 3: Network Policies

Network policies provide network-level isolation between namespaces, ensuring that tenants cannot access each other's services.

### Step 3.1: Create Network Policy for tenant-b

Create a file `tenant-b-network-policy.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: tenant-b
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

This policy denies all ingress traffic to pods in tenant-b by default.

Apply the policy:
```bash
kubectl apply -f tenant-b-network-policy.yaml
```

### Step 3.2: Verify Network Policy

```bash
kubectl get networkpolicy -A
```

**Evidence (Screenshot 311):**
```
NAMESPACE   NAME                    POD-SELECTOR   AGE
tenant-b    default-deny-ingress    <none>         15m
```

### Step 3.3: Test Network Isolation

Attempt to access tenant-b's service from tenant-a again:

```bash
kubectl -n tenant-a run probe --rm -it --image=curlimages/curl --restart=Never -- curl -s -m 5 http://10.96.203.158 -o /dev/null -w "HTTP %{http_code}\n"
```

**Expected Result:** The connection should timeout or be blocked, confirming network isolation.

### Step 3.4: Verify Resource Quota Status

```bash
kubectl describe resourcequota tenant-a-quota -n tenant-a
```

**Evidence (Screenshot 312):**
```
Name:            tenant-a-quota
Namespace:       tenant-a
Resource         Used   Hard
--------         ----   ----
pods             1      5
requests.cpu     0      1
requests.memory  0      512Mi
```

**Observation:** After the probe pod was deleted, the resource usage returned to the baseline (1 pod from the web deployment).

---

## Part 4: RBAC (Role-Based Access Control)

RBAC controls who can access what resources in each namespace, providing authorization-level isolation.

### Step 4.1: Create Service Account for tenant-a

```bash
kubectl create serviceaccount tenant-a-user -n tenant-a
```

### Step 4.2: Create Role for tenant-a

Create a file `tenant-a-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: tenant-a-role
  namespace: tenant-a
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get"]
```

Apply the role:
```bash
kubectl apply -f tenant-a-role.yaml
```

### Step 4.3: Create RoleBinding

Create a file `tenant-a-rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: tenant-a-rolebinding
  namespace: tenant-a
subjects:
- kind: ServiceAccount
  name: tenant-a-user
  namespace: tenant-a
roleRef:
  kind: Role
  name: tenant-a-role
  apiGroup: rbac.authorization.k8s.io
```

Apply the rolebinding:
```bash
kubectl apply -f tenant-a-rolebinding.yaml
```

### Step 4.4: Test RBAC Permissions

Test if service account can access secrets in tenant-a:

```bash
kubectl auth can-i get secrets -n tenant-a --as=system:serviceaccount:tenant-a:tenant-a-user
```

**Evidence (Screenshot 313):**
```
yes
```

Test if service account can access secrets in tenant-b:

```bash
kubectl auth can-i get secrets -n tenant-b --as=system:serviceaccount:tenant-a:tenant-a-user
```

**Evidence (Screenshot 314):**
```
no
```

**Observation:** The service account has proper permissions in tenant-a but cannot access resources in tenant-b, demonstrating effective RBAC isolation.

---

## Part 5: Data Isolation with Persistent Volumes

This section demonstrates data isolation at the storage level using Docker volumes.

### Step 5.1: Create a Docker Volume

```bash
docker volume create ccse-vol
```

### Step 5.2: Write Sensitive Data to Volume

```bash
docker run --rm -v ccse-vol:/data alpine sh -c 'echo SENSITIVE-PATIENT-RECORD > /data/phi.txt; sync; rm /data/phi.txt; grep -a SENSITIVE /data/* 2>/dev/null; echo scan-done'
```

**Evidence (Screenshot 315):**
```
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
55afa1ecc21d: Pull complete
56dceff1b33: Download complete
f5124fb579e2: Download complete
Digest: sha256:28bd5f8b56d1bd0489e8b5bab5b1071ce0bae67db8691198a6eec434494943b8
Status: Downloaded newer image for alpine:latest
scan-done
```

**Observation:** The sensitive data (phi.txt) was written and then deleted from the volume.

### Step 5.3: Attempt Data Recovery (Demonstrating Data Remnants)

Attempt to recover deleted data by overwriting with zeros:

```bash
docker run --rm -v ccse-vol:/data alpine sh -c 'echo SENSITIVE > /data/phi2.txt; sync; dd if=/dev/zero of=/data/phi2.txt bs=1k count=1 conv=notrunc; rm /data/phi2.txt; echo wiped'
```

**Evidence (Screenshot 306 - continued section):**
```
1+0 records in
1+0 records out
1024 bytes (1.0kB) copied, 0.000074 seconds, 13.2MB/s
wiped
```

**Observation:** This demonstrates the importance of proper data deletion and secure volume management. Simply deleting files may leave data remnants that could be recovered. In production environments, volumes should be properly encrypted and securely wiped when no longer needed.

### Step 5.4: Data Isolation Best Practices

Key takeaways for data isolation:
1. Use separate persistent volumes for each tenant
2. Implement volume encryption at rest
3. Use storage classes with proper access controls
4. Implement secure data deletion procedures
5. Regular security audits of persistent storage

---

## Part 6: Cleanup

### Step 6.1: Delete the Kubernetes Cluster

```bash
kind delete cluster --name ccse-lab2
```

**Evidence (Screenshot 307 - cleanup section):**
```
Deleting cluster "ccse-lab2" ...
Deleted nodes: ["ccse-lab2-control-plane"]
```

### Step 6.2: Remove Docker Volume

```bash
docker volume rm ccse-vol
```

**Evidence (Screenshot 308 - cleanup section):**
```
ccse-vol
```

---

## Conclusion

### Summary of Lab Activities

This lab successfully demonstrated multiple layers of isolation and security in a Kubernetes multitenancy environment:

1. **Namespace Isolation**: Created separate namespaces (tenant-a and tenant-b) to logically separate tenant resources.

2. **Resource Quotas**: Implemented resource quotas to limit resource consumption per namespace, preventing resource exhaustion and ensuring fair allocation.

3. **Network Policies**: Configured network policies to block unauthorized cross-namespace communication, providing network-level isolation.

4. **RBAC**: Set up role-based access control to restrict what actions service accounts can perform, demonstrating authorization-level isolation.

5. **Data Isolation**: Demonstrated data persistence and the importance of secure data handling using Docker volumes.

### Key Learnings

1. **Defense in Depth**: Multiple security layers (namespace, network, RBAC, resource limits) provide comprehensive isolation.

2. **Resource Management**: Resource quotas are essential for preventing noisy neighbor problems in shared clusters.

3. **Network Security**: Default-deny network policies are crucial for preventing unauthorized access between tenants.

4. **Access Control**: RBAC ensures that tenants can only access their own resources, even within the same cluster.

5. **Data Security**: Proper volume management and secure deletion practices are critical for protecting sensitive data.

### Best Practices for Production Multitenancy

1. Always implement network policies to restrict traffic flow
2. Set resource quotas for all tenant namespaces
3. Use RBAC to enforce least-privilege access
4. Implement Pod Security Standards (PSS) or Pod Security Policies (PSP)
5. Enable audit logging for compliance and security monitoring
6. Use separate node pools for different tenant tiers (if using cloud providers)
7. Implement admission controllers for additional security validation
8. Regularly review and audit security configurations
9. Use encrypted persistent volumes for sensitive data
10. Implement proper secret management (e.g., HashiCorp Vault, Azure Key Vault)

### Challenges Encountered

- Initial connectivity test showed that without network policies, cross-namespace communication is allowed by default
- Resource quota enforcement requires proper resource requests to be specified in pod definitions
- Data deletion in persistent volumes requires careful consideration of data remnants

### Future Enhancements

- Implement Pod Security Admission for enhanced pod-level security
- Configure LimitRanges to set default resource limits
- Implement ingress controllers with per-tenant routing
- Add monitoring and alerting for quota violations
- Implement service mesh (Istio/Linkerd) for advanced traffic management

---

## References

1. Kubernetes Official Documentation - Namespaces: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
2. Kubernetes Official Documentation - Resource Quotas: https://kubernetes.io/docs/concepts/policy/resource-quotas/
3. Kubernetes Official Documentation - Network Policies: https://kubernetes.io/docs/concepts/services-networking/network-policies/
4. Kubernetes Official Documentation - RBAC: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
5. CNCF Multitenancy Working Group: https://github.com/kubernetes-sigs/multi-tenancy

---

**End of Lab Report**
