#  AWS IAM Lab – Simulating Role-Based Access in the Cloud

##  What This Is About

This is a small lab I built to get hands-on with IAM (Identity and Access Management) on AWS. The goal was to simulate a real-world setup where different users have different levels of access — and then test what happens when you try to go beyond your permissions.

I’ve always read that IAM is a core pillar of cloud security, but doing it myself made it click better than any course or video.

---

##  Users and Groups I Created

| User           | Group      | Permissions                     |
|----------------|------------|----------------------------------|
| `dev-user`     | Developers | LambdaExecute + CloudWatch Logs |
| `analyst-user` | Analysts   | CloudWatch ReadOnly             |
| `admin-user`   | Admins     | AdministratorAccess             |

Each user was added to their group, and the group had its own policy attached.

---

##  What I Tested Initially

- Logged in as `admin-user` → was able to create a new IAM user ✅  
- Logged in as `analyst-user` → tried to create a CloudWatch log group → **got an error** ❌  
- Tried viewing logs as `analyst-user` → allowed ✅  
- Tried accessing EC2 → denied as expected ❌  

I’ve added screenshots of each step in the `/screenshots` folder.

---

##  Lambda Function + IAM Role Lab

The second part of this project focused on deploying a Lambda function as `dev-user`, while respecting IAM boundaries.

### What Happened:
- `dev-user` tried creating a Lambda function → Denied due to missing `iam:CreateRole`
- Then failed again because it couldn’t `iam:ListRoles`
- Even after choosing an existing role, `lambda:CreateFunction` was still denied

### Fixes:
- Created a new IAM role manually as `admin-user`
- Added minimal required permissions to the Developers group (`iam:ListRoles`, `iam:PassRole`, `lambda:CreateFunction`)
- Eventually got the Lambda deployed and running as `dev-user`
- Logs were visible in CloudWatch

📄 Full write-up and YAML summary in [`lambda-test/results.md`](lambda-test/results.md)  
📸 Screenshots in [`lambda-test/screenshots/`](lambda-test/screenshots/)  
🧠 Python function used in [`lambda-test/demo-function-code.py`](lambda-test/demo-function-code.py)

---

### ✅ `mfa-test/` – Enforcing MFA on High-Privilege Users  
Demonstrates how to enforce **Multi-Factor Authentication (MFA)** for a sensitive IAM user (`admin-user`) using a conditional deny policy.

- Created custom policy using `aws:MultiFactorAuthPresent`
- Attached it directly to `admin-user`
- Verified that service access was denied until MFA was configured
- Set up virtual MFA using Google Authenticator
- After login with MFA, access was restored to IAM, Lambda, EC2, etc.

📄 [Full write-up](mfa-test/results.md)  
📸 [Screenshots](mfa-test/screenshots/)  
📜 [Policy JSON](mfa-test/mfa-enforce-policy.json)

---

### ✅ `s3-test/` – IAM + S3 Bucket Policy Enforcement

Demonstrates how to restrict S3 access using both IAM and bucket policies.

- Initially, `dev-user` had no S3 permissions → AccessDenied
- Created a custom IAM policy scoped to only `demo-aws-bucket121`
- Allowed `PutObject`, `GetObject`, and `ListBucket`, but denied deletes
- Added an S3 bucket policy to **deny unencrypted (non-HTTPS) uploads**
- Verified the bucket policy works and CLI defaults to secure uploads

📄 [Full write-up](s3-test/results.md)  
📸 [Screenshots](s3-test/screenshots/)  
📜 [Policy JSONs](s3-test/s3-access-policy.json & s3-test/bucket-policy.json)

---
### ✅ `custom-policy-test/` – Handwritten Least Privilege IAM Policy

This module demonstrates how to **manually write a custom IAM policy** that grants just enough access for a realistic EC2-read use case.

- `analyst-user` started with **no EC2 access**  
- Created a policy allowing only:
  - `ec2:DescribeInstances` – view instance-level metadata  
  - `ec2:DescribeTags` – view associated tags  
  - `ec2:DescribeImages` – view AMIs used to launch instances  
- Attached policy to `Analysts` group  
- Verified:
  - ✅ Can list EC2 instances  
  - ✅ Can view metadata and AMI details  
  - ❌ Cannot launch, terminate, or modify instances  
- Tested both in Console and AWS CloudShell

📄 [Full write-up](custom-policy-test/results.md)  
📸 [Screenshots](custom-policy-test/Screenshots/)  
📜 [Policy JSON](custom-policy-test/analyst-ec2-read-policy.json)

---

---
### ✅ `sts-test`/ – Role Assumption Using STS (Security Token Service)

This module demonstrates how IAM roles and AWS STS work together to grant temporary, limited access to a user who otherwise has no direct permission to perform the action.

- Created audit-user with no EC2 access

- Created a new role: AuditAccessRole

- Trust policy allows only audit-user to assume it

- Inline policy grants: ec2:DescribeInstances only

- As audit-user, tested EC2 describe → ❌ AccessDenied

- Then ran aws sts assume-role to assume the AuditAccessRole

- Exported temporary credentials into session

- Ran EC2 describe again → ✅ Success!

This showcases least privilege, temporary credentials, and the separation of identity from permissions using IAM roles.

📄 Full write-up(sts-test/results.md)
📸 Screenshots (sts-test/screenshots)
📜 Trust Policy (sts-test/trust-policy.json)
📜 Inline Role Policy (sts-test/ec2-inline-policy.json)
📄 CLI Commands Used (sts-test/assume-role-commands.md)

---

---
### 💡 Key Takeaways

- IAM is **deny by default** — all access must be explicitly granted  
- Even for “read-only” roles, **write your own policies** instead of relying on AWS managed ones  
- **`ec2:DescribeInstances`**, `DescribeTags`, and `DescribeImages` can be useful for analysts and dashboards  
- AWS CloudShell is a lightweight tool to validate permissions quickly  
- MFA should be enforced using a **conditional deny approach**  
- IAM is for user/group permissions; **S3 bucket policies** enforce transport conditions and public access control
- Roles decouple identity and permissions – they can be assumed by users temporarily, allowing scoped and time-bound access via STS
- Trust policies define who can assume a role; permissions policies define what that role can do


---

## 🧩 Planned Test Modules

### 🧩 Planned Test Modules (Updated)

| Module Name              | Description                                                             | Status   |
|--------------------------|-------------------------------------------------------------------------|----------|
| `lambda-test/`           | IAM roles and Lambda deployment with least privilege                   | ✅ Done  |
| `mfa-test/`              | Enforce MFA before allowing console/API access for high-privilege users | ✅ Done  |
| `s3-test/`               | Control S3 access via IAM + bucket policies                             | ✅ Done  |
| `custom-policy-test/`    | Write scoped IAM policies manually                                      | ✅ Done  |
| `sts-test/`              | Use STS to assume roles with temporary credentials                      | ✅ Done  |
| `cloudtrail-test/`       | Track IAM activity using CloudTrail logs                                | 🔜 Planned |
    

---

---



##  Built By

This project is part of my journey to transition from beginner to **Cloud Security Engineer**. I’ll be adding new modules, writing blog posts, and sharing insights along the way.

Stay tuned — and feel free to fork, learn, or contribute 🔐
