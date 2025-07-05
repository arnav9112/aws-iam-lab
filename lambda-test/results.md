# Lambda Function Test – IAM Role Simulation (dev-user)

## 🧠 Objective

This lab demonstrates the principle of **least privilege** in AWS IAM by simulating the permissions of a developer user (`dev-user`) trying to deploy and test a Lambda function. The goal was to:

- Create and deploy a Lambda function  
- Attach a pre-created IAM execution role  
- Monitor function logs using CloudWatch  
- Do all of the above with **only the necessary permissions**

---

## 🔧 Initial Setup

- **User:** `dev-user`  
- **Group:** `Developers`  
- **Initial Policies:**
  - `AWSLambdaExecute`
  - `CloudWatchLogsFullAccess`

Expected this setup would be sufficient for Lambda development and monitoring. But several permission issues were encountered.

---

## ❌ Error 1: `iam:CreateRole` Denied

### When?
During Lambda function creation when AWS attempted to auto-create an execution role.

### Error:You don't have permission to iam:CreateRole.

### Fix:
- Switched to `admin-user`
- Manually created role: `lambda-basic-execution-role`
- Attached policy: `AWSLambdaBasicExecutionRole`
- Set trusted entity as Lambda

---

## ❌ Error 2: `iam:ListRoles` Denied

### When?
Trying to select an existing role during Lambda creation.

### Error: Not authorized to perform: iam:ListRoles

### Fix:
As `admin-user`, added this **inline policy** to `Developers` group:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:ListRoles",
        "iam:PassRole"
      ],
      "Resource": "*"
    }
  ]
}

This allowed the developer to view and assign the existing IAM role.

❌ Error 3: lambda:CreateFunction Denied

Fix:
As admin-user, attached AWSLambda_FullAccess to Developers group

After all permissions were added:

dev-user created the Lambda function: demo-function

Chose existing role: lambda-basic-execution-role

Created test event → executed function → returned 200 OK

Logs confirmed in CloudWatch