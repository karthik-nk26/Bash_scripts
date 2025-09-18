# AWS Bucket Setup and Mounting on Ubuntu Server

This document explains how to create an AWS S3 bucket, configure IAM permissions, and mount it on an Ubuntu server.

---

## 1. Create a User in AWS and Generate Access Keys
1. Navigate to **IAM** service in AWS.
2. Click on **Users** in the left sidebar.
3. Click **Create User** and provide a name.
4. Assign permissions later (skip for now).
5. Generate **Access Key** and **Secret Key** (download the `.csv` file).

---

## 2. Create an AWS S3 Bucket
1. Login to AWS account.
2. Go to **S3 → Create Bucket**.
3. Choose:
   - **Bucket type** → General purpose
   - **Bucket name** → (Provide unique name)
   - **Object Ownership** → ACLs disabled
   - **Bucket Versioning** → Disable
4. Leave other settings default and click **Create Bucket**.

---

## 3. Create an IAM User
1. Go to **IAM → Users**.
2. Click **Create User** and provide a username.
3. Skip permissions for now, then click **Create User**.

---

## 4. Create an IAM Group
1. Go to **IAM → User Groups**.
2. Create a group and provide a name.

---

## 5. Create an IAM Policy
1. Go to **IAM → Policies → Create Policy**.
2. Select **JSON** and paste the following (update bucket name as needed):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3Access",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::tnd-bucket-styra/*"
    },
    {
      "Sid": "AllowListBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::tnd-bucket-styra"
    },
    {
      "Sid": "AllowSTS",
      "Effect": "Allow",
      "Action": [
        "sts:AssumeRole",
        "sts:GetCallerIdentity"
      ],
      "Resource": "*"
    }
  ]
}
