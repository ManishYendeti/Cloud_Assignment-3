# ITCS-6190 Assignment 3: AWS Data Processing Pipeline
**Course:** Cloud Computing for Data Analysis (ITCS 6190/8190, Fall 2025)
**Instructor:** Marco Vieira
**Student:** Sai Tarun Vedagiri (ID: 801421332)
---
This project builds a complete serverless data pipeline on AWS. The workflow takes raw data from S3,
processes it through Lambda, catalogs it using AWS Glue, and visualizes results on a web app hosted
on an EC2 instance.
## 1. Amazon S3 Bucket Setup ■
Create an S3 bucket with these folders:
* `raw/` – for uploading original CSV files.
* `processed/` – for Lambda output files.
* `enriched/` – for Athena query results.
---
## 2. IAM Roles and Permissions ■
Create three roles for Lambda, Glue, and EC2.
### Lambda Role
- Go to **IAM > Roles > Create Role**.
- Choose **Lambda** as the service.
- Attach:
* AWSLambdaBasicExecutionRole
* AmazonS3FullAccess
- Name it `Lambda-S3-Processing-Role`.
### Glue Role
- Choose **Glue** as the service.
- Attach:
* AmazonS3FullAccess
* AWSGlueConsoleFullAccess
* AWSGlueServiceRole
- Name it `Glue-S3-Crawler-Role`.
### EC2 Role
- Choose **EC2** as the service.
- Attach:
* AmazonS3FullAccess
* AmazonAthenaFullAccess
- Name it `EC2-Athena-Dashboard-Role`.
---
## 3. Lambda Function ■■
This function processes CSV files uploaded to `raw/`.
Steps:
1. Open **Lambda** and click **Create function**.
2. Name: `FilterAndProcessOrders`.
3. Runtime: Python 3.9 or newer.
4. Use the `Lambda-S3-Processing-Role`.
5. Replace default code with your processing script (`LambdaFunction.py`).
---
## 4. Add S3 Trigger ■
To automate Lambda execution:
- Add a trigger from S3.
- Choose the bucket and select **All object create events**.
- Prefix: `raw/`, Suffix: `.csv`.
- Confirm and save.
Upload `Orders.csv` to the `raw/` folder — Lambda will run automatically.
---
## 5. Create Glue Crawler ■■
This crawler scans processed files and creates a table for Athena queries.
1. Go to **Glue > Crawlers > Create crawler**.
2. Source: S3 `processed/` folder.
3. Role: `Glue-S3-Crawler-Role`.
4. Output: Create database `orders_db`.
5. Run crawler to generate the table.
---
## 6. Query with Amazon Athena ■
Open **Athena**, choose `orders_db`, and run queries like:
- Total sales per customer.
- Monthly order count and revenue.
- Orders by status.
- Average Order Value per customer.
- Top 10 largest orders in February 2025.
---
## 7. Launch EC2 Web Server ■■
Steps:
1. Launch a new EC2 instance.
2. OS: Amazon Linux 2023 AMI.
3. Instance type: `t2.micro`.
4. Allow ports `22` (SSH) and `5000` (Web App).
5. Attach role: `EC2-Athena-Dashboard-Role`.
6. Launch and connect via SSH.
---
## 8. Connect to EC2
```bash
ssh -i /path/to/key.pem ec2-user@
```
---
## 9. Set Up Environment
```bash
sudo yum update -y
sudo yum install python3-pip -y
pip3 install Flask boto3
```
---
## 10. Build the Web App
1. Create a file:
```bash
nano app.py
```
2. Paste `EC2InstanceNANOapp.py` code.
3. Update variables:
- AWS_REGION
- ATHENA_DATABASE
- S3_OUTPUT_LOCATION
4. Save and exit.
---
## 11. Run and View Dashboard ■
```bash
python3 app.py
```
Open in browser:
```
http://:5000
```
---
## Final Notes
- Stop Flask: `Ctrl + C` in terminal.
- To avoid charges, **stop or terminate** your EC2 instance after use.
