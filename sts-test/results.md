#  STS Role Assumption Lab – `sts-test/`

##  Objective

Demonstrate how **AWS STS (Security Token Service)** works by allowing a user (`audit-user`) to assume a role (`AuditAccessRole`) that grants limited EC2 read-only access.

---

## IAM Setup

| Entity        | Type     | Purpose                                        |
|---------------|----------|------------------------------------------------|
| `audit-user`  | IAM User | Cannot access EC2 directly                    |
| `AuditAccessRole` | IAM Role | Grants `ec2:DescribeInstances` when assumed |

---

## Steps Performed

### Step 1: Created `audit-user`
- No direct EC2 permissions
- Verified access denied via CloudShell

`sts-test/screenshots/2 audit user doesnt have access to view EC2 instances.png`

---

### Step 2: Created IAM Role: `AuditAccessRole`
- Trust policy allowed **`audit-user`** to assume this role
- Attached inline policy with only: `ec2:DescribeInstances`

📄 `trust-policy.json`  
📄 `inline-ec2-describe-policy.json`

---

### Step 3: Verified `audit-user` cannot access EC2 directly
- Ran `aws ec2 describe-instances` in CloudShell → ❌ AccessDenied

---

### Step 4: Used STS to Assume the Role
- Ran `aws sts assume-role` command
- Exported temporary credentials into environment variables
- Re-ran `aws ec2 describe-instances` → ✅ Worked!

📸 `sts-test/screenshots/7 logged in with access keys.png`  
📸 `sts-test/screenshots/9 getting caller ID for session.png`

---

## 📜 Policy Breakdown

### Trust Policy (`trust-policy.json`)
Allows `audit-user` to assume the role:

---

## Role Policy (ec2-inline-policy.json)

### Allows EC2 Describe only

---
## CLI Commands Used

### See assume-role-commands.md

---
## Key Concepts

- STS allows temporary credential access via assume-role

- audit-user had zero EC2 access directly, but could temporarily access EC2 via the assumed role

- Roles are better than direct permissions for cross-account or scoped access

- Great for security isolation and temporary access use cases

---

## Related Files

- trust-policy.json – Role trust relationship

- ec2-inline-policy.json – Permissions granted to the role

- assume-role-commands.md – AWS CLI used for assuming role and testing

- screenshots/ – All access proofshots
