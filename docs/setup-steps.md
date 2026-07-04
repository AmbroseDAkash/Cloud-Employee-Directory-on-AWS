# Setup Steps

## 1. Create an Amazon S3 bucket

* Open the AWS Management Console and go to **Amazon S3**.
* Create a new bucket for storing employee images.
* Use a unique bucket name such as `employee-photo-bucket-<initials>-<random-number>`.
* Keep **Block all public access** enabled.
* Make sure the bucket is created in the same AWS Region as the lab environment.

## 2. Add an S3 bucket policy

* Open the bucket you created.
* Go to the **Permissions** tab.
* Edit the **Bucket policy**.
* Add a policy that allows the application IAM role to access the bucket and its objects.

## 3. Configure the web application to use the S3 bucket

* Open the Employee Directory web application.
* Go to the **Configuration** section.
* Enter the S3 bucket name in the S3 bucket field.
* Save the configuration.
* Verify that S3 access is enabled in the application.

## 4. Upload employee images to S3

* Download or collect the employee image files.
* Open the S3 bucket and go to the **Objects** tab.
* Upload the employee `.png` images to the bucket.
* Confirm that the images appear in the Employee Directory application.

## 5. Create a DynamoDB table

* Open **Amazon DynamoDB** in the AWS Management Console.
* Create a table named `Employees`.
* Set the partition key as `id` with type `String`.
* Wait until the table status becomes **Active**.

## 6. Test the application using the web interface

* Refresh the Employee Directory application.
* Open the **Management** section.
* Add a new employee record with fields such as:

  * `id`
  * `name`
  * `location`
  * `email`
  * `photo`
* Save the employee and verify that the record is stored successfully.

## 7. Manage employee records in DynamoDB

* Open the `Employees` table in DynamoDB.
* Use **Explore table items** to view existing employee records.
* Edit attributes such as `location` or `email`.
* Save the changes and verify that they are reflected in the web application.

## 8. Add new items directly from DynamoDB

* In the DynamoDB table, choose **Create item**.
* Add the required employee attributes:

  * `id`
  * `name`
  * `location`
  * `email`
  * `photo`
* Save the item and verify that it appears in the Employee Directory application after refreshing the page.

## Outcome

By the end of this project, the Employee Directory application is integrated with:

* **Amazon S3** for storing employee images
* **Amazon DynamoDB** for storing employee details

This demonstrates a simple AWS-based web application backend integration workflow using storage, NoSQL database, and IAM-based access control.
