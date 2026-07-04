# Learnings

## 1. How Amazon S3 supports application storage

This project showed how **Amazon S3** can be used as an object storage service for application assets such as employee profile images.
Instead of storing images locally inside the application server, they can be stored in an S3 bucket and accessed whenever needed.

---

## 2. How DynamoDB works as a NoSQL backend

I learned how **Amazon DynamoDB** can be used to store employee records in a structured but schema-flexible way.
The employee directory application used DynamoDB to store details such as:

* employee ID
* name
* email
* location
* photo reference

This helped me understand how NoSQL databases can be used in cloud applications for fast and scalable data storage.

---

## 3. IAM roles and policies are critical for service integration

One of the most important takeaways from this project was the role of **IAM permissions** in connecting AWS services securely.

I learned that:

* applications should use **IAM roles** instead of hardcoded credentials
* **S3 bucket policies** can be used to allow or restrict access to objects
* correct permissions are essential for an application to interact with AWS services

---

## 4. Cloud applications often depend on multiple services working together

This lab was a good example of how a simple web application can depend on multiple AWS services at the same time:

* **EC2** for hosting the application
* **S3** for image storage
* **DynamoDB** for employee data
* **IAM** for secure access control

It reinforced the idea that cloud applications are often built by combining multiple managed services rather than relying on a single component.

---

## 5. Configuration is as important as resource creation

A major learning from this project was that simply creating AWS resources is not enough.
The application also needs to be configured correctly to use those resources.

For example:

* the S3 bucket had to be created
* the correct bucket policy had to be added
* the bucket name had to be entered into the application configuration
* the DynamoDB table had to match the expected name and key structure

This taught me the importance of **end-to-end integration**, not just resource provisioning.

---

## 6. DynamoDB item management through both UI and console

I learned two ways of working with DynamoDB data:

* through the **Employee Directory application UI**
* directly through the **AWS Management Console**

This helped me understand how application data stored in DynamoDB can be validated, updated, and tested from different interfaces.

---

## 7. Real-world understanding of storage vs database responsibilities

This project clearly separated:

* **S3** as storage for image files
* **DynamoDB** as storage for employee metadata and application records

That distinction helped me better understand where each AWS service fits in a real application architecture.

---

## 8. Hands-on exposure to AWS application integration

Overall, this project gave me practical exposure to:

* S3 bucket creation and object upload
* bucket policy configuration
* DynamoDB table creation
* item insertion and editing
* testing a web application integrated with AWS services

It was a useful foundational lab for understanding how AWS services support application backends.
