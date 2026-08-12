# Screenshot Placement Guide for Lab 2 Report

This document shows exactly where each screenshot should be inserted in the lab report.

---

## 📸 Screenshot Reference Map

| Screenshot File | Section | What It Shows | Insert Location in Report |
|----------------|---------|---------------|---------------------------|
| **301.png** | Lab Environment Setup | `kubectl get nodes` output showing cluster status | After "Cluster Information" heading, after the kubectl command |
| **302.png** | Part 1 - Namespace Isolation | `kubectl get pods,svc -n tenant-a` showing tenant-a resources | Step 1.3: After "Check pods and services in tenant-a" |
| **303.png** | Part 1 - Namespace Isolation | `kubectl get pods,svc -n tenant-b` showing tenant-b resources | Step 1.3: After "Check pods and services in tenant-b" |
| **304.png** | Part 1 - Namespace Isolation | Service IP address extraction (10.96.203.158) | Step 1.4: After the jsonpath command |
| **305.png** | Part 1 - Namespace Isolation | Cross-namespace connectivity test showing HTTP 200 | Step 1.5: After the curl command from tenant-a |
| **306.png** | Part 2 - Resource Quotas | Resource quota description showing limits and usage | Step 2.2: After `kubectl describe resourcequota` command |
| **307.png** | Part 2 - Resource Quotas | probe.yaml file content showing pod definition | Step 2.3: After "Create a pod manifest probe.yaml" |
| **308.png** | Part 2 - Resource Quotas | "pod/probe created" confirmation message | Step 2.3: After second `kubectl apply -f probe.yaml` command |
| **309.png** | Part 2 - Resource Quotas | Pod status showing Error state | Step 2.3: After `kubectl get pod probe` command |
| **310.png** | Part 2 - Resource Quotas | Pod logs showing "HTTP 000" | Step 2.3: After `kubectl logs probe` command |
| **311.png** | Part 3 - Network Policies | Network policy list showing default-deny-ingress | Step 3.2: After `kubectl get networkpolicy -A` command |
| **312.png** | Part 3 - Network Policies | Updated resource quota status | Step 3.4: After second `kubectl describe resourcequota` command |
| **313.png** | Part 4 - RBAC | "yes" response for tenant-a secret access | Step 4.4: After first `kubectl auth can-i` command |
| **314.png** | Part 4 - RBAC | "no" response for tenant-b secret access | Step 4.4: After second `kubectl auth can-i` command |
| **315.png** | Part 5 - Data Isolation | Docker volume creation and data write output | Step 5.2: After the docker run command with SENSITIVE data |

---

## 📋 Quick Reference by Screenshot Number

### Screenshot 301 - Cluster Status
**Location:** Lab Environment Setup → Cluster Information  
**Insert after:** `kubectl get nodes` command  
**Shows:** Control plane node ready status

---

### Screenshot 302 - Tenant A Resources
**Location:** Part 1, Step 1.3  
**Insert after:** "Check pods and services in tenant-a"  
**Shows:** Pod and service running in tenant-a namespace

---

### Screenshot 303 - Tenant B Resources
**Location:** Part 1, Step 1.3  
**Insert after:** "Check pods and services in tenant-b"  
**Shows:** Pod and service running in tenant-b namespace

---

### Screenshot 304 - Service IP Extraction
**Location:** Part 1, Step 1.4  
**Insert after:** `kubectl get svc web -n tenant-b -o jsonpath='{.spec.clusterIP}'`  
**Shows:** Extracted ClusterIP address (10.96.203.158)

---

### Screenshot 305 - Cross-Namespace Test
**Location:** Part 1, Step 1.5  
**Insert after:** `kubectl -n tenant-a run probe --rm -it --image=curlimages/curl...`  
**Shows:** Successful HTTP 200 response proving no isolation yet

---

### Screenshot 306 - Resource Quota Details
**Location:** Part 2, Step 2.2  
**Insert after:** `kubectl describe resourcequota tenant-a-quota -n tenant-a`  
**Shows:** Resource quota limits and current usage

---

### Screenshot 307 - Probe Pod YAML
**Location:** Part 2, Step 2.3  
**Insert after:** "Create a pod manifest probe.yaml"  
**Shows:** Complete probe pod manifest with resource requests

---

### Screenshot 308 - Pod Creation Confirmation
**Location:** Part 2, Step 2.3  
**Insert after:** Second `kubectl apply -f probe.yaml` command  
**Shows:** "pod/probe created" confirmation message

---

### Screenshot 309 - Pod Error Status
**Location:** Part 2, Step 2.3  
**Insert after:** `kubectl get pod probe -n tenant-a`  
**Shows:** Pod in Error state with 0/1 ready

---

### Screenshot 310 - Pod Logs
**Location:** Part 2, Step 2.3  
**Insert after:** `kubectl logs probe -n tenant-a`  
**Shows:** HTTP 000 error in logs

---

### Screenshot 311 - Network Policy List
**Location:** Part 3, Step 3.2  
**Insert after:** `kubectl get networkpolicy -A`  
**Shows:** default-deny-ingress policy in tenant-b namespace

---

### Screenshot 312 - Updated Quota Status
**Location:** Part 3, Step 3.4  
**Insert after:** Second `kubectl describe resourcequota tenant-a-quota -n tenant-a`  
**Shows:** Resource usage returned to baseline after probe deletion

---

### Screenshot 313 - RBAC Allow Access
**Location:** Part 4, Step 4.4  
**Insert after:** `kubectl auth can-i get secrets -n tenant-a --as=system:serviceaccount:tenant-a:tenant-a-user`  
**Shows:** "yes" response indicating allowed access

---

### Screenshot 314 - RBAC Deny Access
**Location:** Part 4, Step 4.4  
**Insert after:** `kubectl auth can-i get secrets -n tenant-b --as=system:serviceaccount:tenant-a:tenant-a-user`  
**Shows:** "no" response indicating denied access (proper isolation)

---

### Screenshot 315 - Data Isolation Test
**Location:** Part 5, Step 5.2  
**Insert after:** `docker run --rm -v ccse-vol:/data alpine sh -c 'echo SENSITIVE-PATIENT-RECORD...'`  
**Shows:** Docker pulling alpine image and executing scan-done

---

## 🎯 How to Use This Guide

1. Open your **Lab2-Secure-Isolation-and-Multitenancy.md** file
2. Look for the text **"📸 INSERT SCREENSHOT HERE: XXX.png"**
3. Use this guide to understand what each screenshot shows
4. Insert the corresponding screenshot image at that location
5. You can either:
   - Use markdown image syntax: `![Description](301.png)`
   - Or insert the image directly if using a markdown editor with image support
   - Or copy-paste the actual screenshot image into your document

---

## 💡 Tips for Inserting Screenshots

### Option 1: Markdown Syntax
```markdown
![kubectl get nodes output](301.png)
```

### Option 2: HTML in Markdown
```html
<img src="301.png" alt="kubectl get nodes output" width="800">
```

### Option 3: Direct Image Insertion
- If using Word, Google Docs, or similar: Simply copy and paste the image where indicated
- If using a markdown editor with image support: Drag and drop the image at the marker location

---

## ✅ Verification Checklist

After inserting all screenshots, verify:

- [ ] Screenshot 301 shows cluster node status
- [ ] Screenshot 302 shows tenant-a pods and services
- [ ] Screenshot 303 shows tenant-b pods and services  
- [ ] Screenshot 304 shows IP address 10.96.203.158
- [ ] Screenshot 305 shows HTTP 200 response
- [ ] Screenshot 306 shows resource quota with 1/5 pods used
- [ ] Screenshot 307 shows probe.yaml content
- [ ] Screenshot 308 shows "pod/probe created"
- [ ] Screenshot 309 shows pod in Error status
- [ ] Screenshot 310 shows "HTTP 000" in logs
- [ ] Screenshot 311 shows network policy listed
- [ ] Screenshot 312 shows updated quota status
- [ ] Screenshot 313 shows "yes" for tenant-a access
- [ ] Screenshot 314 shows "no" for tenant-b access
- [ ] Screenshot 315 shows docker alpine image download

---

**Total Screenshots: 15** (301.png through 315.png)

All screenshots are now clearly marked in the report with **📸 INSERT SCREENSHOT HERE** indicators!
