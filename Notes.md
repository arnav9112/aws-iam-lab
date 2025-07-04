# Notes – AWS IAM Hands-on Lab

As I began exploring AWS IAM through my demo AWS account, I started with the Identity and Access Management (IAM) service. IAM is a global service in AWS that lets you create and manage users, groups, and permissions. It follows the **principle of least privilege**, meaning users only get the minimum permissions they need to do their job.

---

## Step 1 – Creating Users

I first created three IAM users:
- `admin-user`
- `analyst-user`
- `dev-user`

Each was assigned a password for AWS Management Console access.

---

##  Step 2 – Creating Groups and Assigning Permissions

Then, I created three IAM groups:
- `Admins` – attached **AdministratorAccess**
- `Analysts` – attached **CloudWatchReadOnlyAccess**
- `Developers` – attached **AWSLambdaExecute** and **CloudWatchLogsFullAccess**

I added each user to the appropriate group:
- `admin-user` → Admins
- `analyst-user` → Analysts
- `dev-user` → Developers

---

## Step 3 – Testing Permissions

I logged in with each user account to check what they could (or couldn’t) do.

### Admin User
- Logged in as `admin-user` and successfully created a new IAM user called `sample-user`
- Also added `sample-user` to the `Analysts` group
- Full access worked as expected

### Analyst User
- Logged in as `analyst-user`
- Could not even access the IAM dashboard (permission denied)
- Tried to create a new CloudWatch log group → received an error due to lack of write permissions
- Viewing logs (read access) worked fine

### 🛠 Developer User (To Be Tested Next)
- Haven’t fully tested `dev-user` yet, but plan to try deploying a Lambda function and monitoring its logs

---

## 📌 What I Learned So Far

- IAM permissions **must be explicitly granted**—nothing is allowed by default
- Groups make user management much easier and scalable
- You have to **test permissions actively** to validate what users can/can’t do
- Even with read-only policies, users might face permission issues on certain AWS pages (e.g., CloudWatch log creation)

---

##  Next Steps

- Test `dev-user` with Lambda function execution and logging
- Set up MFA for `admin-user` for added security
- Create a custom JSON policy instead of using only managed ones
- Try simulating a misconfiguration (like giving excessive permissions) and fixing it


