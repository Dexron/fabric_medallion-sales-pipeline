# Microsoft Fabric Medallion Architecture — Sales Data Pipeline

An end-to-end data engineering pipeline built in Microsoft Fabric that ingests sales files from Azure Blob Storage, transforms them through Bronze, Silver, and Gold layers, and delivers reporting-ready dimensional models via a semantic model.

> **Tech stack:** Microsoft Fabric · Azure Blob Storage · PySpark · Delta Lake · OneLake · Fabric Pipelines · Semantic Model

---

## Architecture Overview

```
Azure Blob Storage
       │
       │  (OneLake Shortcut)
       ▼
┌─────────────────────────────────────────────┐
│              Microsoft Fabric Lakehouse      │
│                                             │
│  ┌──────────┐   ┌──────────┐   ┌─────────┐ │
│  │  Bronze  │──▶│  Silver  │──▶│  Gold   │ │
│  │  (Raw)   │   │ (Cleaned)│   │(Reporting│ │
│  └──────────┘   └──────────┘   └─────────┘ │
└─────────────────────────────────────────────┘
       │
       ▼
  Semantic Model
       │
       ▼
  Dashboards / BI Reporting
```

**Trigger:** A new file lands in Blob Storage → Pipeline fires automatically → Data flows Bronze → Silver → Gold → File moves to `Processed/` or `Error/`

---

## Layers

### Bronze — Raw Ingestion
- Reads source files via the Lakehouse shortcut to Azure Blob Storage
- Archives raw files for replay safety and auditability
- Loads raw rows into `sales_bronze_raw` Delta table with ingestion metadata

### Silver — Cleansing & Standardisation
- Parses mixed date formats
- Standardises and validates fields
- Merges at the correct transaction grain: `SalesOrderNumber` + `SalesOrderLineNumber`
- Stores the current trusted transactional state in `sales_silver3`

### Gold — Dimensional Modelling
- Builds dimension and fact tables for reporting
- Applies **SCD Type 2** on `dimcustomer_gold` to preserve customer history (e.g. name changes)
- Outputs: `dimcustomer_gold`, `dimproduct_gold`, `dimdate_gold`, `factsales_gold`

---

## Tables

| Layer  | Table                |
|--------|----------------------|
| Bronze | `sales_bronze_raw`   |
| Silver | `sales_silver`      |
| Gold   | `dimcustomer_gold`  |
| Gold   | `dimproduct_gold`   |
| Gold   | `dimdate_gold`      |
| Gold   | `factsales_gold`    |

---

## Pipeline & Automation

The Fabric Pipeline orchestrates the following steps in sequence:

1. **Silver notebook** — cleanse and merge incoming data
2. **Gold notebook** — build/update dimension and fact tables
3. **Copy activity** — move successful source files to `Processed/`
4. **Copy activity** — move failed source files to `Error/`

A **Blob Storage trigger** fires the pipeline automatically whenever a new file is uploaded — no manual execution required.

---

## Key Design Decisions

### Replay-Safe Raw Data Handling
Raw data history is preserved in Bronze archive folders and Delta tables with metadata — not just through file routing. This means the pipeline can be safely restarted, reprocessed, or audited without data loss.

### Correct Transaction Grain
All Silver and Gold merge logic is scoped to `SalesOrderNumber` + `SalesOrderLineNumber`. This prevents duplicate rows and handles corrected file deliveries cleanly.

### SCD Type 2 for Customer History
Customer dimension changes (e.g. name updates) are handled with Slowly Changing Dimension Type 2 logic. Old versions are retained in history; one active record always exists per customer. Reporting can filter on current or historical state.

---

## Example: Corrected File Delivery

If `Sales_2020_20260404_V1.csv` is replaced by `Sales_2020_20260405_V2.csv`:

- The original raw file is preserved in Bronze history
- Silver updates the affected transaction rows
- Gold applies SCD2 where customer attributes changed
- The semantic model reflects the corrected reporting output

---

## Project Structure

```
/
├── notebooks/
│   ├── silver_notebook.ipynb       # Cleansing, standardisation, merge
│   └── gold_notebook.ipynb         # Dimensional modelling, SCD2
├── pipeline/
│   └── sales_pipeline.json         # Fabric Pipeline definition
├── semantic_model/
│   └── sales_model.bim             # Semantic model definition
└── README.md
```

---

## Technologies

| Tool | Purpose |
|------|---------|
| Microsoft Fabric | Unified analytics platform |
| Azure Blob Storage | Source system |
| OneLake Shortcut | Access Blob data without duplication |
| PySpark | Data transformation |
| Delta Lake | Versioned, ACID-compliant tables |
| Fabric Pipeline | Orchestration |
| Blob Trigger | Event-driven automation |
| Semantic Model | Reporting layer |

---

## Author

**Ogueri Dexter**  
[LinkedIn](https://www.linkedin.com/in/ogueri-dexter-a2b309ab)

---

## What This Project Demonstrates

- End-to-end medallion architecture in Microsoft Fabric
- Event-driven pipeline automation
- Replay-safe raw data preservation
- SCD Type 2 dimensional modelling
- Production-grade orchestration with error routing
- Semantic model readiness for BI reporting
