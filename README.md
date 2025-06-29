# Aircraft Tracking Data Warehouse

A comprehensive data warehouse solution for analyzing FBI and DHS surveillance aircraft flight patterns and tracking data.

## 📊 Project Overview

This project implements a complete data warehouse system to process and analyze flight tracking data from government surveillance aircraft operated by the FBI and Department of Homeland Security (DHS). The solution includes data modeling, ETL processes, and interactive dashboards for intelligence analysis.

**📋 Full Project Documentation:** [OneDrive Link](https://onedrive.live.com/?authkey=%21AEa1ltBlcvw3LG4&id=728232F60FAF6240%21118&cid=728232F60FAF6240)

## 📈 Data Sources

The dataset covers surveillance plane operations with data spanning over four months:

1. **BuzzFeed News Federal Surveillance Planes Dataset**  
   Source: https://github.com/BuzzFeedNews/2016-04-federal-surveillance-planes

2. **Kaggle Spy Plane Finder Dataset**  
   Source: https://www.kaggle.com/jboysen/spy-plane-finder

**Data Origin:** Flight tracking data from Flightradar24 combined with Federal Aviation Administration records.

## 🏗️ Architecture & Design

### Entity-Relationship Diagram (EER)
<img width="574" alt="EER Diagram" src="https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/e8806d02-bc97-45d5-b23b-3d92cc4c9043">

### Data Source Preparation
<img width="780" alt="Data Preparation" src="https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/109133be-81a3-4339-bf19-d2900827bfeb">

### Solution Architecture
<img width="712" alt="Solution Architecture" src="https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/f4fe2157-deb5-4b89-a043-20663fe9f1e7">

## 🗄️ Data Warehouse Implementation

### Star Schema Design
<img width="574" alt="Data Warehouse Schema" src="https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/dff18a86-3d2f-4733-a598-e691d70bab6a">

### Dimensional Model
![Dimensional Model](https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/54748bc6-8bdb-4326-ad00-c465de5a1b2b)

## 📊 Analytics Dashboard

Interactive dashboard providing insights into surveillance aircraft operations:

<img width="1432" alt="Analytics Dashboard" src="https://github.com/dinushiTJ/Aircraft-Tracking-Data-Warehouse/assets/63807534/15905616-b921-413a-a0bd-b69dc4797897">

### Dashboard Features
- **Flight Pattern Analysis** - Track aircraft routes and operational areas
- **Temporal Analytics** - Monitor flight frequency and timing patterns
- **Geographic Visualization** - Map-based surveillance activity analysis
- **Aircraft Utilization** - Usage statistics and operational metrics

## 🛠️ Technical Stack

- **Database:** SQL Server/MySQL for data warehouse
- **ETL:** Custom data integration pipelines
- **Analytics:** Business intelligence tools for reporting
- **Visualization:** Interactive dashboard framework

## 🚀 Key Features

- ✅ **Comprehensive ETL Pipeline** - Automated data extraction, transformation, and loading
- ✅ **Dimensional Modeling** - Optimized star schema for analytical queries
- ✅ **Real-time Analytics** - Interactive dashboards for data exploration
- ✅ **Data Quality Controls** - Validation and cleansing processes
- ✅ **Scalable Architecture** - Designed for large-scale flight tracking data

## 📋 Project Structure

```
Aircraft-Tracking-Data-Warehouse/
├── data/                    # Raw and processed datasets
├── sql/                     # Database schemas and scripts
├── etl/                     # Data transformation pipelines
├── dashboards/              # Visualization and reporting
└── docs/                    # Project documentation
```

## 🔍 Use Cases

- **Intelligence Analysis** - Government surveillance pattern analysis
- **Data Journalism** - Investigative reporting on federal aircraft operations
- **Academic Research** - Studies on government transparency and surveillance
- **Data Engineering** - Example of large-scale data warehouse implementation

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Implement your changes
4. Submit a pull request

## 📄 Data Ethics & Usage

This project uses publicly available flight tracking data for educational and research purposes. All data sources are cited and the analysis respects privacy and transparency principles.

---

**Tags:** `data-warehouse` `etl` `sql` `analytics` `surveillance-data` `dimensional-modeling` `business-intelligence`
