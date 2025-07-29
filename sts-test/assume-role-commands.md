## 🧪 Assume Role via AWS CLI

### 1. Run the AssumeRole command:

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::YOUR_ACCOUNT_ID:role/AuditAccessRole \
  --role-session-name audit-session

---

You’ll receive JSON output like this:

  "Credentials": {
    "AccessKeyId": "ASIA...",
    "SecretAccessKey": "abcd...",
    "SessionToken": "FQoGZXIvYXdzEK...",
    "Expiration": "..."
  }
}

---

Export the Temporary Credentials

export AWS_ACCESS_KEY_ID="ASIA..."
export AWS_SECRET_ACCESS_KEY="abcd..."
export AWS_SESSION_TOKEN="FQoGZXIvYXdzEK..."

---

Test Access Again

aws ec2 describe-instances