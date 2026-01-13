
# Data Ingestion from S3 to RDS with Fallback to AWS Glue using Dockerized Python

## Objective
This project demonstrates an automated data ingestion pipeline that:
- Reads CSV data from Amazon S3
- Pushes the data into an RDS (MySQL-compatible) database
- Falls back to AWS Glue Data Catalog if RDS is unavailable
- Is fully Dockerized for easy deployment

---

## Architecture

S3 → Dockerized Python App → RDS (Primary)
                    ↓
                AWS Glue (Fallback)

---

## AWS Services Used
- Amazon S3
- Amazon RDS (MySQL)
- AWS Glue
- IAM
- Docker

---

## Project Structure

s3-rds-glue-project/
│
├── ingest.py
├── Dockerfile
├── requirements.txt
└── README.md

---

## Step 1: AWS Setup

### Create S3 Bucket
Upload a CSV file like `students.csv`.

Example:
id,name,age,city
1,Suraj,25,Pune
2,Rahul,24,Mumbai

---

### Create RDS MySQL
- DB Name: testdb
- Username: admin
- Password: password
- Public Access: Yes

---

### Create Glue Database
Name: fallback_db

---

### IAM Permissions
- AmazonS3FullAccess
- AmazonRDSFullAccess
- AWSGlueConsoleFullAccess

---

## Step 2: Python Script

The script:
- Reads CSV from S3
- Pushes data to RDS
- On failure, creates Glue Table

File: ingest.py

---

## Step 3: requirements.txt

boto3
pandas
sqlalchemy
pymysql

---

## Step 4: Dockerfile

FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY ingest.py .
CMD ["python", "ingest.py"]

---

## Step 5: Build Docker Image

docker build -t s3-rds-glue .

---

## Step 6: Run Docker Container

docker run -e AWS_ACCESS_KEY_ID=xxxx -e AWS_SECRET_ACCESS_KEY=xxxx -e AWS_REGION=us-east-1 -e S3_BUCKET=my-bucket -e S3_KEY=students.csv -e RDS_HOST=mydb.xxxxx.us-east-1.rds.amazonaws.com -e RDS_USER=admin -e RDS_PASSWORD=password -e RDS_DB=testdb -e RDS_TABLE=students -e GLUE_DB=fallback_db -e GLUE_TABLE=students_fallback -e GLUE_S3_LOCATION=s3://my-bucket/ s3-rds-glue

---

## Output

### Success Case
Data successfully inserted into RDS

### Failure Case
Fallback triggered → Glue table created

---

## Verification

### RDS
SELECT * FROM students;

### Glue
AWS Glue → Databases → fallback_db → Tables

---

## Challenges
- IAM permission issues
- RDS connectivity
- JDBC/SQLAlchemy configuration

---

## Conclusion
This project demonstrates a robust, fault-tolerant ETL pipeline using AWS services and Docker.

---

## Author
Suraj Molke
