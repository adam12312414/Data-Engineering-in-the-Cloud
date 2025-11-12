# ☁️ AWS SingStat Data Pipeline – Individual Solution  
**Author:** Adam Haizad Bin Mohamad Faizal (2302276G)  
**Course:** Diploma in Big Data & Analytics  
**Module:** Data Engineering and Cloud Computing (DAEC)  

---

## 🧭 Project Overview  
This project implements an **automated AWS data pipeline** for **Singapore Department of Statistics (SingStat)** datasets.  
The pipeline ingests JSON data via API Gateway and processes it through multiple AWS services — including Lambda, EC2, Glue, DataBrew, Athena, S3, CloudWatch, and Power BI — to enable end-to-end data transformation, analysis, and visualization.  

The goal is to create a scalable, secure, and fully automated data flow that handles both small and large datasets efficiently while maintaining data integrity and minimizing manual effort.

---

## 🧩 Architecture Components  

### 1️⃣ **API Gateway**
- Acts as the entry point for users to trigger the data pipeline.
- Three APIs were created to handle **three different datasets**.

---

### 2️⃣ **Lambda Functions**

#### 🟦 **Singstat Function**
- Main function that downloads JSON data from SingStat API.  
- Uploads data into the **S3 “untransformedsingstatdata”** folder (monthly, quarterly, or yearly).  
- If dataset is large, it invokes `ProcessingLargeDatasetSingstat`.  
- Automates data cleaning by calling the `AutomateGlueCrawlerAndDatabrew` function next.

#### 🟩 **ProcessingLargeDatasetSingstat**
- Launches an **EC2 instance** to process large datasets in parallel.  
- Splits data into manageable chunks and uploads them to S3.  
- Automatically invokes the Glue/DataBrew function once data upload completes.  

#### 🟨 **AutomateGlueCrawlerAndDatabrew**
- Performs AWS Glue crawls and DataBrew transformations.  
- Loads processed data into the **transformed_singstat_data** database.  
- Executes **Athena SQL queries** (e.g., `SELECT * FROM monthly`) to verify the results.  
- Ensures transformed data is queryable and properly cataloged.  

#### 🟧 **SingstatNotifications**
- Generates a **daily cost report** using AWS Cost Explorer.  
- Uploads Power BI visualizations and cost summaries to S3.  
- Sends **email notifications via SNS** with pre-signed URLs to the reports.  

---

### 3️⃣ **S3 Buckets**
- Stores both raw and transformed data.  
- **Versioning enabled** to prevent accidental overwrites.  
- **AWS KMS encryption** applied to protect sensitive information both at rest and in transit.  

---

### 4️⃣ **Quicksight / Power BI**
- Athena query results visualized using Power BI via ODBC driver.  
- The visualizations are uploaded to the S3 folder `PowerBIChartCostExplorerReport` and shared with clients through **SNS notifications**.  

---

### 5️⃣ **CloudWatch**
- Created **alarms** for all Lambda functions monitoring:
  - Errors  
  - Throttles  
  - Duration  
- CloudWatch dashboard tracks real-time Lambda performance and triggers alerts via **SNS**.  
- Added a **CloudWatch Rule** to trigger the SingStat Lambda function annually, ensuring data is always refreshed.

---

### 6️⃣ **CloudTrail**
- Tracks user and API activity across AWS regions.  
- Uses log file validation for audit and compliance.  
- Stores logs in an S3 bucket (`aws-cloudtrail-logs-609225191640-576709e6`) with **Insights** enabled to detect anomalies.

---

### 7️⃣ **Key Management Service (KMS)**
- Custom encryption key (`Singstat-encryption-key`) used across S3, Athena, and DataBrew.  
- Ensures consistent and secure encryption policies across all services.

---

## 👩‍💻 User Workflow
1. User triggers the **SingStat API Gateway** endpoint.  
2. The **Lambda function** downloads and uploads raw data to the correct S3 folder (monthly, quarterly, yearly).  
3. For large datasets, EC2 handles heavy processing before returning to Lambda.  
4. The **Glue & DataBrew** job transforms and catalogs the dataset.  
5. **Athena** queries the processed data.  
6. **Power BI** retrieves the Athena results for visualization via ODBC.  
7. The **Notifications Lambda** emails pre-signed report links and Power BI charts to the client.

---

## ⚙️ Automation Summary
| AWS Service | Purpose | Automation |
|--------------|----------|------------|
| API Gateway | Entry point for pipeline | Trigger Lambda functions |
| Lambda | Main compute logic | Automates processing & notifications |
| EC2 | Handles large dataset processing | Invoked only when needed |
| Glue & DataBrew | Data transformation | Triggered by Lambda |
| Athena | Query transformed data | SELECT queries automated |
| CloudWatch | Monitor pipeline | Triggers alarms & dashboards |
| CloudTrail | Track user activity | Continuous logging |
| SNS | Email alerts | Automatic report distribution |
| KMS | Data encryption | Shared across services |

---

## 📊 Visualization Output
- Data from Athena is visualized in **Power BI dashboards**.  
- Reports include both **SingStat metrics** and **AWS cost summaries**.  
- Pre-signed URLs ensure temporary access for clients (7-day expiry).

---

## 📘 SDL Learning Journal (PPMR Framework)

### 🧩 Task 1 – Implementing AWS Lambda for Data Ingestion
- **Plan:** Use AWS Lambda to ingest SingStat JSON data into S3.  
- **Perform:** Modified and executed code successfully.  
- **Monitor:** Encountered missing `requests` library error → Resolved by packaging dependency manually.  
- **Reflect:** Learned how to deploy third-party libraries and integrate EC2 for large-scale ingestion.

---

### 🧩 Task 2 – Querying & Debugging in Athena
- **Plan:** Test Athena queries on transformed data.  
- **Perform:** Data did not appear in Athena; used ChatGPT and AWS docs for troubleshooting.  
- **Monitor:** Identified an unsaved folder interfering with Glue crawler.  
- **Reflect:** Learned the importance of inspecting all components — even minor S3 folders — as they can impact the entire pipeline.

---

## 🧠 Key Learnings
- Packaging and deploying libraries in AWS Lambda.  
- Automating end-to-end data ingestion and transformation using serverless services.  
- Integrating Lambda, EC2, and Glue for efficient scaling.  
- Troubleshooting complex multi-service pipelines using logs and CloudWatch metrics.  
- Maintaining cost visibility and transparency with automated Power BI reporting.  

---

## 🔐 Security & Compliance
- Enforced **data encryption** with AWS KMS.  
- Enabled **CloudTrail logging** with validation for audit readiness.  
- **Versioning and IAM roles** configured for least-privilege access.  

---

## 🧰 Tools & Technologies
| Category | Tools Used |
|-----------|-------------|
| Cloud Services | AWS Lambda, EC2, S3, Glue, DataBrew, Athena, CloudWatch, CloudTrail, SNS |
| Visualization | Power BI (ODBC connection) |
| Security | AWS KMS |
| Programming | Python (boto3, requests) |
| Development | AWS Academy Learner Lab |

---

## 🚀 Outcome
The completed AWS SingStat pipeline successfully:
- Automates data ingestion, transformation, and visualization.  
- Handles both small and large datasets intelligently.  
- Provides real-time notifications, cost insights, and visual dashboards.  
- Ensures high security, reliability, and scalability across all components.

---

## 👨‍💻 Author
**Adam Haizad Bin Mohamad Faizal**  
Temasek Polytechnic – School of Informatics & IT   

---

## 📚 References
- OpenAI. (2023). *ChatGPT (Sep 25 version)* [Large language model].  
- AWS Academy Learner Lab documentation  
- GeeksforGeeks, AWS Docs, and YouTube tutorials (for troubleshooting)  

---
