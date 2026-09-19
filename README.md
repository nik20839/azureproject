# Azure Databricks Medallion Architecture – Spotify Data Engineering Project

This project implements the Databricks processing layer of an end-to-end Azure Data Engineering solution using a Medallion Architecture.

Data is incrementally ingested from Azure SQL Database using Azure Data Factory and stored in the Bronze layer in Azure Data Lake Storage Gen2. Azure Databricks processes the Bronze data using Auto Loader, Spark Structured Streaming, Delta Lake, and PySpark transformations to build curated Silver and Gold layers.

The solution also includes Unity Catalog for data governance, reusable transformation utilities, metadata-driven SQL generation using Jinja templates, and Databricks Asset Bundles for organizing and managing Databricks resources.

## Data Flow

Azure SQL Database → Azure Data Factory → ADLS Gen2 (Bronze) → Azure Databricks → Silver → Gold

## Architecture & Technologies

- **Azure SQL Database** – Source system containing fact and dimension data.
- **Azure Data Factory (ADF)** – Performs metadata-driven incremental ingestion from Azure SQL using watermark/CDC columns.
- **Azure Data Lake Storage Gen2 (ADLS)** – Stores data across Bronze, Silver, and Gold layers.
- **Azure Databricks** – Performs scalable data processing and transformations using PySpark.
- **Auto Loader** – Incrementally discovers and processes new files arriving in the Bronze layer.
- **Spark Structured Streaming** – Processes incoming Bronze data incrementally using checkpoint-based state management.
- **Delta Lake** – Provides reliable Delta tables for the Silver and Gold layers.
- **Unity Catalog** – Provides centralized table management and data governance.
- **Jinja Templates** – Generates metadata-driven SQL for reusable business views.
- **Databricks Asset Bundles** – Organizes Databricks source code and deployment configuration using a structured project layout.

  
## Bronze to Silver Processing

The Bronze layer contains raw incremental data ingested from Azure SQL Database by Azure Data Factory and stored in ADLS Gen2.

Azure Databricks processes the Bronze data into the Silver layer using:

- **Databricks Auto Loader** to incrementally discover newly arrived Parquet files.
- **Spark Structured Streaming** to process incoming data incrementally.
- **Checkpointing** to track processed files and prevent unnecessary reprocessing.
- **Schema evolution and rescued data handling** to manage unexpected schema changes.
- **PySpark transformations** for data cleaning, column transformations, and duplicate removal.
- **Reusable transformation utilities** to avoid repeating common transformation logic.
- **Delta Lake** to persist the transformed Silver datasets.
- **Unity Catalog** to register and govern the Silver tables.

## Metadata-Driven Business Views

To avoid manually writing separate SQL queries for different business requirements, the project implements a metadata-driven SQL generation approach using Jinja templates.

- Table names, aliases, required columns, and join conditions are defined as metadata.
- Jinja templates dynamically generate SQL based on the supplied metadata.
- The FactStream table acts as the base fact table and is enriched using dimension tables such as DimUser and DimTrack.
- Generated SQL is executed using Spark SQL to create reusable business datasets.
- This approach reduces repetitive SQL development and makes the transformation logic easier to extend for different business requirements.

## Gold Layer

The Gold layer transforms curated Silver data into business-ready fact and dimension tables for analytics and reporting.

- **DimUser** – Contains curated user information.
- **DimTrack** – Contains track-level descriptive information.
- **DimDate** – Provides date attributes for analytical queries.
- **FactStream** – Stores streaming/listening events and connects to the dimension tables.
- Gold transformations are organized using **Databricks Lakeflow Declarative Pipelines (DLT)**.
- The Gold layer follows a dimensional modeling approach to provide analytics-ready datasets.

## Project Structure

```text
spotify_dab/
├── src/
│   ├── silver/
│   │   └── silver_dimensions.ipynb
│   ├── jinja/
│   │   └── jinja_notebook.ipynb
│   └── gold/
│       └── dlt/
│           ├── transformations/
│           │   ├── DimDate.py
│           │   ├── DimTrack.py
│           │   ├── DimUser.py
│           │   └── FactStream.py
│           └── exploration/
│               └── sample_exploration.ipynb
├── utils/
│   └── transformations.py
├── databricks.yml
└── pyproject.toml

```

  ## Key Engineering Features

- End-to-end Medallion Architecture using Bronze, Silver, and Gold layers.
- Parameterized incremental ingestion from Azure SQL using Azure Data Factory with watermark-based CDC logic.
- Incremental Bronze-to-Silver processing using Databricks Auto Loader.
- Checkpoint-based processing to track ingestion progress and avoid unnecessary reprocessing.
- Data cleaning and transformation using reusable PySpark utilities.
- Delta Lake storage with Unity Catalog governance.
- Metadata-driven SQL generation using Jinja templates.
- Dimensional modeling with fact and dimension tables in the Gold layer.
- Databricks Lakeflow Declarative Pipelines for Gold-layer processing.
- Databricks Asset Bundle structure for organizing project code and resources.
