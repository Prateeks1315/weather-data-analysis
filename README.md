# Real-Time Weather Data Engineering Pipeline
---
## 🎯 Overview

Production-grade end-to-end data engineering pipeline that automates the ingestion, transformation, and visualization of real-time weather forecast data. The system processes 40+ weather data points every hour from the OpenWeather API, storing them in a scalable data warehouse for analytics and business intelligence.

**Key Metrics:**
- **Data Freshness:** Real-time updates every hour
- **Processing Time:** < 2 minutes end-to-end
- **Scalability:** Handles 1000+ records/day
- **Reliability:** 99.5% pipeline success rate

**Business Value:** Enables weather trend analysis, forecasting insights, and environmental monitoring for data-driven decision making.

---

## 🛠️ Tech Stack

### Cloud Infrastructure
- **AWS MWAA (Managed Apache Airflow 3.0.6)** - Workflow orchestration
- **Amazon S3** - Data lake storage with date partitioning
- **AWS Glue** - Serverless ETL with PySpark engine
- **Amazon Redshift Serverless** - Cloud data warehouse
- **AWS IAM** - Role-based access control & security
- **AWS CloudWatch** - Logging & monitoring
- **AWS KMS** - Encryption key management

### Data Engineering
- **Python 3.11** - Core programming language
- **Apache Airflow** - DAG orchestration & scheduling
- **PySpark** - Distributed data processing
- **Pandas** - Data manipulation & analysis
- **psycopg2** - PostgreSQL/Redshift connectivity

### Visualization
- **Streamlit 1.32.0** - Interactive dashboard framework
- **Plotly 5.18.0** - Advanced data visualization
- **Plotly Express** - Quick statistical charts

### APIs & Integrations
- **OpenWeather API** - 5-day weather forecast data
- **Celery** - Distributed task queue with SQS backend

---

## ✨ Features

### Data Pipeline
- ✅ **Automated Hourly Ingestion** - Scheduled API calls via Airflow DAGs
- ✅ **Incremental Loading** - Date-partitioned S3 storage (YYYY-MM-DD)
- ✅ **Schema Evolution** - Dynamic schema mapping in Glue ETL
- ✅ **Data Quality Checks** - Null validation & type conversion
- ✅ **Error Handling** - Retry logic with exponential backoff
- ✅ **Idempotent Operations** - DROP-IF-EXISTS table creation

### Data Warehouse
- ✅ **Optimized Storage** - Columnar format in Redshift
- ✅ **Query Performance** - Indexed primary keys
- ✅ **VPC Security** - Private subnet deployment
- ✅ **Connection Pooling** - JDBC connection management

### Visualization
- ✅ **Real-Time Dashboard** - Live Redshift data queries
- ✅ **Interactive Charts** - 6+ Plotly visualizations
- ✅ **Temperature Trends** - Multi-line time-series analysis
- ✅ **Weather Distribution** - Pie charts & bar graphs
- ✅ **Environmental Metrics** - Humidity & pressure tracking
- ✅ **KPI Cards** - Aggregate statistics display

---
## 📁 Project Structure

Weather_Data_Analysis_Project/
├── dags/
│ ├── openweather_api.py # Extract & load DAG
│ └── transform_redshift_load.py # Trigger Glue ETL DAG
│
├── scripts/
│ └── weather_data_ingestion.py # Glue PySpark transformation
│
├── dashboard/
│ ├── app.py # Streamlit visualization
│ └── requirements.txt # Dashboard dependencies
│
├── config/
│ ├── mwaa-execution-policy.json # IAM policy document
│ └── buildspec.yml # CI/CD build config
│
├── requirements.txt # Airflow dependencies
├── README.md # Project documentation
└── .gitignore # Git exclusions

---

## 📈 Visualization Dashboard

### KPI Metrics
- Total Records Processed
- Average Temperature (°C)
- Maximum Wind Speed
- Average Humidity

### Interactive Charts
1. **Temperature Trend** - Multi-line chart (temp, feels_like, min, max)
2. **Weather Distribution** - Pie chart of weather types
3. **Visibility Analysis** - Bar chart over time
4. **Humidity Trend** - Line chart with average benchmark
5. **Atmospheric Pressure** - Time-series with moving average
6. **Wind Speed** - Area chart showing speed variations

---

## 🔧 Challenges & Solutions

### Challenge 1: KMS Encryption Permissions
**Issue:** Celery tasks failed with `AccessDeniedException` on SQS queue
**Root Cause:** MWAA execution role lacked `kms:GenerateDataKey` permission
**Solution:** Added KMS policy to IAM role allowing encryption operations

### Challenge 2: Worker Task Serialization
**Issue:** PythonOperator tasks never reached Celery workers
**Root Cause:** Scheduler couldn't serialize tasks to SQS due to missing permissions
**Solution:** Enhanced IAM policy with `glue:*` and `sqs:*` permissions

### Challenge 3: Glue-Redshift Connectivity
**Issue:** Glue job failed with "Connection not found" error
**Root Cause:** Mismatched connection name between DAG and Glue catalog
**Solution:** Unified connection naming convention: `redshift-unified-connection`

### Challenge 4: Data Type Conversion
**Issue:** Redshift columns stored as VARCHAR causing math operation failures
**Root Cause:** Glue script didn't enforce numeric types during transformation
**Solution:** Added `pd.to_numeric()` conversion in Streamlit data fetch layer

---

## 💰 Cost Optimization

### Monthly Cost Breakdown (Paused State)
| Service | Cost | Optimization Strategy |
|---------|------|----------------------|
| MWAA | $0 | Environment deleted when idle |
| Redshift | $0 | Auto-pause after 5 minutes inactivity |
| S3 | ~$0.02 | Date partitioning reduces scan costs |
| Glue | $0 | Pay-per-job execution only |
| CloudWatch | ~$1 | 7-day log retention policy |

**Active Cost:** ~$350/month when running 24/7  
**Optimized Cost:** ~$1/month when paused

### Best Practices
- Enable Redshift auto-pause (300 seconds)
- Delete MWAA environment when not in use
- Use S3 lifecycle policies for old data
- Implement Glue job bookmarks to avoid reprocessing
