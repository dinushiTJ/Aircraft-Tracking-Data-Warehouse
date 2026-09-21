# Aircraft Tracking Data Warehouse

**In one sentence:** an end-to-end Microsoft BI solution that pulls flight-tracking data for FBI and DHS surveillance aircraft from four source systems, cleans it through a staging layer into a dimensional data warehouse, and serves it through an OLAP cube, SSRS reports and an Excel dashboard.

![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![SSIS](https://img.shields.io/badge/ETL-SSIS-blue)
![SSAS](https://img.shields.io/badge/OLAP-SSAS-blue)
![SSRS](https://img.shields.io/badge/Reporting-SSRS-blue)
![MySQL](https://img.shields.io/badge/Source-MySQL-4479A1?logo=mysql&logoColor=white)

| | |
|---|---|
| **Tracking records** | 397,040 transponder detections |
| **Aircraft / flight segments** | 188 aircraft, 5,159 flight segments |
| **Period covered** | 17 Aug 2015 – 1 Jan 2016 |
| **Source systems** | MySQL, SQL Server, CSV and tab-delimited text (7 source tables) |
| **Warehouse** | 6 dimensions + 1 fact table, snowflake schema, SCD Type 2 history |
| **Outputs** | SSAS cube with KPI and hierarchies, 4 SSRS report types, Excel dashboard |

---

## The problem

The data comes from the [BuzzFeed News federal surveillance planes investigation](https://github.com/BuzzFeedNews/2016-04-federal-surveillance-planes) ([Kaggle version](https://www.kaggle.com/jboysen/spy-plane-finder)). It combines Flightradar24 transponder data with the FAA aircraft registration database.

To simulate a real enterprise environment, where data rarely lives in one place, I split the dataset across **four different source technologies**. The ETL then has to extract from each, reconcile them and load them into one model.

![Source partitioning](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/109133be-81a3-4339-bf19-d2900827bfeb)

| Source | Technology | Tables |
|---|---|---|
| Aircraft registry | MySQL (InnoDB) | `aircraft_data` (188 rows), `aircraft_type` |
| Manufacturers | Microsoft SQL Server | `manufacturer` (restored from `.bak`) |
| Registrants | Tab-delimited text | `registant.txt`, `registant_address.txt` |
| Operations | CSV | `agency.csv`, `aircraft_tracking.csv` (397,040 rows) |

### Source data model (EER)

![EER diagram](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/e8806d02-bc97-45d5-b23b-3d92cc4c9043)

---

## Architecture

![Solution architecture](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/f4fe2157-deb5-4b89-a043-20663fe9f1e7)

The ETL runs in two layers, each built as its own SSIS package:

**1. Staging** (`Aircraft_Staging_ETL.dtsx`, into the `Aircraft_tracking_Staging` database)
- Truncates the staging tables, then extracts all seven sources into them with minimal transformation, so every run is repeatable.
- Uses four flat-file connections, plus OLE DB connections to MySQL and SQL Server.
- Triggers the warehouse package automatically when it finishes.

**2. Warehouse** (`Aircraft_DW_ETL.dtsx`, into the `Aircraft_tracking_DW` database)
- **Lookups (×6)** resolve natural keys to surrogate keys.
- **Merge Join** combines registrants with their addresses.
- **Slowly Changing Dimension (×2)** keeps history for registrants and aircraft using `start_date` / `end_date`.
- **Derived Columns** add audit timestamps and unit conversions:
  - `altitude_in_meters = altitude × 0.3048`
  - `speed_in_kmph = speed × 1.852`
- **OLE DB Commands** update existing rows.

**Validation:** after each load, record counts are compared between the staging and warehouse layers.

All source-to-target rules are documented in [`Document/Aircraft Tracking ETL Mapping.xlsx`](Document/). It covers source details, column-level lineage, dimension and fact definitions (derived attributes, nullability, keys) and the transformation logic for every target field.

---

## Warehouse design

![Warehouse schema](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/dff18a86-3d2f-4733-a598-e691d70bab6a)

- **Fact:** `FactAircraftTracking`, one row per transponder detection. Measures are position, altitude, speed, track and squawk, plus the derived metric conversions.
- **Dimensions:**
  - `DimAircraftData` (SCD2) snowflakes out to `DimAircraftType`, `DimManufacturer` and `DimRegistrant` (SCD2).
  - `DimAgency` and `DimDate` link to the fact table directly.
- Every dimension has an **integer surrogate key** plus the original **alternate (business) key**, so each row traces back to its source.
- `insert_date` and `modified_date` columns record when each row was loaded or changed.

![Physical model](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/54748bc6-8bdb-4326-ad00-c465de5a1b2b)

---

## Analytics layer

**SSAS cube** (`ETL/Aircraft_Tracking_SSAS`)
- Built on the warehouse, with date and aircraft hierarchies and a KPI.
- Queried with MDX and through Excel Power Pivot.
- Used to demonstrate drill-down, roll-up, slice, dice and pivot.

**SSRS reports** (`Reports/`)

| Report | Type |
|---|---|
| R1: Aircraft-wise tracked distance, year over year | Matrix |
| R2: Aircraft type and manufacturer-wise tracked distance, monthly | Parameterised |
| R3: Aircraft-wise tracked distance in metres, year over year | Drill-down |
| R4: Aircraft type-wise tracking analysis (yearly → monthly) | Drill-through (2 levels) |

**Excel dashboard**

![Dashboard](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/15905616-b921-413a-a0bd-b69dc4797897)

---

## Repository layout

```
DataSources/   raw source files: CSV, TXT, MySQL dump, SQL Server .bak, DimDate script
ETL/
├── aircraft_tracking_ETL_DW/   SSIS project: staging + warehouse packages
└── Aircraft_Tracking_SSAS/     SSAS multidimensional project: cube, dimensions, KPI
Reports/       SSRS report definitions (.rdl)
Document/      ETL mapping workbook, dashboard workbook, design reports (PDF)
```

## Run it locally

You will need SQL Server with SSIS and SSAS, Visual Studio with SQL Server Data Tools, MySQL, and SQL Server Management Studio (SSMS).

1. Import `DataSources/aircraft_data_db.sql` into MySQL.
2. Restore `DataSources/aircraft_manufacturer_source.bak` into SQL Server.
3. Create the `Aircraft_tracking_Staging` and `Aircraft_tracking_DW` databases.
4. Run `DataSources/DateMaster.sql` to populate `DimDate`.
5. Open `ETL/aircraft_tracking_ETL_DW.sln` and update the connection managers and file paths.
6. Run `Aircraft_Staging_ETL.dtsx`. It loads staging, then runs the warehouse package.
7. Open `ETL/Aircraft_Tracking_SSAS.sln`, deploy the cube, then publish the reports in `Reports/` to your report server.

## Documentation

- [`Document/DWBI_assigment_1_report.pdf`](Document/): source preparation, warehouse design and ETL development
- [`Document/DWBI_assigment_2_report.pdf`](Document/): SSAS cube, OLAP operations and SSRS reports

## Data and ethics

This project uses public data from the BuzzFeed News investigation, for educational purposes. Credit for collecting the data goes to BuzzFeed News, Flightradar24 and the FAA.

---

Built by [Dinushi Jayasinghe](https://www.linkedin.com/in/dinushi-jayasinghe/) for the Data Warehousing and Business Intelligence module (IT3021) at SLIIT.
