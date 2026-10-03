# Day 4 — AWS IAM

## 1. IAM

**IAM (Identity and Access Management)** controls **who can access AWS resources and what they can do**.

### 4 Core IAM Components

| Component | Meaning |
|---|---|
| **User** | An identity representing a person or application that needs AWS access. |
| **Group** | A collection of IAM users. Policies can be attached to the group and inherited by its users. |
| **Role** | An identity with permissions that can be assumed by AWS services, users, or applications. Commonly used by EC2 instead of storing access keys. |
| **Policy** | A JSON document that defines what actions are **Allowed** or **Denied**, and on which resources. |

### How They Connect

```text
Human
  |
 IAM User
  |
  +----> Group ----> Policy
  |
  +----> Policy


AWS Service
     |
   Role
     |
   Policy
     |
 AWS Resource
```

**Easy way to remember:**

```text
User   → Individual identity
Group  → Collection of users
Role   → Assumed identity / temporary access
Policy → What can and cannot be done
```

> A role is not only for AWS services, but **EC2 → IAM Role → Policy** is one of the most important DevOps patterns.

---

## 2. IAM Policy

A policy defines:

```text
Effect    → Allow / Deny
Action    → What operation is allowed
Resource  → Which AWS resource
```

Example:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject",
    "s3:ListBucket"
  ],
  "Resource": "*"
}
```

### Principle of Least Privilege

Give an identity **only the permissions it actually needs**.

Example:

If an EC2 application only needs to read logs from S3, don't give it full S3 access.

---

## 3. Why IAM Role Instead of Access Keys on EC2?

Avoid storing:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

on the EC2 server.

Instead:

```text
EC2
 ↓
IAM Role
 ↓
Policy
 ↓
S3
```

The role provides temporary credentials to the EC2 instance.

**Interview answer:**

> Use an IAM Role because EC2 can obtain temporary credentials automatically without storing long-term AWS access keys on the server. This reduces the risk of credential leakage and simplifies credential management.

---

# 4. Scenario: EC2 Needs to Read S3 Logs

### Question

Your application running on EC2 needs to read logs from an S3 bucket. How do you grant access securely without storing AWS keys?

### 3 Exact Steps

**1. Create an IAM Policy**

Grant only the required S3 read permissions, such as:

```text
s3:GetObject
s3:ListBucket
```

for the required bucket.

**2. Create an IAM Role**

Create a role trusted by EC2 and attach the S3 read policy to it.

```text
EC2 Trust Policy
       +
S3 Read Policy
       ↓
    IAM Role
```

**3. Attach the Role to EC2**

Attach the IAM role to the EC2 instance through its **instance profile**.

Final flow:

```text
EC2
 ↓
IAM Role
 ↓
S3 Read Policy
 ↓
S3 Bucket
```

No access keys need to be stored on the server.

---

# 5. Scenario-Based Interview Questions

### Q1. Developer needs access to S3 but should not access EC2. What would you use?

Use an **IAM User/Group with an appropriate policy** or a role depending on the access pattern. Grant only the required S3 permissions.

---

### Q2. Three developers need the same S3 permissions. What is better than attaching the policy individually?

Create an **IAM Group**, attach the policy to the group, and add the users to it.

```text
Developers Group
      ↓
 S3 Policy
      ↓
User A
User B
User C
```

---

### Q3. EC2 needs to upload files to S3. Where should you put the AWS access keys?

**Nowhere.**

Use:

```text
EC2 → IAM Role → S3 Policy
```

---

### Q4. An application only needs to read objects from one S3 bucket. Should you give `AmazonS3FullAccess`?

**No.**

Follow least privilege and allow only the required actions on the required bucket.

---

### Q5. What is the difference between an IAM User and an IAM Role?

**User:** Usually represents a specific identity with long-term credentials.

**Role:** Provides permissions that can be assumed and commonly uses temporary credentials.

---

## Quick Revision

```text
IAM
│
├── User   → Individual identity
├── Group  → Collection of users
├── Role   → Assumed identity / temporary access
└── Policy → Permissions
```

### Most important DevOps pattern

```text
EC2 → IAM Role → Policy → AWS Resource
```

### Most important interview concept

**Never store long-term AWS access keys on EC2 when an IAM Role can be used.**
