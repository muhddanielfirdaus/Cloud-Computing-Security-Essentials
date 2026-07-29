# Lab 0: Environment Setup Report

**Course:** IKB42603 Cloud Computing  
**Lab:** Lab 0 - Environment Setup  
**Date:** July 29, 2026  
**Student Name:** Muhammad Daniel Firdaus  
**Student ID:** 52215225183

---

## Executive Summary

This report documents the successful completion of Lab 0 environment setup verification for the IKB42603 Cloud Computing course. All required tools and services have been verified as operational according to the Lab 0 Environment Setup Cheatsheet. The environment is fully prepared for subsequent lab exercises.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Verification Steps](#verification-steps)
4. [Evidence Summary](#evidence-summary)
5. [Conclusion](#conclusion)
6. [Appendix: Screenshots](#appendix-screenshots)

---

## 1. Introduction

### 1.1 Purpose

The purpose of this lab is to verify that all necessary tools and services required for the IKB42603 Cloud Computing course are properly installed and configured on the development environment. This includes:

- Container runtime (Docker)
- Cloud CLI tools (AWS CLI)
- Kubernetes tools (kind, kubectl)
- Security tools (OpenSSL, OATH Toolkit)
- Local cloud services (LocalStack)

### 1.2 Scope

This report covers the verification of nine critical components as specified in the IKB42603_Lab0_Environment_Setup_Cheatsheet.pdf guide.

---

## 2. Prerequisites

Before beginning the verification process, the following components should be installed:

- Docker (container runtime)
- AWS CLI (Amazon Web Services Command Line Interface)
- kind (Kubernetes in Docker)
- kubectl (Kubernetes command-line tool)
- OpenSSL (cryptographic toolkit)
- OATH Toolkit (one-time password authentication)
- A running Kubernetes cluster
- LocalStack (local AWS cloud stack)

---

## 3. Verification Steps

### Step 1: Verify Docker Installation

**Objective:** Confirm Docker is installed and operational.

**Command:**
```bash
docker --version
```

**Expected Output:** Docker version information

**Actual Output:**
```text
Docker version 26.1.5+dfsg1, build a72d7cd
```

**Status:** ✅ **PASS**

**Analysis:** Docker version 26.1.5+dfsg1 is successfully installed. This version is sufficient for running containerized applications required in the lab exercises.

**Evidence:** See [Screenshot 111.png](#screenshot-111)

---

### Step 2: Verify AWS CLI Installation

**Objective:** Confirm AWS Command Line Interface is installed and operational.

**Command:**
```bash
aws --version
```

**Expected Output:** AWS CLI version information including Python version and platform

**Actual Output:**
```text
aws-cli/2.36.9 Python/3.14.6 Linux/6.16.8+kali-amd64 exe/x86_64.kali.2025
```

**Status:** ✅ **PASS**

**Analysis:** AWS CLI version 2.36.9 is installed and operational. The CLI is running on Python 3.14.6 on a Kali Linux system (kernel 6.16.8). This version supports all AWS service interactions required for the course.

**Evidence:** See [Screenshot 112.png](#screenshot-112) and [Screenshot 119.png](#screenshot-119)

---

### Step 3: Verify kind Installation

**Objective:** Confirm kind (Kubernetes in Docker) is installed.

**Command:**
```bash
kind --version
```

**Expected Output:** kind version information

**Actual Output:**
```text
kind version 0.23.0
```

**Status:** ✅ **PASS**

**Analysis:** kind version 0.23.0 is successfully installed. This tool enables running local Kubernetes clusters using Docker containers, which is essential for container orchestration labs.

**Evidence:** See [Screenshot 113.png](#screenshot-113)

---

### Step 4: Verify kubectl Installation

**Objective:** Confirm Kubernetes command-line tool is installed.

**Command:**
```bash
kubectl version --client
```

**Expected Output:** kubectl client version and Kustomize version

**Actual Output:**
```text
Client Version: v1.33.4
Kustomize Version: v5.5.0
```

**Status:** ✅ **PASS**

**Analysis:** kubectl client version v1.33.4 is installed with Kustomize v5.5.0. This provides the necessary interface to interact with Kubernetes clusters and manage Kubernetes resources.

**Evidence:** See [Screenshot 114.png](#screenshot-114)

---

### Step 5: Verify OpenSSL Installation

**Objective:** Confirm OpenSSL cryptographic toolkit is installed.

**Command:**
```bash
openssl version
```

**Expected Output:** OpenSSL version information

**Actual Output:**
```text
OpenSSL 3.5.4 30 Sep 2025 (Library: OpenSSL 3.5.4 30 Sep 2025)
```

**Status:** ✅ **PASS**

**Analysis:** OpenSSL version 3.5.4 (released September 30, 2025) is installed. This provides essential cryptographic functions including certificate generation, encryption, and secure communication protocols needed for security-related labs.

**Evidence:** See [Screenshot 115.png](#screenshot-115)

---

### Step 6: Verify OATH Toolkit Installation

**Objective:** Confirm OATH Toolkit for one-time password generation is installed.

**Command:**
```bash
oathtool --version
```

**Expected Output:** oathtool version information

**Actual Output:**
```text
oathtool (OATH Toolkit) 2.6.14
Copyright (C) 2009-2026 Simon Josefsson.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by Simon Josefsson.
```

**Status:** ✅ **PASS**

**Analysis:** OATH Toolkit version 2.6.14 is successfully installed. This tool is essential for implementing and testing time-based one-time password (TOTP) and HMAC-based one-time password (HOTP) authentication mechanisms in security labs.

**Evidence:** See [Screenshot 116.png](#screenshot-116)

---

### Step 7: Verify Kubernetes Cluster Status

**Objective:** Confirm a Kubernetes cluster is running and nodes are in Ready state.

**Command:**
```bash
kubectl get nodes
```

**Expected Output:** List of Kubernetes nodes with STATUS "Ready"

**Actual Output:**
```text
NAME                 STATUS   ROLES           AGE     VERSION
ccse-control-plane   Ready    control-plane   4m32s   v1.30.0
```

**Status:** ✅ **PASS**

**Analysis:** A Kubernetes cluster named "ccse-control-plane" is running and operational. The control plane node shows a "Ready" status, indicating it has been running for 4 minutes and 32 seconds. The cluster is running Kubernetes version v1.30.0, which is suitable for course requirements.

**Evidence:** See [Screenshot 117.png](#screenshot-117)

---

### Step 8: Verify LocalStack Health

**Objective:** Confirm LocalStack (local AWS cloud stack) is running and services are available.

**Command:**
```bash
curl http://localhost:4566/_localstack/health
```

**Expected Output:** JSON response showing service availability

**Actual Output:**
```json
{
  "services": {
    "acm": "available",
    "apigateway": "available",
    "cloudformation": "available",
    "cloudwatch": "available",
    "config": "available",
    "dynamodb": "available",
    "dynamodbstreams": "available",
    "ec2": "available",
    "es": "available",
    "events": "available",
    "firehose": "available",
    "iam": "available",
    "kinesis": "available",
    "kms": "available",
    "lambda": "available",
    "logs": "available",
    "opensearch": "available",
    "redshift": "available",
    "resource-groups": "available",
    "resourcegroupstaggingapi": "available",
    "route53": "available",
    "route53resolver": "available",
    "s3": "available",
    "s3control": "available",
    "scheduler": "available",
    "secretsmanager": "available",
    "ses": "available",
    "sns": "available",
    "sqs": "available",
    "ssm": "available",
    "stepfunctions": "available",
    "sts": "available",
    "transcribe": "available"
  },
  "edition": "community",
  "version": "3.8.1"
}
```

**Status:** ✅ **PASS**

**Analysis:** LocalStack Community Edition version 3.8.1 is running successfully on `http://localhost:4566`. All essential AWS services are available including:
- **IAM** (Identity and Access Management)
- **S3** (Simple Storage Service)
- **Lambda** (Serverless compute)
- **DynamoDB** (NoSQL database)
- **SQS** (Simple Queue Service)
- **SNS** (Simple Notification Service)
- **STS** (Security Token Service)

This local AWS emulation environment enables testing cloud applications without incurring AWS costs.

**Evidence:** See [Screenshot 118.png](#screenshot-118)

---

### Step 9: Verify AWS STS Get Caller Identity

**Objective:** Confirm AWS CLI can communicate with LocalStack and retrieve identity information.

**Command:**
```bash
aws $EP sts get-caller-identity
```

*Note: `$EP` is an environment variable containing LocalStack endpoint configuration (typically `--endpoint-url=http://localhost:4566`)*

**Expected Output:** JSON response with UserId, Account, and Arn

**Actual Output:**
```json
{
    "UserId": "AKIAIOSFODNN7EXAMPLE",
    "Account": "000000000000",
    "Arn": "arn:aws:iam::000000000000:root"
}
```

**Status:** ✅ **PASS**

**Analysis:** The AWS CLI successfully authenticated with the LocalStack endpoint and retrieved caller identity information. The response contains:
- **UserId:** AKIAIOSFODNN7EXAMPLE (standard LocalStack test credentials)
- **Account:** 000000000000 (default LocalStack account ID)
- **Arn:** Root account ARN for the LocalStack environment

This confirms proper integration between AWS CLI and LocalStack, enabling AWS service operations in the local development environment.

**Evidence:** See [Screenshot 120.png](#screenshot-120)

---

## 4. Evidence Summary

All verification steps have been completed successfully with supporting evidence:

| Step | Component | Version/Status | Evidence File | Result |
|------|-----------|----------------|---------------|--------|
| 1 | Docker | 26.1.5+dfsg1 | 111.png | ✅ PASS |
| 2 | AWS CLI | 2.36.9 | 112.png, 119.png | ✅ PASS |
| 3 | kind | 0.23.0 | 113.png | ✅ PASS |
| 4 | kubectl | v1.33.4 (Kustomize v5.5.0) | 114.png | ✅ PASS |
| 5 | OpenSSL | 3.5.4 | 115.png | ✅ PASS |
| 6 | OATH Toolkit | 2.6.14 | 116.png | ✅ PASS |
| 7 | Kubernetes Cluster | ccse-control-plane (Ready) | 117.png | ✅ PASS |
| 8 | LocalStack | 3.8.1 (Community) | 118.png | ✅ PASS |
| 9 | AWS STS Identity | Connected successfully | 120.png | ✅ PASS |

---

## 5. Conclusion

### 5.1 Summary

The Lab 0 environment setup has been successfully completed and verified. All nine required components are installed, properly configured, and operational:

1. ✅ **Docker** - Container runtime ready for containerized applications
2. ✅ **AWS CLI** - Cloud command-line interface configured
3. ✅ **kind** - Kubernetes in Docker tool available
4. ✅ **kubectl** - Kubernetes client tool operational
5. ✅ **OpenSSL** - Cryptographic toolkit installed
6. ✅ **OATH Toolkit** - Authentication tool ready
7. ✅ **Kubernetes Cluster** - Local cluster running and accessible
8. ✅ **LocalStack** - Local AWS services available
9. ✅ **AWS Integration** - CLI successfully communicates with LocalStack

### 5.2 Environment Status

**Overall Status:** ✅ **READY FOR LAB EXERCISES**

The development environment meets all prerequisites specified in the IKB42603_Lab0_Environment_Setup_Cheatsheet.pdf. The system is fully prepared to proceed with:
- Container orchestration exercises
- Cloud service deployments
- Security and authentication implementations
- AWS service interactions
- Kubernetes resource management

### 5.3 Next Steps

With the environment successfully verified, the following activities can proceed:
1. Continue with Lab 1 exercises
2. Deploy containerized applications
3. Interact with AWS services via LocalStack
4. Perform Kubernetes cluster operations
5. Implement security and authentication mechanisms

### 5.4 Notes

- All tools are running on Kali Linux (kernel 6.16.8+kali-amd64)
- LocalStack is configured to run on the default port 4566
- The Kubernetes cluster uses the name "ccse-control-plane"
- Default LocalStack credentials are being used for testing purposes

---

## 6. Appendix: Screenshots

### Screenshot 111.png
**Docker Version Verification**

![Docker Version](images/111.png)

Command output showing Docker version 26.1.5+dfsg1, build a72d7cd

---

### Screenshot 112.png
**AWS CLI Version Verification**

![AWS CLI Version](images/112.png)

Command output showing aws-cli/2.36.9 with Python/3.14.6 on Linux/6.16.8+kali-amd64

---

### Screenshot 113.png
**kind Version Verification**

![kind Version](images/113.png)

Command output showing kind version 0.23.0

---

### Screenshot 114.png
**kubectl Version Verification**

![kubectl Version](images/114.png)

Command output showing Client Version: v1.33.4 and Kustomize Version: v5.5.0

---

### Screenshot 115.png
**OpenSSL Version Verification**

![OpenSSL Version](images/115.png)

Command output showing OpenSSL 3.5.4 30 Sep 2025

---

### Screenshot 116.png
**OATH Toolkit Version Verification**

![OATH Toolkit Version](images/116.png)

Command output showing oathtool (OATH Toolkit) 2.6.14 with license information

---

### Screenshot 117.png
**Kubernetes Cluster Status**

![Kubernetes Nodes](images/117.png)

Command output showing ccse-control-plane node in Ready status running v1.30.0

---

### Screenshot 118.png
**LocalStack Health Check**

![LocalStack Health](images/118.png)

JSON response showing LocalStack Community 3.8.1 with all services marked as "available"

---

### Screenshot 119.png
**AWS CLI Version (Additional)**

![AWS CLI Version Additional](images/119.png)

Additional verification of AWS CLI version 2.36.9

---

### Screenshot 120.png
**AWS STS Get Caller Identity**

![AWS STS Identity](images120.png)

JSON response showing successful authentication with LocalStack including UserId, Account, and Arn

---

**End of Report**
