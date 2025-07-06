# MFA Enforcement Test – Secure Access for admin-user

##  Objective
This lab demonstrates how to enforce Multi-Factor Authentication (MFA) in AWS IAM for a privileged user (`admin-user`) using a conditional deny policy.

The goal was to:
- Enforce MFA setup before allowing access to AWS services
- Observe access denial behavior when MFA is not configured
- Enable MFA and verify full access is restored

---

##  Initial Setup

**User**: `admin-user`  
**Group**: `Adminstrators`  
**Custom Policy Created**: `DenyAllExceptForMFACheck`

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "BlockMostAccessUnlessMFAAuthenticated",
      "Effect": "Deny",
      "NotAction": [
        "iam:ListVirtualMFADevices",
        "iam:ListAccountAliases",
        "iam:ListUsers",
        "iam:ListSSHPublicKeys",
        "iam:ListAccessKeys",
        "iam:ListServiceSpecificCredentials",
        "iam:ListMFADevices",
        "iam:ListGroups",
        "iam:ListRoles",
        "sts:GetSessionToken",
        "iam:GetUser",
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:ListUserTags"
      ],
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}

---

##  Test Case 1: Login Without MFA

- **When**: Logging in to AWS Console as `admin-user` after the policy was attached  
- **Behavior**: User could sign in, but access to all services (IAM, EC2, Lambda, etc.) was denied  
- **Error Message**:  
  `"User: arn:aws:iam::XXXXXXXXXXXX:user/admin-user is not authorized to perform: iam:ListUsers because no identity-based policy allows the iam:ListUsers action"`  
-  Screenshot: `screenshots/access-denied-no-mfa.png`

---

##  MFA Device Setup

- Navigated to: **My Security Credentials → Assign MFA device**  
- Chose **Virtual MFA Device**  
- Scanned QR code using **Google Authenticator**  
- Entered two consecutive codes to complete setup  
- Setup was successful  
- 📸 Screenshot: `screenshots/mfa-device-setup.png`

---

##  Test Case 2: Login After Enabling MFA

- **When**: Logged out and logged back in as `admin-user`  
- **Prompted** for MFA code  
- **Entered valid token** → Login successful  
- Access to services like IAM, Lambda, CloudWatch, and EC2 was now fully functional  
- 📸 Screenshot: `screenshots/access-after-mfa.png`

---

##  Final Outcome

 IAM policy correctly denied all access when MFA was not configured  
 After enabling MFA, access was automatically restored  
 Successfully demonstrated AWS best practice for securing high-privilege users with enforced MFA

---

## Key Concepts

- **Conditional IAM Policies**: Using `aws:MultiFactorAuthPresent` in deny statements  
- **Least Privilege Access Control**: Enforcing security configuration prerequisites  
- **Virtual MFA Devices**: Simple setup using Google Authenticator or similar apps  
- **Secure User Management**: Enforcing best practices proactively for sensitive users