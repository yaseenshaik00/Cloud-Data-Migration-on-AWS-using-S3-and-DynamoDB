<div id="top"></div>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a>
    <img src="images/kindpng_1522129.png" alt="Logo" width="160" height="105">
  </a>
  <h3 align="center">On premise Database to on Cloud Data migration - AWS</h3>

</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
	<li><a href="#Topics-I-implemented">Topics I implemented</a></li>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
      </ul>
    </li>
    <li><a href="#Lab-Setup">Lab Setup</a></li>
    <li><a href="#Steps-to-Migrate-data-from-MySQL-to-S3">Steps to Migrate data from MySQL to S3</a></li>
    <li><a href="#Import-and-Export-data-from-S3-to-DynamoDB">Import and Export data from S3 to DynamoDB</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

<p>We use S3 as a storage unit and DynamoDB as a processing unit. S3 of Amazon Web Services (AWS) is a platform of data storage and it is in the form of objects. This form object helps to analyze, store and recover huge amounts of data from websites, API’s or mobile applications. These objects are called buckets. There are few policies that we can add to the bucket which will allow us to access it and modify the data.</p>
<p>S3 will be acting as a landing zone. Once the data transfers to S3, we transfer data from S3 to DynamoDB Which will be acting as the primary database and we will be doing the transformations in the DynamoDB.</p>

### Topics I implemented

* AWS S3
* AWS DynamoDB
* MySQL
* AWS DMS
* AWS EC2

<p align="right">(<a href="#top">back to top</a>)</p>

### Built With

The tools that have been used in the project are mentioned below:

* [AWS](https://aws.amazon.com/)
* [MySQL Tool](https://www.mysql.com/downloads/)

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

<p>Amazon DynamoDB is a fully managed, serverless, key-value NoSQL database designed for high-performance applications at any scale. It comes with security, in-memory caching, automatic multi-region replication, and automated multi-region replication, as well as automatic data export controls.</p>
<p>Scalability, data availability, security, and performance are the advantages of Amazon Simple Storage Service (Amazon S3).</p>

### Prerequisites

1. An active AWS account.
2. A MySQL source database in an on-premises data center.


<p align="right">(<a href="#top">back to top</a>)</p>


<!-- Lab Setup -->
## Lab Setup

* Create a empty S3 bucket 
* Launch a MySQL RDS instance in the default VPC (Use Free tier version)
* Launch Amazon Linux EC2 instance in the same VPC and AZ. Open inbound ports 22 and 3306 in its security group
* Modify RDS security group to provide access to EC2 Security Group and VPC’s default security group on port 3306
* SSH in your EC2 instance and change to root user
* Install MySQL client (sudo yum install mysql)
* Create a database called EMPLOYEE and a table named details. Insert a few records in the details table


<p align="right">(<a href="#top">back to top</a>)</p>


<!-- Steps to Migrate data from MySQL to S3 -->
## Steps to Migrate data from MySQL to S3

* Create an IAM role that provides read-write access to S3 and RDS. Note the arn
* Got to AWS Database Migration Services (DMS)
* Create a replication instance - db.t2.micro
* Create a source endpoint from MySQL Table and test the MySQL end point
* Create a target endpoint for S3 bucket. Test the S3 endpoint
* Create a Task to migrate the data. Select MySQL as the source endpoint and S3 as the target endpoint
* Run the Task
* A .csv should be created in the S3 bucket under the folder - EMPLOYEES/details



<p align="right">(<a href="#top">back to top</a>)</p>


<!-- Import and Export data from S3 to DynamoDB -->
## Import and Export data from S3 to DynamoDB

Create a table in DynamoDB

* Create IAM role and attach the policy created for the role
* The Lambda function is the main role of this task and it has 4 small functions
* Lambda Trigger will inject the data in DynamoDB table which was extracted from S3
* Create a Lambda Function
 ```py
import boto3
s3_client = boto3.client("s3")

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('employees')

def lambda_handler(event, context):
	bucket_name = event['Records'][0]['s3']['bucket']['name']
	s3_file_name = event['Records'][0]['s3']['object']['key']
	resp = s3_client.get_object(Bucket = bucket_name, Key = s3_file_name)
	data = resp['Body'].read().decode("utf-8")
	employees = data.split("\n")
	for emp in employees:
		print(emp)
		emp_data = emp.split(",")
		try:
			table.put_item(
			Item = {
				"id" : emp_data[0]
				"name" : emp_data[1]
				"location" : emp_data[2]
				}
			)
			except Exception as e:
				print("End of file")
 ```
* Write a Python Lambda_Function to get details form S3
* And if you execute this program the csv file which was stored in S3 will be imported to DynamoDB table
* Create a new S3 bucket and add an event to trigger the lambda function
* The reason for adding that event is whenever we upload a .csv file to a bucket then it automatically transfers the data to lambda Function which we developed above and stores the data in DynamoDB automatically.





<p align="right">(<a href="#top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

<br/>

> ### <center> ___Yaseen Shaik___ </center>
>
> * Email ID  - yaseenshaik00@gmail.com
> * Linkedin  - [www.linkedin.com/in/yaseenshaik00](www.linkedin.com/in/yaseenshaik00)
> * Twitter   - [https://twitter.com/yaseenshaik00](https://twitter.com/yaseenshaik00)
> * Github    - [https://github.com/yaseenshaik00](https://github.com/yaseenshaik00)
>  

<p align="right">(<a href="#top">back to top</a>)</p>
