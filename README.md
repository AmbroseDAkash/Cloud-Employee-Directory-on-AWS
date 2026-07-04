# Cloud Employee Directory on AWS

### Secure integration of a web application with Amazon S3 and Amazon DynamoDB using IAM-based access control

![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Amazon S3](https://img.shields.io/badge/Storage-Amazon%20S3-blue)
![DynamoDB](https://img.shields.io/badge/Database-DynamoDB-4053D6)
![IAM](https://img.shields.io/badge/Security-IAM-red)
![Project Type](https://img.shields.io/badge/Project-Cloud%20Application%20Integration-success)

---

## Executive Summary

This project demonstrates how a web-based **Employee Directory application** can be integrated with core AWS services to support **object storage**, **NoSQL data persistence**, and **secure service-to-service access**.

The application is hosted in an AWS lab environment and initially lacked backend integrations for employee image storage and employee record management. To complete the architecture, I provisioned and configured:

* **Amazon S3** for employee image storage
* **Amazon DynamoDB** for employee metadata and directory records
* **IAM role / S3 bucket policy integration** to securely authorize the application to access storage resources
* End-to-end validation of the application workflow through both the **application UI** and the **AWS Management Console**

The result is a simple but realistic cloud integration scenario that demonstrates how storage, database, and access control services work together to support an application backend on AWS.

---

## Why this project matters

This project is intentionally simple at the application layer, but architecturally useful because it demonstrates several foundational cloud patterns:

* **Decoupling unstructured and structured data**

  * Employee images are stored in **Amazon S3**
  * Employee metadata is stored in **Amazon DynamoDB**

* **IAM-based service integration**

  * The application uses an **IAM role** and **bucket policy** instead of hardcoded credentials

* **Application-to-cloud dependency mapping**

  * The web application depends on correct resource creation, naming, permissions, and configuration before becoming functional

* **Cloud troubleshooting mindset**

  * The application initially surfaced backend dependency errors
  * The solution required validating resource availability, permissions, and application configuration across multiple AWS services

---

## Solution Architecture

The Employee Directory application is hosted on an **Amazon EC2 instance** in a pre-provisioned AWS lab environment. The application relies on two AWS-managed services to complete its backend workflow:

* **Amazon S3** stores employee profile images
* **Amazon DynamoDB** stores employee records such as name, email, location, and image reference
* **IAM / bucket policy** grants the application permission to access the S3 bucket securely

### High-level architecture

```text
┌──────────────────────────────┐
│ Employee Directory Web App   │
│ Hosted on Amazon EC2         │
└──────────────┬───────────────┘
               │
               │ Reads/Writes employee data
               ▼
      ┌─────────────────────┐
      │ Amazon DynamoDB     │
      │ Table: Employees    │
      │ PK: id              │
      └─────────────────────┘

               │
               │ Fetches employee images
               ▼
      ┌─────────────────────┐
      │ Amazon S3           │
      │ Employee image store│
      └─────────────────────┘

               ▲
               │ Access controlled via
               │ IAM role + S3 bucket policy
               │
      ┌─────────────────────┐
      │ EmployeeDirectory   │
      │ App IAM Role        │
      └─────────────────────┘
```

---

## Architecture Decisions

### 1) Amazon S3 for employee images

Employee profile images are binary/static objects and are better suited for object storage than relational or NoSQL database storage.

**Why S3 is appropriate here**

* Highly durable object storage
* Simple retrieval of static assets
* Logical separation of file storage from application records
* Easier scaling for image storage than storing binary content in a database

---

### 2) Amazon DynamoDB for employee records

Employee records such as `id`, `name`, `location`, `email`, and `photo` are stored in DynamoDB.

**Why DynamoDB is appropriate here**

* Simple key-value / document-style data model
* Fully managed NoSQL database
* Fast lookup of employee records by primary key
* Suitable for lightweight application metadata and user directory records

---

### 3) IAM role + bucket policy instead of embedded credentials

The application accesses S3 using AWS-native identity controls rather than storing static access keys inside the application.

**Security value of this approach**

* Avoids hardcoded credentials
* Centralizes access control in AWS IAM / resource policies
* Aligns with AWS security best practices for service-to-service access
* Reduces the risk of credential leakage in application code or configuration files

---

## AWS Services Used

| Service                    | Purpose in this project                                       |
| -------------------------- | ------------------------------------------------------------- |
| **Amazon EC2**             | Hosts the employee directory web application                  |
| **Amazon S3**              | Stores employee profile images                                |
| **Amazon DynamoDB**        | Stores employee directory records                             |
| **AWS IAM**                | Controls secure access from the application to AWS resources  |
| **AWS Management Console** | Used for resource creation, validation, and record management |

---

## Project Scope

This project covered the following implementation areas:

* Creating and configuring an **Amazon S3 bucket**
* Writing and applying an **S3 bucket policy**
* Configuring the application to use the S3 bucket for employee images
* Uploading employee image assets to S3
* Creating a **DynamoDB Employees table**
* Adding, editing, and deleting employee records through the application UI
* Managing and validating DynamoDB items directly through the AWS Console
* Verifying that the application correctly consumed both storage and database backends

---

## Implementation Walkthrough

## 1. Provisioned an S3 bucket for employee images

A dedicated S3 bucket was created to store employee profile images used by the application.

### Key configuration points

* Bucket created in the lab AWS Region
* Public access remained blocked
* Bucket used as a dedicated storage backend for employee images

### Example naming pattern

```text
employee-photo-bucket-<project-name>
```

---

## 2. Applied an S3 bucket policy for application access

The application required permission to access objects stored in the S3 bucket.
This was implemented using a **bucket policy** that grants the application IAM role access to the bucket and its contents.

### Bucket policy used in this project

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<AWS-ACCOUNT-ID>:role/EmployeeDirectoryAppRole"
      },
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::employee-photo-bucket-<project-name>",
        "arn:aws:s3:::employee-photo-bucket-<project-name>/*"
      ]
    }
  ]
}
```

### Security note

For a production environment, this policy should be **narrowed further** to least-privilege permissions such as only the required S3 actions (`GetObject`, possibly `PutObject` depending on application behavior) instead of `s3:*`.

---

## 3. Integrated the web application with S3

The application initially showed an error indicating that the employee image bucket was not configured.
To resolve this, the S3 bucket name was added in the application’s **Configuration** section.

### Outcome

Once the bucket name was configured correctly and the bucket policy was in place:

* S3 access was successfully enabled
* the application could retrieve employee images from S3

---

## 4. Uploaded employee image assets to S3

A set of employee image files was uploaded to the S3 bucket and then validated from the Employee Directory application.

### Outcome

The **Images** section of the application displayed the uploaded employee images successfully, confirming:

* the bucket was reachable
* object upload worked correctly
* the application had the required access to retrieve objects

---

## 5. Created the DynamoDB backend table

A DynamoDB table named **Employees** was created to store employee directory data.

### Table design

| Attribute              | Value       |
| ---------------------- | ----------- |
| **Table name**         | `Employees` |
| **Partition key**      | `id`        |
| **Partition key type** | `String`    |

### Record attributes used by the application

* `id`
* `name`
* `location`
* `email`
* `photo`

---

## 6. Tested application functionality through the UI

After the S3 and DynamoDB backends were in place, the application was tested end-to-end using its own web interface.

### Operations validated

* Add employee
* Select employee image
* Save employee record
* Edit employee data
* Delete employee record

### Why this mattered

This verified that:

* the application could **write employee records to DynamoDB**
* the application could **reference image objects stored in S3**
* the frontend and backend dependencies were integrated correctly

---

## 7. Validated and modified records directly in DynamoDB

The `Employees` table was also managed directly from the AWS Console to verify the backend data model independently of the application.

### Actions performed

* Viewed table items
* Edited employee fields such as `location` and `email`
* Confirmed changes reflected back in the application after refresh

### Value of this step

This helped confirm that the application was not caching static test data and was instead reading live data from DynamoDB.

---

## 8. Added records manually through the DynamoDB console

Additional employee items were inserted directly from the AWS Console using **Create item**.

### Outcome

These records became visible in the application, confirming:

* the application was consuming live table data
* the S3 + DynamoDB + application integration path was functioning as expected

---

## Repository Structure

```text
aws-employee-directory-lab/
│
├─ README.md
├─ docs/
│  ├─ setup-steps.md
│  └─ screenshots/
│
├─ policies/
│  └─ s3-bucket-policy.json
│
├─ sample-data/
│  └─ sample-employee-records.json
│
└─ notes/
   ├─ learnings.md
   └─ issues-faced.md
```

---

## Screenshots

> Add your screenshots inside `docs/screenshots/` and reference them here.

### S3 bucket policy

```text
docs/screenshots/s3-bucket-policy.png
```

### Employee images visible in the application

```text
docs/screenshots/employee-images.png
```

### DynamoDB Employees table

```text
docs/screenshots/dynamodb-table.png
```

### Application configuration page

```text
docs/screenshots/app-configuration.png
```

Once you upload the screenshots, replace the placeholders above with markdown image links like this:

```md
![S3 Bucket Policy](docs/screenshots/s3-bucket-policy.png)
```

---

## Security Considerations

Although this project was completed in a lab environment, it still highlights several important security concepts relevant to real-world cloud engineering.

### Security controls demonstrated

* Use of **IAM role-based access** rather than embedding AWS credentials in the application
* Use of **S3 bucket policy** to explicitly control resource access
* **Public access blocked** at the S3 bucket level
* Separation of storage and database responsibilities across managed services

### Security improvements I would make for a production implementation

If this were deployed as a production workload, I would strengthen it with:

* **Least-privilege S3 permissions**
  Replace `s3:*` with only the minimum actions required

* **S3 encryption at rest**

  * SSE-S3 or SSE-KMS for object encryption

* **DynamoDB encryption and backup validation**

  * Confirm encryption at rest
  * Enable PITR if business requirements justify it

* **CloudTrail and CloudWatch integration**

  * Log access to S3 and administrative actions
  * Alert on anomalous changes to IAM, S3 policies, or DynamoDB tables

* **Application authentication / authorization**

  * Restrict who can add, modify, or delete employee records

* **Infrastructure as Code**

  * Recreate the environment using Terraform or CloudFormation for repeatability and change control

* **Secrets / configuration hygiene**

  * Avoid environment-specific values in source control
  * Externalize configuration using a secure parameter store or secrets manager where appropriate

---

## Key Technical Learnings

This project helped reinforce several core cloud engineering and cloud security concepts:

* How **Amazon S3** fits into an application architecture as object storage
* How **DynamoDB** can be used as a lightweight NoSQL application backend
* Why **IAM roles and resource policies** are central to secure AWS service integration
* How cloud applications often fail due to **configuration gaps**, not only code defects
* Why validating architecture across **UI + backend services + cloud console** is important during troubleshooting
* How to reason about **storage of files vs storage of application metadata** as separate architectural concerns

---

## Skills Demonstrated

### Cloud / AWS

* Amazon S3 bucket creation and object management
* S3 bucket policy implementation
* DynamoDB table creation and item management
* IAM-based application access configuration
* Multi-service AWS application integration

### Security / Governance

* Understanding of trust boundaries between application, storage, and database layers
* Use of role-based access instead of static credentials
* Awareness of least privilege and secure resource policy design
* Basic cloud configuration validation and troubleshooting

### Operational / Practical

* Application backend dependency mapping
* End-to-end functional validation of cloud-integrated applications
* Manual data verification using the AWS Console
* Troubleshooting misconfiguration between application and AWS services

---

## Future Enhancements

If I were extending this project beyond the lab, the next improvements I would implement are:

### Architecture enhancements

* Replace direct application dependencies with an **API layer**
* Introduce **AWS Lambda** for backend business logic
* Use **Amazon API Gateway** as the application’s managed API frontend
* Add **CloudFront** if serving images externally at scale
* Add **infrastructure as code** using Terraform or CloudFormation

### Security enhancements

* Replace broad S3 permissions with least-privilege access
* Add **S3 default encryption** and validation of bucket security controls
* Add **CloudTrail + CloudWatch alerts** for visibility and monitoring
* Introduce **authentication and authorization** for employee management operations
* Validate data access patterns and role assumptions more rigorously

### Data / application enhancements

* Add employee search and filtering support
* Add photo upload workflows from the application itself
* Add form validation and audit logging for record changes
* Introduce soft-delete or record versioning patterns for employee data

---

## What I would discuss in a cloud / security interview about this project

If asked to walk through this project in an interview, I’d frame it around four themes:

### 1. Architecture

How the application uses **S3 for object storage** and **DynamoDB for employee metadata**, and why that separation makes architectural sense.

### 2. Security

How IAM role-based access and bucket policies allow the application to securely interact with AWS services without hardcoded credentials.

### 3. Troubleshooting

How the application initially failed due to missing cloud dependencies, and how the problem was resolved by validating resource creation, access control, and application configuration.

### 4. Production hardening

How I would improve the design using least privilege, encryption, logging, monitoring, and infrastructure-as-code if this were a production deployment.

---

## Conclusion

This project is a compact but practical demonstration of **cloud application integration on AWS**.

It shows how to connect an application with:

* **Amazon S3** for object storage
* **Amazon DynamoDB** for persistent application data
* **IAM and bucket policies** for secure access control

More importantly, it demonstrates the mindset required to work on cloud-backed applications: understanding service roles, validating dependencies, applying permissions correctly, and thinking about how to harden a simple architecture for production use.

---

## Author Notes

This repository is intended as a **cloud engineering / cloud security portfolio project** to demonstrate practical AWS fundamentals around:

* storage integration
* NoSQL backend configuration
* IAM-based access control
* multi-service application troubleshooting
* secure cloud design thinking
