# S3 Bucket Access Control Test – IAM + Bucket Policy Layering

## 🧠 Objective

This lab demonstrates how IAM and S3 bucket policies interact to control access to Amazon S3. It focuses on enforcing least privilege using IAM and adding an extra security layer with a bucket policy that denies insecure uploads.

The goal was to:
- Grant `dev-user` limited access to only one S3 bucket (`demo-aws-bucket121`)
- Ensure they can upload, download, and list files – but **only** in that bucket
- Prevent deletion or access to other buckets
- Enforce **HTTPS-only uploads** using a bucket policy

---

## 🔧 Initial Setup

**User**: `dev-user`  
**Group**: `Developers`  
**S3 Bucket**: `demo-aws-bucket121`

At the start, `dev-user` had **no S3 permissions**.

### ❌ Attempt 1: No IAM Permissions

When trying to access S3 as `dev-user`, received:

> AccessDenied: You are not authorized to perform this operation.

📸 Screenshot: `screenshots/3 dev-user has no permission to view bucket.png`

---

## 🛠️ IAM Fix: Custom Limited Access Policy

We created a custom IAM policy (`s3-access-policy.json`) and attached it to the **Developers** group. It allowed:

- `s3:ListBucket` – only on the target bucket
- `s3:PutObject` and `s3:GetObject` – only inside that bucket
- No `s3:DeleteObject` or access to other buckets

### 📄 Policy Used: [`s3-access-policy.json`](./s3-access-policy.json)




## Final IAM Behavior
Once the above policy was attached:

 dev-user could now see the bucket
 Screenshot: `screenshots/8 another change to be made to policy to view s3 objects.png` (before fix)

 Could upload a file (test.txt) via Console
 Screenshot: `screenshots/11 Uploading test folder.png`

 Could download the file

 Could not delete the object
(because s3:DeleteObject was not granted)

 Could not access any other S3 buckets

 IAM-based access was now properly scoped using the principle of least privilege.

## Bonus Layer: Enforcing HTTPS with a Bucket Policy
To strengthen security, we added a bucket policy that denies all requests made over insecure transport (HTTP).

This ensures that even if an IAM user has the right permissions, they must use HTTPS to interact with the bucket.

### Bucket Policy Used: bucket-policy.json

## CLI Test: Simulating Insecure Upload
We tested the bucket policy by attempting to simulate an insecure upload using the AWS CLI:


aws s3 cp test.txt s3://demo-aws-bucket121/ --profile dev-user --no-verify-ssl
 Result:
Instead of an AccessDenied error, the CLI issued a warning and the upload succeeded:

Warning: Skipping SSL validation. This is insecure and not recommended.
Upload successful.

Screenshot: `screenshots/17 using CLI to test transport.png`

### Why This Happened:
The AWS CLI still uses HTTPS, even with --no-verify-ssl

The flag only disables certificate verification, not the transport protocol itself

As a result, the condition aws:SecureTransport = false was not triggered

So the bucket policy did not block the request

### This validated that:

The bucket policy is active and correctly configured

The AWS CLI is secure by default

Combining IAM policies and S3 bucket policies provides layered defense-in-depth