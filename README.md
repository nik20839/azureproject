# End-to-End Azure Data Engineering Project

## Project Overview

This project implements an end-to-end data engineering solution using Azure Data Factory, Azure Data Lake Storage Gen2, Azure Databricks, Delta Lake, and Azure SQL Database.

The solution follows the Medallion Architecture (Bronze, Silver, and Gold) and demonstrates incremental data ingestion, data transformation, streaming processing, Slowly Changing Dimensions (SCD Type 2), and deployment using Databricks Asset Bundles.

### Data Flow

Azure SQL Database → Azure Data Factory → ADLS Gen2 (Bronze) → Azure Databricks (Silver) → Delta Lake (Gold)

## Key Features

- Metadata-driven incremental ingestion using Azure Data Factory
- Parameterized processing of multiple source tables
- Watermark/CDC-based incremental data extraction
- Bronze layer storage in ADLS Gen2
- Incremental file processing using Databricks Auto Loader
- Data cleaning and transformation in the Silver layer
- Delta Lake tables for reliable data storage
- Gold-layer dimensional data model
- SCD Type 2 implementation for historical dimension tracking
- Fact and dimension table processing
- Databricks Asset Bundles for deployment and environment management
- Git/GitHub for source control

## Solution Architecture

The project follows a Medallion Architecture to progressively ingest, transform, and model data across Bronze, Silver, and Gold layers.

### Bronze Layer — Ingestion
- Azure SQL Database acts as the source system.
- Azure Data Factory performs metadata-driven incremental ingestion.
- ADF uses CDC/watermark columns to identify newly added or updated records.
- Multiple tables are processed dynamically using a parameterized ForEach pipeline.
- Incremental data is stored as Parquet files in the Bronze container of ADLS Gen2.

### Metadata-Driven Incremental Ingestion

The ADF pipeline uses a parameterized ForEach loop to process multiple source tables dynamically. Each iteration retrieves the previous watermark, extracts incremental records from Azure SQL, and conditionally processes newly available data.

<img width="900" alt="image" src="https://github.com/user-attachments/assets/f6235132-e7b0-42ba-8e1a-0b40e58ad97d" />

<img width="900" alt="image" src="https://github.com/user-attachments/assets/de7e55a2-d165-407f-8c17-0162f5b80128" />



### Silver Layer — Transformation
- Databricks Auto Loader incrementally detects and processes new Bronze files.
- PySpark transformations clean and standardize the incoming data.
- Structured Streaming and checkpoints maintain incremental processing state.
- Processed data is stored as Delta tables in the Silver layer.

### Gold Layer — Data Modeling
- Silver Delta tables are used as sources for Gold-layer processing.
- Dimension and fact tables are created for analytical consumption.
- SCD Type 2 is implemented for selected dimensions to preserve historical changes.
- Databricks AUTO CDC handles changes based on business keys and sequencing columns.

### Deployment & Source Control
- Azure Data Factory is integrated with GitHub for source control and collaborative development.
- The `main` branch is configured as the ADF collaboration branch.
- ADF development changes are version-controlled through feature/development branches before being merged into main.
- The `adf_publish` branch is used by Azure Data Factory to store generated deployment artifacts after publishing.
- Databricks Asset Bundles are used to define and deploy Databricks resources.
- A development (`dev`) target is configured for Databricks bundle deployment.
- Bundle validation is performed before deployment using the Databricks CLI.
