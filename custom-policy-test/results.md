# Custom IAM Policy Test – Describe-Only EC2 Access for Analyst

## 🧠 Objective

This lab demonstrates how to manually create a custom IAM policy based on the **least privilege** principle. The use-case was to:

- Allow `analyst-user` to view EC2 instance metadata
- Prevent launching, terminating, or modifying EC2 instances
- Test and validate this access using both the AWS Console and CLI (via CloudShell)

---

## 🔧 Initial Setup

**User**: `analyst-user`  
**Group**: `Analysts`  
**Initial Permissions**: None related to EC2

---

## ❌ Step 1: No EC2 Access

- Logged in as `analyst-user`
- Navigated to EC2 Dashboard
- Immediately received an **Access Denied** message  
📸 Screenshot: `Screenshots/1 analyst cant see ec2 instances.png`

---

## 🛠️ Step 2: Created and Attached Custom Policy

- Wrote a custom policy: [`analyst-ec2-read-policy.json`](./analyst-ec2-read-policy.json)
- Attached it to the `Analysts` group
- This policy grants access to only:
  - `ec2:DescribeInstances`
  - `ec2:DescribeTags`
  - `ec2:DescribeImages`

📸 Screenshot: `Screenshots/2 admin made a custom policy for analyst to see relevant ec2 data.png`

---

## ✅ Step 3: Permissions Behavior After Policy

| Action                             | Result       |
|------------------------------------|--------------|
| View list of EC2 instances         | ✅ Allowed   |
| View instance tags                 | ✅ Allowed   |
| View available AMIs                | ✅ Allowed   |
| Launch new instance                | ❌ Denied    |
| Terminate instance                 | ❌ Denied    |
| Modify instance                    | ❌ Denied    |

📸 Screenshots:  
- `Screenshots/4 analyst user allowed to access instances post policy.png`  
- `Screenshots/6 analyst not authorised to launch an instance.png`  
- `Screenshots/7 analyst user could not terminate an instance as well.png`

---

## 🖥️ Step 4: Verified in CloudShell

- Gave `analyst-user` access to AWS CloudShell by attaching the managed policy: `AWSCloudShellFullAccess`
- Opened CloudShell as `analyst-user`
- Ran:
  bash
  aws ec2 describe-instances
-  Successfully retrieved metadata about running EC2 instances

📸 Screenshot: `Screenshots/9 analyst user couldnt terminate instance using cli.png`

---

## Key Takeaways
- Custom IAM policies give fine-grained control over AWS access

- Even “read-only” access to EC2 can and should be scoped explicitly

- Policies are evaluated in terms of Allow, Deny, and implicit deny

- It's safer to only allow specific actions and test permissions iteratively

- CloudShell is a great way to validate CLI behavior without installing the AWS CLI locally