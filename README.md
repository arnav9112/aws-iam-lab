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

##  Takeaways

- IAM follows the **principle of least privilege** — everything is denied unless explicitly allowed  
- Real devs often don’t have permission to create roles — roles should be pre-created by admins  
- You can only learn these things by trying, breaking, and fixing  
- Even common permissions like `iam:PassRole` are easy to miss

---

##  What's Next

This repo will be updated regularly with new IAM-related labs, including:
- MFA setup and policy enforcement
- S3 bucket access with granular permissions
- Custom IAM policies from scratch
- Role chaining and federated identity
- CloudTrail monitoring for audit analysis

---

##  Who This Is For

- Anyone preparing for **AWS SAA / Security+ / Cybersec roles**
- Developers or beginners trying to get comfortable with IAM
- Cloud security aspirants who want **real projects** in their portfolio
- Anyone tired of just reading and wants to **build and break** things

---

##  Built By

This project is part of my journey to transition from beginner to **Cloud Security Engineer**, targeting roles at top cybersecurity companies like Zscaler. I’ll be adding new modules, writing blog posts, and sharing insights along the way.

Stay tuned — and feel free to fork, learn, or contribute 🔐