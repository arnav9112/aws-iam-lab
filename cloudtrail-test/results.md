# ✅ cloudtrail-test – Tracking IAM Activity with Logs

This module explores how **AWS CloudTrail** helps monitor IAM activity like role assumptions, login attempts, policy changes, and MFA deactivation. It's the final piece in understanding how to **audit** and **verify** what’s happening in your AWS environment — especially for security-sensitive operations.

---

## 🔧 Setup Summary

- **Trail Name:** `iam-trail-demo`
- **Trail Type:** Management events only (no data or S3 events)
- **S3 Bucket:** `iam-logs-demo`
- **Encryption:** Default SSE-S3 (KMS tested as well — see note below)
- **Region:** us-east-1
- **Cost Optimization:** Only management events were logged to reduce storage costs
- **Cleanup:** Trail and S3 bucket deleted after test

---

## 🧪 Simulated IAM Activities

| # | Activity                                | IAM User       | CloudTrail Event Name         | Purpose                                               |
|---|------------------------------------------|----------------|-------------------------------|-------------------------------------------------------|
| 1 | Role assumed using STS                   | `audit-user`   | `AssumeRole`                  | Validates temporary delegation using STS              |
| 2 | Attach policy to a user                  | `admin-user`   | `AttachUserPolicy`            | Tracks permission escalation attempts                 |
| 3 | Detach policy from a user                | `admin-user`   | `DetachUserPolicy`            | Tracks privilege removal or changes                   |
| 4 | Successful login                         | `dev-user`     | `ConsoleLogin`                | Validates interactive IAM usage                       |
| 5 | Failed login attempt                     | invalid creds  | `ConsoleLogin` (with failure) | Detects brute-force or password-guessing attempts     |
| 6 | MFA device deactivated                   | `admin-user`   | `DeactivateMFADevice`         | Monitors security posture degradation                 |
| 7 | IAM user creation                        | `admin-user`   | `CreateUser`                  | Detects provisioning of new identities                |
| 8 | IAM user deletion                        | `admin-user`   | `DeleteUser`                  | Validates cleanup, tracks offboarding                 |

---

## 🔍 Log Analysis and Findings

### 1️⃣ STS: AssumeRole Event

- **Command Run:**
  ```bash
  aws sts assume-role \
    --role-arn arn:aws:iam::<account-id>:role/analyst-assumable-role \
    --role-session-name demoSession \
    --profile audit-user
   ```

    Event Name: AssumeRole

    Captured In: CloudTrail > Event history

    Details: Shows audit-user assuming analyst-assumable-role with session metadata

    Screenshot: (<screenshots/3 assume role using sts.png>)

### 2️⃣ IAM Policy Attach/Detach
- Action: Attached & detached AmazonS3ReadOnlyAccess from analyst-user

- Event Names: AttachUserPolicy, DetachUserPolicy

- Captured In: CloudTrail > Filter by Event Source: iam.amazonaws.com

- Screenshot: (<screenshots/5 detaching S3 policy from audit user.png>)

### 3️⃣ IAM Console Logins
- Successful Login

    User: audit-user

    Event Name: ConsoleLogin

    Outcome: Success

    Screenshot: (<screenshots/6 logged in as audit-user for a console sign in log.png>)

- Failed Login Attempt

    User: analyst-user

    Event Name: ConsoleLogin

    Outcome: Failure – incorrect password

    Screenshot: (<screenshots/7 failed login attempt.png>)

### 4️⃣ MFA Device Deactivation
- User: admin-user

- Event Name: DeactivateMFADevice

- Steps:

    Navigated to IAM Console → Users → admin-user → Security Credentials

    Deactivated virtual MFA 

    Screenshot: (<screenshots/8 removing MFA for admin.png>)

### 5️⃣ IAM User Create/Delete
- User Created: demo-user

- User Deleted: demo-user

- Event Names: CreateUser, DeleteUser

- Screenshot: (<screenshots/9 creating and deleting demo-user.png>)

---
## Cleanup Performed
- Deleted CloudTrail trail (iam-trail-demo)

- Deleted associated S3 bucket (iam-logs-demo)

- Removed any temporary users or roles created for testing

---

##  Key Takeaways
- CloudTrail logs all API activity, including login attempts, policy changes, and STS usage

- Events are near real-time (typically <15 minutes delay)

- Every IAM change is traceable — useful for audit and incident response

- CloudTrail can store logs in S3, integrate with Athena or CloudWatch Logs for deeper analysis

- Failed logins and MFA deactivation are critical alerts in real-world environments

- CloudTrail should be enabled by default in all production accounts



    