# Issues Faced

## 1. Missing backend resources in the application

When the Employee Directory application was first opened, it showed errors indicating that:

* the **S3 employee images bucket** was not configured
* the **DynamoDB Employees table** did not exist

This meant the application backend was incomplete until both services were created and linked properly.

---

## 2. S3 bucket policy configuration

One of the main setup challenges was configuring the **S3 bucket policy** correctly so the application could access employee images stored in the bucket.

Things that needed attention:

* the correct **AWS Account ID**
* the correct **IAM role ARN** for the application
* the exact **S3 bucket name** in the resource section of the policy

If any of these values were incorrect, the application would not be able to access the images.

---

## 3. Application configuration dependency on the bucket name

Even after creating the S3 bucket and uploading images, the application would not work until the **exact bucket name** was entered in the Employee Directory application configuration page.

This showed that infrastructure creation alone was not enough — the application also had to be manually connected to the newly created S3 bucket.

---

## 4. DynamoDB table naming and schema requirements

The DynamoDB table had to be created with the exact configuration expected by the application:

* **Table name:** `Employees`
* **Partition key:** `id`
* **Type:** `String`

If the table name or primary key configuration was different, the application would not be able to use the table correctly.

---

## 5. Manual synchronization between S3 images and DynamoDB records

Employee images and employee records were stored separately:

* images in **Amazon S3**
* employee metadata in **Amazon DynamoDB**

This meant that the `photo` attribute in DynamoDB had to match the correct image stored in S3. If the image name did not match, the employee image would not display properly in the application.

---

## 6. Verifying changes across multiple services

Testing the project required checking changes across:

* the **Employee Directory web application**
* the **Amazon S3 bucket**
* the **DynamoDB table**

For example:

* images had to be uploaded to S3
* employee records had to be added to DynamoDB
* the application had to be refreshed to confirm that both were working together

This made troubleshooting a little more involved than a single-service project.

---

## 7. Editing restrictions in DynamoDB

While modifying records directly from the DynamoDB console, the **primary key field (`id`) could not be edited**.
If a wrong `id` was entered, the item had to be deleted and recreated instead of updated.

---

## Summary

The main challenges in this project were not coding-related, but **configuration-related**:

* setting up the right AWS resources
* applying the correct permissions
* linking the application with S3 and DynamoDB
* ensuring consistency between stored images and employee records

These issues were useful for understanding how small configuration mistakes can affect the working of a cloud application.
