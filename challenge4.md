# Challenge Number: 4

## Challenge Statement:
Admin only?
We learned from our mistakes from the past. Now our bucket only allows access to one specific admin user. Or does it?
---

## IAM Policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                },
                "ForAllValues:StringLike": {
                    "aws:PrincipalArn": "arn:aws:iam::133713371337:user/admin"
                }
            }
        }
    ]
}
```

---

## What do I have access to?

- I can read the files inside the S3 bucket “thebigiamchallenge-admin-storage-abf1321”.
The bucket allows listing files inside the "files/" folder but only for IAM user “admin”.

---

## What don't I have access to?

- I cannot list the files in the bucket but I can still access a file directly if I know the exact file name.
---

## What do I find interesting?

- Anyone can read the files but only admin IAM user can list them. Technically, this means that files are exposed to the public if the file names are known.

---

## Solution:

To find the flag, I’ve taken the following steps:

a.	The IAM Policy indicates s3:GetObject and s3:ListBucket so this is about S3 resource. Link to the reference AWS documentation are listed below.
-	https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/list-buckets.html 
-	https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-object.html 
-	https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition_examples-multi-valued-context-keys.html 
-	https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html 
-	https://docs.aws.amazon.com/cli/latest/reference/ 

b.	I have tried to list the files inside the bucket using below command. However, it returned a result as Access Denied.
   ```
   aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/files/
   ```

c.	The access is limited to admin user only. To disable signing and prevent loading the credentials, I have used --no-sign-request option. The command is shown below.
```
aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/files/ --no-sign-request
```

d.	The output lists the files inside the bucket. The bucket contains 2 files.
    
    •	2023-06-07 19:15:43         42 flag-as-admin.txt
    •	2023-06-08 19:20:01      81889 logo-admin.png

e.	To check the contents of the file, I’ve used below command:
```
aws s3 cp s3://thebigiamchallenge-admin-storage-abf1321/files/flag-as-admin.txt -  --no-sign-request
```
---

## Reflection:

### What was your approach?
Read the IAM Policy and identify the resource. Research on the command syntax for the specified resource.

### What was the biggest challenge?
The hardest part is figuring out how to get into the bucket without being admin.

### How did you overcome the challenges?
A lot of reading and online references with regards to the proper command syntax and bypassing the admin credentials.

### What led to the breakthrough?
The key information is getting the filenames of objects stored in the bucket.

### On the blue side, how can the learning be used to properly defend the important assets?
Defend important assets by restrict listing, provide access only to specific users, and possibly logging of unauthorized access.
