# Project 1: Microsoft Fabric Medallion Architecture Pipeline

## Overview
This project shows how I built an end-to-end Microsoft Fabric medallion architecture for processing sales data from Azure Blob Storage.

The goal of the project was to take raw files from Blob Storage, transform them into a clean and trusted Silver layer, build reporting-ready Gold tables, automate the process with a pipeline and trigger, and create a semantic model for reporting.

## What this project does
The solution starts when files are uploaded into Azure Blob Storage. A Lakehouse shortcut points to the Blob Storage location so the files can be accessed inside Microsoft Fabric. From there, the pipeline processes the files through the Silver and Gold layers.

The workflow includes:
- raw files uploaded to Azure Blob Storage
- a Lakehouse shortcut connected to the Blob source
- a Silver layer for cleaning and standardizing data
- a Gold layer for dimensions, facts, and reporting-ready tables
- a pipeline that runs automatically when a new file arrives
- a semantic model for reporting and analytics
- source-to-target mapping documentation to define how data moves from source to destination

## Architecture
### Source
- Azure Blob Storage

### Ingestion
- Files land in Blob Storage
- The Lakehouse uses a shortcut to access the Blob files

### Silver Layer
The Silver layer:
- reads the incoming files
- cleans and standardizes the data
- fixes data types and formatting issues
- handles transaction-level merge logic
- creates a trusted current-state dataset

### Gold Layer
The Gold layer:
- creates reporting-ready dimensions and facts
- builds customer, product, date, and sales tables
- applies business logic for analytics
- supports dashboarding and semantic modeling

### Semantic Model
A semantic model is created on top of the reporting layer so the data can be used easily in reports, dashboards, and business analysis. This makes the final dataset more useful for business users and helps connect the technical data model to reporting needs.

### Pipeline and Trigger
The pipeline automates the whole process:
1. runs the Silver notebook
2. runs the Gold notebook
3. moves successful files to Processed
4. moves failed files to Error

A trigger starts the pipeline automatically when a new file is uploaded to Blob Storage.

## Source-to-Target Mapping
A Source-to-Target Mapping document is an important part of the project. It explains how each source column is transformed and loaded into the destination tables.

This helps with:
- documenting the transformation logic clearly
- making testing and validation easier
- improving traceability from raw data to reporting tables
- supporting collaboration between engineering, reporting, and business teams

## Naming Standards and Data Modeling
This project also highlights the importance of using proper naming standards when building data models.

I paid attention to naming columns with clear classwords so the meaning of each field is easy to understand. For example:
- keys end with words like `Key` or `RecordID`
- business identifiers use names like `CustomerID` or `ProductID`
- descriptive attributes use names like `CustomerFullName` or `CityName`
- amounts use names like `UnitPriceAmount` or `TaxAmount`
- flags use names like `CurrentFlag`

Using clear naming standards makes the model easier to read, easier to maintain, and better for reporting.

## Key concepts demonstrated
- Microsoft Fabric Lakehouse
- Blob Storage integration
- OneLake shortcut usage
- Silver and Gold layer transformation
- medallion architecture design
- pipeline orchestration
- trigger-based automation
- dimensional modeling
- semantic model creation
- source-to-target mapping
- naming standards for data modeling

## Why this project matters
This project demonstrates practical data engineering skills in Microsoft Fabric. It shows how to design a structured data workflow that is automated, scalable, and ready for reporting.

It also highlights the importance of separating:
- raw data ingestion
- data cleansing
- business modeling
- reporting enablement

It also shows that good data engineering is not just about moving data. It also involves:
- documenting transformations clearly
- applying consistent naming standards
- modeling data in a way that supports reporting and analytics

## Tools and technologies
- Microsoft Fabric
- Azure Blob Storage
- Lakehouse
- PySpark
- Delta tables
- Fabric Pipeline
- Fabric Trigger
- Semantic Model

## Outcome
The final result is a fully automated data pipeline that takes raw source files and turns them into reporting-ready data structures that can be used for dashboards, analytics, and business reporting.


