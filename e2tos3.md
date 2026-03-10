# Connect Amazon S3 from EC2 (Java / Spring Boot Perspective)

This guide explains how an application running on an EC2 instance can access an S3 bucket securely using IAM Roles and the AWS SDK for Java.

Services involved:
- Amazon EC2 (compute)
- Amazon S3 (object storage)
- AWS IAM (access control)

---

# 1. Recommended Approach

Never store AWS credentials in code or configuration files.

Best practice:
Attach an IAM Role to the EC2 instance and allow that role to access S3.

Architecture:

Application (Spring Boot)
↓
EC2 Instance
↓
IAM Role
↓
Amazon S3

---

# 2. Step 1 – Create IAM Role

Go to AWS Console → IAM → Roles → Create Role

Select:
EC2 → Next

Attach policy:
AmazonS3FullAccess

Or create custom policy:

{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": [
"s3:PutObject",
"s3:GetObject",
"s3:ListBucket"
],
"Resource": [
"arn:aws:s3:::your-bucket-name",
"arn:aws:s3:::your-bucket-name/*"
]
}
]
}

Name example:
ec2-s3-access-role

---

# 3. Step 2 – Attach Role to EC2

Go to:
EC2 → Instances

Select instance

Actions → Security → Modify IAM Role

Attach:
ec2-s3-access-role

Now the EC2 instance automatically receives temporary credentials.

---

# 4. Step 3 – Add AWS SDK Dependency

Maven dependency:

<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
</dependency>

---

# 5. Step 4 – Create S3 Client

AWS SDK automatically uses the EC2 IAM role credentials.

Java code:

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;

S3Client s3Client = S3Client.builder()
.region(Region.AP_SOUTH_1)
.build();

---

# 6. Upload File to S3

import software.amazon.awssdk.services.s3.model.PutObjectRequest;
import software.amazon.awssdk.core.sync.RequestBody;

PutObjectRequest request = PutObjectRequest.builder()
.bucket("your-bucket-name")
.key("uploads/test.txt")
.build();

s3Client.putObject(
request,
RequestBody.fromFile(Paths.get("test.txt"))
);

---

# 7. Download File from S3

import software.amazon.awssdk.services.s3.model.GetObjectRequest;

GetObjectRequest request = GetObjectRequest.builder()
.bucket("your-bucket-name")
.key("uploads/test.txt")
.build();

s3Client.getObject(
request,
Paths.get("downloaded-test.txt")
);

---

# 8. List Objects in Bucket

import software.amazon.awssdk.services.s3.model.ListObjectsV2Request;
import software.amazon.awssdk.services.s3.model.ListObjectsV2Response;

ListObjectsV2Request listRequest = ListObjectsV2Request.builder()
.bucket("your-bucket-name")
.build();

ListObjectsV2Response response = s3Client.listObjectsV2(listRequest);

response.contents().forEach(object ->
System.out.println(object.key())
);

---

# 9. Important Security Notes

Best practices:

1. Do not hardcode access keys.
2. Use IAM roles for EC2.
3. Restrict S3 bucket permissions to required actions only.
4. Enable bucket versioning for important data.
5. Keep S3 bucket private unless public access is required.

---

# 10. Production Architecture Example

Client
↓
Spring Boot API
↓
EC2 Instance
↓
IAM Role
↓
Amazon S3 Bucket

Files are uploaded and retrieved from S3 using AWS SDK inside the application.

---

# 11. Common Interview Questions

Q: How does EC2 access S3 without credentials?

Answer:
EC2 instances assume an IAM role attached to them. The AWS SDK automatically retrieves temporary credentials from the instance metadata service.

---

Q: Why should we avoid storing AWS keys in code?

Answer:
Hardcoded credentials are insecure and can be leaked. IAM roles provide temporary credentials automatically.

---

Q: What service provides permissions for EC2 to access S3?

Answer:
AWS IAM (Identity and Access Management).