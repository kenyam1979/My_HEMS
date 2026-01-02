
# 1. Overview
## 1.1 Data pipeline

Manual CSV upload --> AWS S3 --> AWS Glue (ETL) --> Athena --> Queried by Grafana

# 2. Specification

### 2.1 Manual CSV upload 
1. Download CSV file from くらしのtepco
2. Convert files to UTF [TODO: This to be automated on ETL]

### 2.2 AWS S3 
1. Upload on a S3 bucket

## 2.2.1 Folder structure
```
tepco_data/
├── raw/
│   ├── 2023_comparison.csv
│   ├── 2024_comparison.csv
│   ├── 2025_comparison.csv
│   └── ...
└── data/
    ├── (auto generated name).parquet
    ├── (auto generated name).parquet
    ├── (auto generated name).parquet
    └── ...
```

### 2.3 AWS Glue (ETL)
1. Run a job manually

## 2.3.1 ETL process
1. Purge files on data folder
2. Load data from S3
3. Transform data (remove comma from numeric text)
4. Change schema (remove unnessessary fields and change data type to bigint)
5. Save as parquet files on data folder


### 2.4 Athena
### 2.5 Queried by Grafana
1. Query from Grafana using Athena plugin