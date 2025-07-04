# AWS IAM Lab – Simulating Role-Based Access in the Cloud

##  What this is about
This is a small lab I built to get hands-on with IAM (Identity and Access Management) on AWS. The goal was to simulate a real-world setup where different users have different levels of access—and then test what happens when you try to go beyond your permissions.

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

##  What I Tested

- Logged in as `admin-user` → was able to create a new IAM user ✅
- Logged in as `analyst-user` → tried to create a CloudWatch log group → **got an error** ❌
- Tried viewing logs as `analyst-user` → allowed ✅
- Tried accessing EC2 → denied as expected ❌

I’ve added screenshots of each step in the `/screenshots` folder.

---

##  Takeaways

- IAM follows the **principle of least privilege** by default—if it’s not explicitly allowed, it’s denied
- Group-based permissions make managing access a lot easier than setting up every user manually
- Even small misconfigurations (like missing a policy) can block legitimate actions
- Testing is essential—permissions don’t always behave the way you assume

---

## 📁 What’s inside this repo