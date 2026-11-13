<div align="center">

# SQL Server Data Warehouse

**An end-to-end data warehousing project built with SQL Server and Medallion Architecture**

<p>
  <img src="docs/readme-assets/architecture.svg" alt="SQL Server data warehouse architecture" width="100%">
</p>

[Architecture](#architecture) · [Data Flow](#data-flow) · [Data Model](#gold-layer-data-model) · [Project Structure](#project-structure) · [Getting Started](#getting-started)

</div>

---

## Overview

This project implements a SQL Server data warehouse that consolidates sales-related data from two source systems: **CRM** and **ERP**. The data is processed through three layers—**Bronze, Silver, and Gold**—to move from raw source data to cleaned, integrated, business-oriented data for analytical use.

The project covers source-data ingestion, data cleansing and standardization, data integration, dimensional modelling, documentation, and data-quality checks.

## Project Goals

- Build a structured data warehouse in Microsoft SQL Server.
- Ingest source data supplied as CSV files from CRM and ERP.
- Separate raw ingestion, transformation, and analytical presentation into distinct layers.
- Clean, standardize, and integrate data from multiple source systems.
- Create a Gold-layer sales data mart using a star schema.
- Document the architecture, data flow, data model, naming conventions, and Gold-layer data definitions.
- Add SQL-based data-quality checks for the Silver and Gold layers.

## Technology Stack

| Area | Technology / Approach |
|---|---|
| Database | Microsoft SQL Server |
| Query language | T-SQL |
| Data ingestion | CSV source files and SQL loading procedures |
| Architecture | Medallion Architecture (Bronze, Silver, Gold) |
| Data modelling | Dimensional modelling / Star Schema |
| Processing | Stored procedures and SQL transformations |
| Version control | Git and GitHub |

## Architecture

The warehouse follows a three-layer Medallion Architecture.

<p align="center">
  <img src="docs/readme-assets/architecture.svg" alt="Bronze, Silver and Gold architecture" width="100%">
</p>

### 1. Bronze Layer — Raw Data

The Bronze layer stores source data in its original structure.

- Receives CSV data from CRM and ERP.
- Stores raw records in source-oriented tables.
- Uses a full-load approach with truncate-and-insert processing, as documented for this project.
- Keeps transformations out of the raw ingestion layer.

### 2. Silver Layer — Cleaned and Standardized Data

The Silver layer prepares source data for integration and analysis.

- Cleans and standardizes source data.
- Applies normalization and derived-column logic where required.
- Enriches and prepares data for downstream modelling.
- Uses SQL stored procedures to load the layer.

### 3. Gold Layer — Business-Ready Data

The Gold layer presents integrated data in an analytical structure.

- Combines relevant CRM and ERP information.
- Organizes data into customer and product dimensions and a sales fact table.
- Uses a star-schema model for analytical querying.
- Provides business-oriented data for reporting and analysis.

## Source Systems

The project uses six CSV datasets across two source systems.

| Source system | Dataset file | Description |
|---|---|---|
| CRM | `cust_info.csv` | Customer information |
| CRM | `prd_info.csv` | Product information |
| CRM | `sales_details.csv` | Sales transaction details |
| ERP | `CUST_AZ12.csv` | Additional customer information |
| ERP | `LOC_A101.csv` | Customer location information |
| ERP | `PX_CAT_G1V2.csv` | Product category information |

The source-system relationship and integration documentation is available in [`docs/data_integration.png`](docs/data_integration.png).

## Data Flow

The datasets move from their source files into corresponding Bronze tables, then into Silver tables for cleaning and standardization. The prepared data is integrated into the Gold data mart.

<p align="center">
  <img src="docs/readme-assets/data-flow.svg" alt="Simplified warehouse data flow" width="100%">
</p>

The diagram shows the source-to-target lineage for the CRM and ERP entities and their movement across the three warehouse layers.

## Gold Layer Data Model

The Gold layer contains one fact table and two dimension tables.

<p align="center">
  <img src="docs/data_model.png" alt="Gold layer sales star schema" width="100%">
</p>

| Table | Type | Purpose |
|---|---|---|
| `gold.fact_sales` | Fact | Stores sales transactions and measures, with keys linking to customer and product dimensions. |
| `gold.dim_customers` | Dimension | Stores customer attributes, including demographic and geographic information. |
| `gold.dim_products` | Dimension | Stores product attributes and product classification details. |

### Relationships

- `fact_sales.customer_key` links to `dim_customers.customer_key`.
- `fact_sales.product_key` links to `dim_products.product_key`.

The fact table includes order dates, shipping and due dates, sales amount, quantity, and price. The dimensions provide descriptive context for analysing sales.

For column-level definitions, see [`docs/data_catalog.md`](docs/data_catalog.md).

## ETL Approach

The project separates ETL responsibilities across the warehouse layers:

| Layer | Load / processing approach | Main responsibility |
|---|---|---|
| Bronze | Full load; truncate and insert | Ingest raw source data |
| Silver | Full load; truncate and insert | Clean, standardize, and prepare data |
| Gold | Business-oriented presentation | Integrate data into analytical structures |

The ETL overview diagram is available in [`docs/ETL.png`](docs/ETL.png).

## Data Quality

SQL validation scripts are provided for both the Silver and Gold layers:

- [`tests/quality_checks_silver.sql`](tests/quality_checks_silver.sql)
- [`tests/quality_checks_gold.sql`](tests/quality_checks_gold.sql)

These scripts are intended to support validation of the prepared and analytical data. Run the checks after loading the corresponding layer.

## Repository Structure

```text
sql-data-warehouse-project/
│
├── datasets/
│   ├── source_crm/
│   │   ├── cust_info.csv
│   │   ├── prd_info.csv
│   │   └── sales_details.csv
│   │
│   └── source_erp/
│       ├── CUST_AZ12.csv
│       ├── LOC_A101.csv
│       └── PX_CAT_G1V2.csv
│
├── docs/
│   ├── ETL.png
│   ├── data_architecture.png
│   ├── data_catalog.md
│   ├── data_flow.png
│   ├── data_integration.png
│   ├── data_layers.pdf
│   ├── data_model.png
│   ├── naming_conventions.md
│   └── readme-assets/
│       └── architecture.svg
│
├── scripts/
│   ├── init_database.sql
│   ├── bronze/
│   │   ├── ddl_bronze.sql
│   │   └── proc_load_bronze.sql
│   ├── silver/
│   │   ├── ddl_silver.sql
│   │   └── proc_load_silver.sql
│   └── gold/
│       └── ddl_gold.sql
│
├── tests/
│   ├── quality_checks_gold.sql
│   └── quality_checks_silver.sql
│
├── LICENSE
└── README.md
```

## Getting Started

### Prerequisites

- Microsoft SQL Server.
- SQL Server Management Studio (SSMS) or another SQL Server-compatible client.
- Git, if you want to clone the repository.

### 1. Clone the repository

```bash
git clone https://github.com/AtharvBhavsar22/sql-data-warehouse-project.git
cd sql-data-warehouse-project
```

### 2. Review the project files

Before executing the scripts, review the SQL files and the source-data locations. In particular, check the file paths used by the Bronze loading procedure and update them to match the location of the cloned repository on your machine.

### 3. Execute the SQL scripts in sequence

Use SQL Server Management Studio to execute the scripts in the following order:

1. `scripts/init_database.sql`
2. `scripts/bronze/ddl_bronze.sql`
3. `scripts/silver/ddl_silver.sql`
4. `scripts/gold/ddl_gold.sql`
5. `scripts/bronze/proc_load_bronze.sql`
6. `scripts/silver/proc_load_silver.sql`

After creating the database objects and procedures, execute the Bronze and Silver loading procedures according to their definitions in the SQL files. Then run the data-quality scripts:

7. `tests/quality_checks_silver.sql`
8. `tests/quality_checks_gold.sql`

**Note:** The exact execution commands and any required SQL Server permissions depend on the implementation in the scripts. Review the procedure definitions before running them. The repository documentation should be treated as the guide to the intended layer responsibilities.

## Documentation

| Document | Description |
|---|---|
| [High-Level Architecture](docs/data_architecture.png) | Overview of the source systems and warehouse layers |
| [Data Flow](docs/data_flow.png) | Source-to-target lineage across the layers |
| [Data Integration](docs/data_integration.png) | Relationships between CRM and ERP entities |
| [Gold Data Model](docs/data_model.png) | Sales star schema |
| [ETL Methods](docs/ETL.png) | ETL concepts and processing methods |
| [Data Layers](docs/data_layers.pdf) | Responsibilities and characteristics of Bronze, Silver, and Gold |
| [Data Catalog](docs/data_catalog.md) | Gold-layer table and column definitions |
| [Naming Conventions](docs/naming_conventions.md) | Naming standards for warehouse objects |

## Key Concepts Demonstrated

- SQL Server database and schema organization.
- CSV-based source-data ingestion.
- Full-load ETL processing.
- Bronze, Silver, and Gold layer separation.
- Data cleansing and standardization.
- Integration of CRM and ERP datasets.
- Fact and dimension table design.
- Star-schema dimensional modelling.
- SQL-based data-quality validation.
- Technical documentation and Git version control.

## Acknowledgements

This project was developed as a learning and portfolio project using concepts studied through the **Data With Baraa SQL Data Warehouse course** by Baraa Khatib Salkini.

The course materials and original creator-branded reference diagrams are acknowledged as learning references. Project-specific SQL implementation, organization, and documentation should be attributed according to their actual authorship.

## License

See the [`LICENSE`](LICENSE) file for the license terms that accompany this repository. Retain the original copyright and attribution notices when redistributing material covered by that license.
