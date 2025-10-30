# Security-Policies-Implementation

# 🛡️ AWS CLI Activity – Security Policies Implementation

## 🚀 Objective
To compare and create different types of **AWS Security Policies** used for securing a web application on AWS infrastructure — using both **AWS Console** and **Command Line Interface (CLI)**.

---

## Configure AWS

<img width="1197" height="202" alt="Screenshot 2025-10-30 191613" src="https://github.com/user-attachments/assets/8bfed2aa-fa39-4845-ad44-f2f79d84e82a" />


## 🧩 1. Creation of Security Policy (IAM Policy)

### 🔹 Using AWS Console

<img width="1916" height="915" alt="Screenshot 2025-10-30 190448" src="https://github.com/user-attachments/assets/9585a9e0-db10-47b0-a430-9d5bb4a820df" />

1. Open **IAM → Policies → Create Policy**


2. Choose **JSON** tab and paste:
   
   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": ["s3:GetObject", "s3:ListBucket"],
         "Resource": "*"
       }
     ]
   }
   ```
   
<img width="1919" height="922" alt="Screenshot 2025-10-30 190508" src="https://github.com/user-attachments/assets/ac5f1bf7-0b45-4bf3-a597-9f9ba0475164" />

3.Name it MyS3ReadPolicy and click Create Policy.

<img width="1909" height="880" alt="Screenshot 2025-10-30 190535" src="https://github.com/user-attachments/assets/3f3003ff-3621-4bec-8b6a-03b060a1adc9" />

----------------------

### 🔹 Using AWS CLI

File: s3-policy-cli.json

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": "*"
    }
  ]
}

Command: aws iam create-policy --policy-name MyS3ReadPolicyCLI --policy-document file://s3-policy-cli.json

### ✅ Output Example:

<img width="1447" height="408" alt="Screenshot 2025-10-30 191637" src="https://github.com/user-attachments/assets/2da628ee-e2e9-4284-b352-14aa6486b32c" />


-------
-------

### 🔒 2. Creation of Permission Boundary
🔹 Using AWS Console

1.Go to IAM → Policies → Create Policy

<img width="1919" height="913" alt="Screenshot 2025-10-30 191159" src="https://github.com/user-attachments/assets/b0940bf3-d43e-4be1-af83-bb162b389526" />


2.Paste this JSON:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["ec2:Describe*", "s3:GetObject"],
      "Resource": "*"
    }
  ]
}

<img width="1919" height="882" alt="Screenshot 2025-10-30 191208" src="https://github.com/user-attachments/assets/fcba4914-f85d-4b49-8da0-e065e09f868c" />


3.Name it MyBoundaryPolicy and create it.


4.Attach this policy as a Permission Boundary to your role or user.

<img width="1919" height="944" alt="Screenshot 2025-10-30 191247" src="https://github.com/user-attachments/assets/6ca595eb-b8e3-49a6-8f6b-b2b640b3d361" />


### 🔹 Using AWS CLI

File: boundary-cli.json

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["ec2:Describe*", "s3:GetObject"],
      "Resource": "*"
    }
  ]
}

### Command:

aws iam create-policy --policy-name MyBoundaryPolicyCLI --policy-document file://boundary-cli.json

### ✅ Output Example:

<img width="1473" height="434" alt="Screenshot 2025-10-30 191714" src="https://github.com/user-attachments/assets/6f9c5b10-63bf-4ade-a57f-0dc913b3c376" />


---------------------
---------------------

⚙️ 3. Creation of IAM Role
🔹 Using AWS Console

Go to IAM → Roles → Create Role

<img width="1919" height="940" alt="Screenshot 2025-10-30 190938" src="https://github.com/user-attachments/assets/0e310901-d308-4a45-90d3-f5ad7c130654" />

Choose AWS Service → EC2

Click Next → Attach your MyS3ReadPolicy

<img width="1919" height="922" alt="Screenshot 2025-10-30 191005" src="https://github.com/user-attachments/assets/7857b068-b087-485d-bb1a-2d78502ceeea" />

Name the role MyEC2Role → Create Role

<img width="1919" height="915" alt="Screenshot 2025-10-30 191042" src="https://github.com/user-attachments/assets/3b6ea33f-3d40-459a-a53d-90e8f3d96a32" />

<img width="1910" height="908" alt="Screenshot 2025-10-30 191116" src="https://github.com/user-attachments/assets/290ed2fd-5b99-4a30-9336-75edd6bfe18f" />


### 🔹 Using AWS CLI

File: trust-policy-cli.json

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "ec2.amazonaws.com" },
      "Action": "sts:AssumeRole"
    }
  ]
}

### Commands:

aws iam create-role --role-name MyEC2RoleCLI --assume-role-policy-document file://trust-policy-cli.json

<img width="1462" height="615" alt="Screenshot 2025-10-30 191817" src="https://github.com/user-attachments/assets/ae73a74c-ee90-42dd-b690-928bb03e690e" />


aws iam attach-role-policy --role-name MyEC2RoleCLI --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/MyS3ReadPolicyCLI

<img width="1461" height="64" alt="Screenshot 2025-10-30 191937" src="https://github.com/user-attachments/assets/8c66cc86-20ad-4384-9245-4c83ae1f85e4" />


aws iam put-role-permissions-boundary --role-name MyEC2RoleCLI --permissions-boundary arn:aws:iam::<ACCOUNT_ID>:policy/MyBoundaryPolicyCLI


<img width="1919" height="909" alt="Screenshot 2025-10-30 192023" src="https://github.com/user-attachments/assets/eb9de09f-b700-4211-9d52-893af8732169" />


### ✅ Verify Role:

aws iam get-role --role-name MyEC2RoleCLI

<img width="1357" height="709" alt="Screenshot 2025-10-30 192035" src="https://github.com/user-attachments/assets/7e86b272-9355-4ea0-b863-f1f53c8e8ea0" />

-------
--------
### 🧠 Key Learnings

IAM Policies define what actions are allowed or denied for AWS resources.

Permission Boundaries limit the maximum permissions a role or user can have.

IAM Roles are used by services (like EC2) for secure access without credentials.

SCPs apply organization-wide restrictions and enforce compliance.

Always follow principle of least privilege when granting access.

## Author : Suyash Dahitule
