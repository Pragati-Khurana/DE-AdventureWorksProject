# Adventure Works Data Engineering Project

## Overview

This project demonstrates an end-to-end **modern data engineering pipeline** built using Azure services, leveraging Adventure Works sample data. It automates ingestion, transformation, and preparation of sales-related data for reporting and analytics.

The architecture follows the **Bronze → Silver → Gold** data lake pattern and uses Azure Data Factory, Azure Databricks, Azure Synapse Analytics, and Azure Data Lake Storage (ADLS).

---

## Tools & Technologies

- **Azure Data Factory (ADF)** – Data ingestion from GitHub to ADLS
- **Azure Data Lake Storage (ADLS)** – Scalable data storage (Bronze, Silver, Gold zones)
- **Azure Databricks (ADB)** – Data transformation and cleansing
- **Azure Synapse Analytics** – Data modeling, external table creation, and Power BI connectivity

---

## Pipeline Flow

### 1. **Data Ingestion (ADF → ADLS Bronze)**

- ADF pipeline reads multiple `.csv` files hosted on GitHub.
- A **Lookup** activity fetches an array of:
  - `relativeURL` (file location)
  - `sinkFolderName` (target folder in ADLS)
  - `sinkFileName` (target file name)
- A **ForEach** activity iterates over the array:
  - Inside ForEach: a **Copy** activity pulls data via HTTP (GitHub) and dumps it into the appropriate **ADLS Bronze container** folder.

### 2. **Data Transformation (ADB → ADLS Silver)**

- Azure Databricks Notebook connects to ADLS using a registered application.
- Each file from the Bronze layer is:
  - Loaded into a DataFrame
  - Transformed (e.g., merging yearly sales, cleansing, standardization)
  - Written to **ADLS Silver container**

### 3. **Data Modeling & Serving (Synapse → ADLS Gold)**

- Synapse workspace contains:
  - External **views** to read data from the Silver container
  - **External tables** that write refined data to the **Gold layer**
- These external tables are exposed via **Azure Synapse Analytics Serverless SQL endpoint**, ready to be consumed by reporting tools like **Power BI**

>  **Note**: Power BI report is not included but the Synapse endpoint is provided for direct reporting.

## Data Used

Sample `.csv` files based on Adventure Works dataset:

- `Sales_2015.csv`
- `Sales_2016.csv`
- `Sales_2017.csv`
- `Customers.csv`
- `Products.csv`
- `ProductSubCategories.csv`
- `Calendar.csv`
- `Territories.csv`
- `Returns.csv`

These files are hosted on GitHub and ingested using the ADF pipeline.

---

## Project Structure

<pre lang="text"> adventure-works-data-engineering/
│
├── data-ingestion/
│ └── adf-pipeline/ (Lookup + ForEach + Copy to Bronze)
│
├── data-transformation/
│ └── databricks-notebook/ (Load from Bronze, transform, write to Silver)
│
├── data-modeling/
│ └── synapse-scripts/ (Create views, external tables, write to Gold) </pre>
