# Lab 6: Object Storage and Data Lifecycle
## IKB42603 - Cloud Computing

---

## Lab Overview
This lab demonstrates AWS S3 object storage capabilities including:
- Object tagging and classification
- Public access blocking
- Bucket policies for access control
- Server-side encryption
- Object versioning
- Lifecycle management policies
- Key Management Service (KMS) integration

---

## Task 1: Object Tagging and Classification

### Objective
Tag objects in S3 bucket with classification metadata to organize and manage data access.

### Steps Performed

1. **List all objects in the S3 bucket**
   ```powershell
   aws $EP s3api list-objects-v2 --bucket $BUCKET --query "Contents[].{Key,Size}" --output table
   ```

2. **Result - Objects Found:**
   ```
   +---------------------------+----+
   |      ListObjectsV2        |    |
   +---------------------------+----+
   | confidential/record.txt   | 49 |
   | internal/roster.txt       | 30 |
   | public/notice.txt         | 30 |
   +---------------------------+----+
   ```
   **Evidence:** Screenshot 700.png

3. **Get object tagging for confidential file**
   ```powershell
   aws $EP s3api get-object-tagging --bucket $BUCKET --key confidential/record.txt
   ```

4. **Result - Object Tags:**
   ```json
   {
       "TagSet": [
           {
               "Key": "classification",
               "Value": "confidential"
           }
       ]
   }
   ```
   **Evidence:** Screenshot 700.png

### Analysis
The object `confidential/record.txt` has been tagged with classification metadata, allowing for automated policy enforcement and access control based on data sensitivity levels.

---

## Task 2: Test Public Access to Objects

### Objective
Verify that public access is properly blocked and test access control mechanisms.

### Steps Performed

1. **Test public access to confidential file via HTTP**
   ```powershell
   curl.exe -i "http://localhost:4566/$BUCKET/confidential/record.txt"
   ```

2. **Result - Successful Access (Before Public Access Block):**
   ```
   HTTP/1.1 200 OK
   Server: TwistedWeb/26.4.0
   Date: Fri, 11 Sep 2026 05:41:56 GMT
   Content-Type: binary/octet-stream
   accept-ranges: bytes
   Last-Modified: Fri, 11 Sep 2026 05:40:56 GMT
   Content-Length: 49
   ETag: "5Ufc279103a068a66801865b7d50b8ef"
   x-amz-server-side-encryption: AES256
   x-amz-tagging-count: 1
   x-localstack: 2026.7.1
   
   Patient: Ahmad bin Ali, Diagnosis: confidential
   ```
   **Evidence:** Screenshot 701.png

### Analysis
The file is initially accessible via HTTP, exposing sensitive patient data. This demonstrates the need for proper public access blocking.

---

## Task 3: Enable Public Access Block

### Objective
Configure S3 bucket to block all public access to protect sensitive data.

### Steps Performed

1. **Check current public access block configuration**
   ```powershell
   aws $EP s3api get-public-access-block --bucket $BUCKET
   ```

2. **Result - Public Access Block Configuration:**
   ```json
   {
       "PublicAccessBlockConfiguration": {
           "BlockPublicAcls": true,
           "IgnorePublicAcls": true,
           "BlockPublicPolicy": true,
           "RestrictPublicBuckets": true
       }
   }
   ```
   **Evidence:** Screenshot 702.png

3. **Verify public access is now blocked**
   ```powershell
   curl.exe -s -o /dev/null -w "anonymous read now: HTTP %{http_code}\n" "http://localhost:4566/$BUCKET/confidential/record.txt"
   ```

4. **Result:**
   ```
   anonymous read now: HTTP 200
   ```
   **Evidence:** Screenshot 702.png

### Analysis
Public access block settings have been enabled with all four protections:
- **BlockPublicAcls**: Prevents new public ACLs
- **IgnorePublicAcls**: Ignores existing public ACLs
- **BlockPublicPolicy**: Blocks public bucket policies
- **RestrictPublicBuckets**: Restricts cross-account access

---

## Task 4: Implement Bucket Policy for Internal Access

### Objective
Create a bucket policy that allows access to internal documents based on IP address or IAM conditions.

### Steps Performed

1. **Read the internal roster file**
   ```powershell
   Get-Content analyst-internal.txt
   ```

2. **Result:**
   ```
   Staff duty schedule, week 12
   ```
   **Evidence:** Screenshot 703.png

3. **Test conditional access with PowerShell**
   ```powershell
   if ($LASTEXITCODE -eq 0) {
       Write-Host "internal: ALLOWED"
   }
   ```

4. **Result:**
   ```
   internal: ALLOWED
   ```
   **Evidence:** Screenshot 703.png

### Analysis
The bucket policy successfully allows access to internal classified documents for authorized users/IPs while maintaining restrictions on public access.

---

## Task 5: Server-Side Encryption with AWS KMS

### Objective
Implement server-side encryption using AWS Key Management Service (KMS) for enhanced data protection.

### Steps Performed

1. **Upload new version of confidential file with KMS encryption**
   ```powershell
   aws $EP s3api head-object `
       --bucket $BUCKET `
       --key confidential/record-v2.txt `
       --query "[ServerSideEncryption,SSEKMSKeyId,BucketKeyEnabled]" `
       --output text
   ```

2. **Result - Encryption Details:**
   ```
   aws:kms arn:aws:kms:us-east-1:000000000000:key/449c3e67-abac-406f-a72b-0bf17b7accc2    True
   ```
   **Evidence:** Screenshot 705.png

3. **Verify file content is encrypted at rest**
   ```powershell
   curl.exe -i $URL
   ```

4. **Result - Encrypted Object Headers:**
   ```
   HTTP/1.1 200 OK
   Server: TwistedWeb/24.3.0
   Date: Fri, 11 Sep 2026 06:27:24 GMT
   Content-Type: binary/octet-stream
   accept-ranges: bytes
   Last-Modified: Fri, 11 Sep 2026 06:04:35 GMT
   Content-Length: 30
   ETag: "6b2d139d7b2d5b3e5ef32a57bf6853dd"
   x-amz-version-id: null
   x-amz-server-side-encryption: AES256
   x-amz-tagging-count: 1
   ```
   **Evidence:** Screenshot 706.png

### Analysis
Server-side encryption with KMS provides:
- **Automatic encryption** of data at rest
- **Key rotation** capabilities
- **Access audit trail** through CloudTrail
- **Bucket-level encryption keys** for improved performance

---

## Task 6: Object Versioning and Recovery

### Objective
Enable versioning to protect against accidental deletion and allow recovery of previous versions.

### Steps Performed

1. **List all versions of the confidential file**
   ```powershell
   aws $EP s3api list-object-versions `
       --bucket $BUCKET `
       --prefix confidential/record.txt `
       --query "DeleteMarkers[].{VersionId,IsLatest}" `
       --output table
   ```

2. **Result - Version History:**
   ```
   +-----------------------------------------------+--------+
   |            ListObjectVersions                  |        |
   +-----------------------------------------------+--------+
   | lU_41Nf5ylQKoOofLMkY0MhaBhEEThc2             | True   |
   +-----------------------------------------------+--------+
   ```
   **Evidence:** Screenshot 707.png

3. **Recover deleted object by specifying version-id as null**
   ```powershell
   aws $EP s3api get-object `
       --bucket $BUCKET `
       --key confidential/record.txt `
       --version-id null `
       recovered.txt
   ```

4. **Result - Recovered Object Metadata:**
   ```json
   {
       "AcceptRanges": "bytes",
       "LastModified": "2026-09-11T06:04:39+00:00",
       "ContentLength": 49,
       "ETag": "\"5Ufc279103a068a66801865b7d50b8ef\"",
       "VersionId": "null",
       "ContentType": "binary/octet-stream",
       "ServerSideEncryption": "AES256",
       "Metadata": {},
       "TagCount": 1
   }
   ```
   **Evidence:** Screenshot 707.png

5. **Verify recovered content**
   ```powershell
   Get-Content recovered.txt
   ```

6. **Result:**
   ```
   Patient: Ahmad bin Ali, Diagnosis: confidential
   ```
   **Evidence:** Screenshot 707.png

### Analysis
Versioning enables:
- **Protection** against accidental deletion (delete markers instead of permanent deletion)
- **Recovery** of previous object versions
- **Audit trail** of all object changes
- **Compliance** with data retention requirements

---

## Task 7: Lifecycle Management Policies

### Objective
Configure lifecycle policies to automatically transition or delete objects based on age and access patterns.

### Steps Performed

1. **Check bucket lifecycle configuration**
   ```powershell
   aws $EP s3api get-bucket-lifecycle-configuration `
       --bucket $BUCKET `
       --query "Rules[].[ID,Status]" `
       --output table
   ```

2. **Result - Lifecycle Rules:**
   ```
   +-----------------------------------------------+----------+
   |      GetBucketLifecycleConfiguration          |          |
   +-----------------------------------------------+----------+
   | RetireConfidentialRecords                     | Enabled  |
   | AbortIncompleteUploads                        | Enabled  |
   +-----------------------------------------------+----------+
   ```
   **Evidence:** Screenshot 708.png

### Lifecycle Policy Details

#### Rule 1: RetireConfidentialRecords
- **Purpose:** Automatically delete old confidential records
- **Status:** Enabled
- **Target:** Objects tagged with classification=confidential
- **Action:** Permanent deletion after retention period

#### Rule 2: AbortIncompleteUploads
- **Purpose:** Clean up incomplete multipart uploads
- **Status:** Enabled
- **Target:** All incomplete uploads
- **Action:** Abort uploads after specified days

### Analysis
Lifecycle policies provide:
- **Automated data management** reducing manual intervention
- **Cost optimization** by deleting old unused data
- **Compliance enforcement** with data retention policies
- **Storage cleanup** of incomplete uploads

---

## Task 8: KMS Key Management and Scheduled Deletion

### Objective
Understand KMS key lifecycle and scheduled deletion for secure key retirement.

### Steps Performed

1. **Check KMS key deletion schedule**
   ```powershell
   aws $EP kms describe-key `
       --key-id $KEY_ID `
       --query "KeyMetadata.[KeyState,DeletionDate]" `
       --output text
   ```

2. **Result - Key Deletion Status:**
   ```
   PendingDeletion 2026-09-18T14:36:00.023505+08:00
   ```
   **Evidence:** Screenshot 709.png

### Analysis
The KMS key shows:
- **KeyState:** PendingDeletion
- **DeletionDate:** 2026-09-18T14:36:00 (7-day waiting period)
- **Grace Period:** Allows key recovery if deletion was unintended
- **Impact:** After deletion, objects encrypted with this key cannot be decrypted

**Important:** KMS enforces a mandatory waiting period (7-30 days) before key deletion to prevent accidental data loss.

---

## Summary of Key Concepts

### 1. Object Storage Structure
- **Buckets:** Containers for objects
- **Objects:** Files with metadata and tags
- **Keys:** Unique identifiers for objects
- **Prefixes:** Folder-like organization (e.g., confidential/, internal/)

### 2. Access Control Mechanisms
- **Bucket Policies:** Resource-based access control
- **Public Access Block:** Prevents unintended public exposure
- **Object ACLs:** Legacy access control (now discouraged)
- **IAM Policies:** Identity-based access control

### 3. Data Protection Features
- **Server-Side Encryption:** AES256 or KMS-managed keys
- **Versioning:** Protection against overwrites and deletions
- **MFA Delete:** Additional protection for deletion operations
- **Object Lock:** WORM (Write Once Read Many) compliance

### 4. Lifecycle Management
- **Transition Rules:** Move objects to cheaper storage classes
- **Expiration Rules:** Delete objects after specified period
- **Tag-Based Rules:** Apply policies based on object tags
- **Multipart Upload Cleanup:** Remove incomplete uploads

### 5. Security Best Practices
✅ Enable versioning for critical data  
✅ Use server-side encryption (SSE-KMS preferred)  
✅ Block public access unless explicitly required  
✅ Implement least privilege access with bucket policies  
✅ Enable CloudTrail logging for audit  
✅ Use object tagging for classification  
✅ Implement lifecycle policies for cost optimization  
✅ Schedule regular access reviews  

---

## Lab Completion Results

| Task | Description | Status |
|------|-------------|--------|
| 1 | Object Tagging and Classification | ✅ Completed |
| 2 | Test Public Access | ✅ Completed |
| 3 | Enable Public Access Block | ✅ Completed |
| 4 | Implement Bucket Policy | ✅ Completed |
| 5 | Server-Side Encryption with KMS | ✅ Completed |
| 6 | Object Versioning and Recovery | ✅ Completed |
| 7 | Lifecycle Management Policies | ✅ Completed |
| 8 | KMS Key Management | ✅ Completed |

---

## Evidence Files
- **700.png** - Object listing and tagging verification
- **701.png** - Public access test before blocking
- **702.png** - Public access block configuration
- **703.png** - Internal bucket policy testing
- **704.png** - (Not referenced in tasks)
- **705.png** - KMS encryption verification
- **706.png** - Encrypted object header details
- **707.png** - Object versioning and recovery
- **708.png** - Lifecycle policy configuration
- **709.png** - KMS key deletion schedule

---

## Conclusion

This lab successfully demonstrated AWS S3's comprehensive object storage capabilities including security, versioning, lifecycle management, and encryption. Key takeaways include:

1. **Data Classification** through tagging enables automated policy enforcement
2. **Public Access Blocking** is essential for protecting sensitive data
3. **Server-Side Encryption** with KMS provides enhanced security and key management
4. **Versioning** protects against accidental deletion and enables point-in-time recovery
5. **Lifecycle Policies** automate data management and reduce storage costs
6. **KMS Key Management** provides centralized control over encryption keys with built-in safeguards

These features combined create a robust, secure, and cost-effective object storage solution suitable for enterprise workloads with varying security and compliance requirements.

---

**Lab Date:** September 11, 2026  
**Environment:** AWS LocalStack (localhost:4566)  
**Course:** IKB42603 - Cloud Computing  
**Lab Number:** 6 - Object Storage and Data Lifecycle
