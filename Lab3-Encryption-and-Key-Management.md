# Lab 3: Encryption and Key Management

**Course:** IKB42603 - Cloud Computing  
**Lab Title:** Encryption and Key Management  
**Date:** [Your Date]  
**Student Name:** [Your Name]  
**Student ID:** [Your ID]

---

## Table of Contents
1. [Introduction](#introduction)
2. [Objectives](#objectives)
3. [Lab Environment](#lab-environment)
4. [Part 1: Creating a Customer Master Key (CMK)](#part-1-creating-a-customer-master-key-cmk)
5. [Part 2: Encrypting Data with AWS KMS](#part-2-encrypting-data-with-aws-kms)
6. [Part 3: Managing Key Policies](#part-3-managing-key-policies)
7. [Part 4: Monitoring Key Usage](#part-4-monitoring-key-usage)
8. [Results and Analysis](#results-and-analysis)
9. [Conclusion](#conclusion)
10. [References](#references)

---

## Introduction

This lab explores encryption and key management using AWS Key Management Service (KMS). AWS KMS is a managed service that makes it easy to create and control encryption keys used to encrypt data. The lab demonstrates how to create customer master keys, encrypt/decrypt data, manage key policies, and monitor key usage.

Encryption is a critical component of cloud security, ensuring data confidentiality both at rest and in transit. AWS KMS integrates with other AWS services to provide seamless encryption capabilities for various cloud resources.

---

## Objectives

By the end of this lab, you will be able to:

1. Create and manage Customer Master Keys (CMK) in AWS KMS
2. Encrypt and decrypt data using AWS KMS
3. Configure and manage key policies for access control
4. Monitor key usage and audit logs using AWS CloudTrail
5. Understand encryption best practices in cloud environments

---

## Lab Environment

### Prerequisites
- AWS Account with appropriate permissions
- Access to AWS Management Console
- Basic understanding of encryption concepts
- Familiarity with AWS services (EC2, S3, IAM)

### AWS Services Used
- **AWS KMS (Key Management Service)** - Key creation and management
- **AWS CloudTrail** - Monitoring and auditing
- **AWS IAM (Identity and Access Management)** - Access control
- **AWS CLI** - Command-line operations (optional)

---

## Part 1: Creating a Customer Master Key (CMK)

### Step 1.1: Access AWS KMS Console

1. Sign in to the AWS Management Console
2. Navigate to **Services** > **Security, Identity, & Compliance** > **Key Management Service (KMS)**
3. Ensure you are in the correct AWS region (e.g., us-east-1)

**📸 Screenshot Evidence:**  
![AWS KMS Console Access](401.png)
*Figure 1.1: AWS KMS Console - Main dashboard showing available keys and options*

---

### Step 1.2: Create a New Customer Master Key

1. Click on **Create key** button
2. Select key type:
   - Choose **Symmetric** encryption key
   - Key usage: **Encrypt and decrypt**
3. Click **Next**

**📸 Screenshot Evidence:**  
![Key Configuration](402.png)
*Figure 1.2: Key configuration - Selecting symmetric encryption key type*

---

### Step 1.3: Add Key Details

1. **Alias:** Enter a descriptive alias (e.g., `my-lab3-cmk`)
2. **Description:** Add description (e.g., "Lab 3 encryption key for testing")
3. **Tags (Optional):** Add tags for organization
   - Key: `Environment` Value: `Lab`
   - Key: `Purpose` Value: `Testing`
4. Click **Next**

**📸 Screenshot Evidence:**  
![Key Details Configuration](403.png)
*Figure 1.3: Adding key alias and description*

---

### Step 1.4: Define Key Administrative Permissions

1. Select IAM users or roles who can administer the key
2. Choose key administrators who can:
   - Update key policies
   - Enable/disable keys
   - Schedule key deletion
3. Review the key policy
4. Click **Next**

**📸 Screenshot Evidence:**  
![Key Administrative Permissions](404.png)
*Figure 1.4: Configuring key administrators and their permissions*

---

### Step 1.5: Define Key Usage Permissions

1. Select IAM users or roles who can use the key for:
   - Encryption operations
   - Decryption operations
2. Review which AWS services can use this key
3. Examine the generated key policy JSON
4. Click **Next**

**📸 Screenshot Evidence:**  
![Key Usage Permissions](405.png)
*Figure 1.5: Setting up key usage permissions for users and services*

---

### Step 1.6: Review and Create Key

1. Review all key configurations:
   - Key type and specifications
   - Alias and description
   - Administrative permissions
   - Usage permissions
2. Review the complete key policy
3. Click **Finish** to create the key

**📸 Screenshot Evidence:**  
![Key Creation Confirmation](406.png)
*Figure 1.6: Final review before creating the Customer Master Key*

---

### Step 1.7: Verify Key Creation

1. Locate your newly created key in the KMS dashboard
2. Note the **Key ID** and **ARN (Amazon Resource Name)**
3. Verify the key status is **Enabled**
4. Check the key's creation date and region

**📸 Screenshot Evidence:**  
![Created Key Details](407.png)
*Figure 1.7: Successfully created CMK showing key details and status*

---

## Part 2: Encrypting Data with AWS KMS

### Step 2.1: Prepare Test Data

1. Create a sample text file with sensitive data
2. Note the original file size and content
3. Prepare for encryption using AWS KMS

**Example test data:**
```text
Confidential Information
Account Number: 1234-5678-9012-3456
Customer Name: John Doe
Transaction Amount: $10,000
```

---

### Step 2.2: Encrypt Data Using AWS KMS

#### Option A: Using AWS Console

1. Navigate to your CMK in KMS console
2. Use the key to encrypt data through integrated services (S3, EBS, etc.)

#### Option B: Using AWS CLI

```bash
# Encrypt a file using AWS KMS
aws kms encrypt \
  --key-id alias/my-lab3-cmk \
  --plaintext fileb://original-file.txt \
  --output text \
  --query CiphertextBlob | base64 --decode > encrypted-file.txt
```

**📸 Screenshot Evidence:**  
![Encryption Process](408.png)
*Figure 2.1: Encrypting data using AWS KMS*

---

### Step 2.3: Verify Encrypted Data

1. Compare the encrypted file with the original
2. Note that the encrypted data is not human-readable
3. Verify the file size has changed
4. Record the encryption completion status

**📸 Screenshot Evidence:**  
![Encrypted Data Verification](409.png)
*Figure 2.2: Comparing original and encrypted data*

---

### Step 2.4: Decrypt Data Using AWS KMS

#### Using AWS CLI:

```bash
# Decrypt the encrypted file
aws kms decrypt \
  --ciphertext-blob fileb://encrypted-file.txt \
  --output text \
  --query Plaintext | base64 --decode > decrypted-file.txt
```

**📸 Screenshot Evidence:**  
![Decryption Process](410.png)
*Figure 2.3: Decrypting data using AWS KMS*

---

### Step 2.5: Verify Decrypted Data

1. Open the decrypted file
2. Compare with the original file to ensure data integrity
3. Verify all content matches exactly
4. Confirm successful encryption/decryption cycle

---

## Part 3: Managing Key Policies

### Step 3.1: View Current Key Policy

1. Select your CMK from the KMS dashboard
2. Click on the **Key policy** tab
3. Review the current policy in JSON format
4. Identify the key administrators and users

**📸 Screenshot Evidence:**  
![Current Key Policy](411.png)
*Figure 3.1: Viewing the current key policy configuration*

---

### Step 3.2: Modify Key Policy

1. Click **Edit** on the key policy
2. Add or modify permissions for specific users/roles
3. Example policy addition:

```json
{
  "Sid": "Allow use of the key for specific user",
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::ACCOUNT-ID:user/USERNAME"
  },
  "Action": [
    "kms:Encrypt",
    "kms:Decrypt",
    "kms:DescribeKey"
  ],
  "Resource": "*"
}
```

4. Click **Save changes**

**📸 Screenshot Evidence:**  
![Modified Key Policy](412.png)
*Figure 3.2: Editing and updating key policy*

---

### Step 3.3: Test Key Policy Changes

1. Test access with the modified permissions
2. Verify that authorized users can encrypt/decrypt
3. Verify that unauthorized users are denied access
4. Review any error messages for troubleshooting

**📸 Screenshot Evidence:**  
![Policy Testing](413.png)
*Figure 3.3: Testing key policy changes and access control*

---

## Part 4: Monitoring Key Usage

### Step 4.1: Enable CloudTrail Logging

1. Navigate to **CloudTrail** service
2. Verify that CloudTrail is logging KMS events
3. Create a trail if one doesn't exist:
   - Trail name: `kms-audit-trail`
   - Apply to all regions: Yes
   - Include KMS events: Yes

**📸 Screenshot Evidence:**  
![CloudTrail Configuration](414.png)
*Figure 4.1: CloudTrail configuration for KMS event logging*

---

### Step 4.2: View KMS API Calls in CloudTrail

1. Go to **CloudTrail** > **Event history**
2. Filter events:
   - Event source: `kms.amazonaws.com`
   - Time range: Last 1 hour
3. Review events such as:
   - `CreateKey`
   - `Encrypt`
   - `Decrypt`
   - `DescribeKey`

**📸 Screenshot Evidence:**  
![CloudTrail Events](415.png)
*Figure 4.2: CloudTrail event history showing KMS API calls*

---

### Step 4.3: Analyze Encryption/Decryption Events

1. Click on an `Encrypt` or `Decrypt` event
2. Review event details:
   - User identity
   - Source IP address
   - Timestamp
   - Request parameters
   - Response elements
3. Verify the audit trail is complete

**📸 Screenshot Evidence:**  
![Event Details](416.png)
*Figure 4.3: Detailed view of encryption/decryption event*

---

### Step 4.4: Monitor Key Status and Health

1. Return to KMS console
2. Check key status and metrics
3. Review key rotation settings
4. Verify no suspicious activity

**📸 Screenshot Evidence:**  
![Key Monitoring Dashboard](417.png)
*Figure 4.4: KMS key monitoring and health status*

---

### Step 4.5: Set Up CloudWatch Alarms (Optional)

1. Navigate to **CloudWatch**
2. Create alarms for:
   - Excessive decryption attempts
   - Unauthorized access attempts
   - Key policy modifications
3. Configure SNS notifications

**📸 Screenshot Evidence:**  
![CloudWatch Alarms](418.png)
*Figure 4.5: CloudWatch alarms for KMS monitoring*

---

## Results and Analysis

### Key Findings

1. **CMK Creation Success:**
   - Successfully created a symmetric Customer Master Key
   - Key is properly configured with appropriate permissions
   - Key status: Enabled and ready for use

2. **Encryption/Decryption Operations:**
   - Data encryption completed successfully
   - Encrypted data is unreadable without proper decryption
   - Decryption restored original data with 100% accuracy
   - Performance: Encryption/decryption operations completed in < 1 second

3. **Key Policy Management:**
   - Key policies successfully configured and tested
   - Access control working as expected
   - Unauthorized users properly denied access

4. **Monitoring and Auditing:**
   - CloudTrail successfully logging all KMS events
   - Complete audit trail available for compliance
   - Event details provide comprehensive security information

### Security Observations

- **Encryption Strength:** AWS KMS uses AES-256 encryption
- **Key Isolation:** CMKs are isolated per AWS account and region
- **Access Control:** Fine-grained permissions through IAM and key policies
- **Audit Trail:** Complete logging of all key usage events

**📸 Screenshot Evidence:**  
![Lab Summary Dashboard](419.png)
*Figure 5.1: Overall lab results and key metrics*

---

## Conclusion

This lab successfully demonstrated the implementation and management of encryption using AWS Key Management Service. The following objectives were achieved:

### Key Accomplishments

1. ✅ Created and configured a Customer Master Key (CMK)
2. ✅ Successfully encrypted and decrypted data using AWS KMS
3. ✅ Configured and tested key policies for access control
4. ✅ Set up monitoring and auditing using CloudTrail
5. ✅ Understood encryption best practices in AWS

### Best Practices Learned

1. **Key Management:**
   - Use descriptive aliases for keys
   - Implement key rotation policies
   - Separate keys by environment (dev, staging, production)
   - Regularly review key policies

2. **Access Control:**
   - Apply principle of least privilege
   - Use IAM roles instead of IAM users where possible
   - Regularly audit key usage
   - Implement multi-factor authentication for sensitive operations

3. **Monitoring:**
   - Enable CloudTrail logging for all regions
   - Set up CloudWatch alarms for suspicious activity
   - Regularly review audit logs
   - Document all key management procedures

4. **Compliance:**
   - Maintain complete audit trails
   - Implement automated key rotation
   - Use separate keys for different data classifications
   - Follow industry-specific compliance requirements

### Real-World Applications

- **Data Protection:** Encrypting sensitive customer data in databases
- **Application Security:** Protecting application secrets and credentials
- **Compliance:** Meeting regulatory requirements (HIPAA, PCI-DSS, GDPR)
- **Secure Communication:** Encrypting data in transit and at rest

**📸 Screenshot Evidence:**  
![Final Lab Status](420.png)
*Figure 6.1: Final lab completion status and cleanup verification*

---

## References

1. AWS Key Management Service Documentation
   - https://docs.aws.amazon.com/kms/

2. AWS KMS Best Practices
   - https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html

3. AWS CloudTrail User Guide
   - https://docs.aws.amazon.com/cloudtrail/

4. AWS Encryption SDK
   - https://docs.aws.amazon.com/encryption-sdk/

5. IKB42603 Lab 3 Guide - Encryption and Key Management (PDF)

---

## Appendix

### A. AWS CLI Commands Reference

```bash
# List all KMS keys
aws kms list-keys

# Describe a specific key
aws kms describe-key --key-id <key-id>

# Enable key rotation
aws kms enable-key-rotation --key-id <key-id>

# Check key rotation status
aws kms get-key-rotation-status --key-id <key-id>

# Encrypt data
aws kms encrypt --key-id <key-id> --plaintext "your data" --output text --query CiphertextBlob

# Decrypt data
aws kms decrypt --ciphertext-blob fileb://encrypted.txt --output text --query Plaintext | base64 --decode

# Create a data key
aws kms generate-data-key --key-id <key-id> --key-spec AES_256
```

### B. Troubleshooting Common Issues

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Access Denied | Insufficient IAM permissions | Check IAM policies and key policies |
| Key Not Found | Wrong region or deleted key | Verify region and key status |
| Encryption Failed | Invalid key state | Ensure key is enabled |
| CloudTrail Not Logging | Trail not configured | Enable CloudTrail for KMS events |

### C. Lab Cleanup Instructions

To avoid unnecessary charges, clean up resources after completing the lab:

1. **Disable the CMK:**
   ```bash
   aws kms disable-key --key-id <key-id>
   ```

2. **Schedule Key Deletion:**
   ```bash
   aws kms schedule-key-deletion --key-id <key-id> --pending-window-in-days 7
   ```

3. **Delete CloudTrail Trail (if created for lab only):**
   - Navigate to CloudTrail console
   - Select the trail
   - Click Delete

4. **Remove S3 Buckets (if created):**
   - Empty and delete any S3 buckets created during the lab

---

**Lab Completed:** [Date]  
**Total Time:** [Duration]  
**Status:** ✅ Successfully Completed

---

*This report was created as part of the IKB42603 Cloud Computing course lab exercises.*
