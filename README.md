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

## 💡 Key Takeaways So Far

- IAM is **deny by default** — every action must be explicitly allowed
- Even basic operations like creating Lambda require chained permissions
- **`iam:PassRole`**, **`iam:ListRoles`**, and **`lambda:CreateFunction`** are easy to miss but critical
- MFA enforcement is best handled via a **conditional deny policy**
- You won’t truly understand IAM until you simulate and debug it yourself


---

## 🧩 Planned Test Modules

| Module Name              | Description                                                             | Status |
|--------------------------|-------------------------------------------------------------------------|--------|
| `lambda-test/`           | IAM roles and Lambda deployment with least privilege                   | ✅ Done |
| `mfa-test/`              | Enforce MFA before allowing console/API access for high-privilege users | ✅ Done |
| `s3-test/`               | Control S3 access via IAM + bucket policies                             | ⏳ Next |
| `custom-policy-test/`    | Write scoped IAM policies manually                                      | 🔜     |
| `sts-test/`              | Use STS to assume roles with temporary credentials                     | 🔜     |
| `cloudtrail-test/`       | Track IAM activity using CloudTrail logs                                | 🔜     |

---

---



##  Built By
This project is part of my journey to transition from beginner to **Cloud Security Engineer**. I’ll be adding new modules, writing blog posts, and sharing insights along the way.

Stay tuned — and feel free to fork, learn, or contribute 🔐