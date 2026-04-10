<div align="center">

# 🏙️ SCIMS — Smart City Information Management System

**A comprehensive MySQL-powered database platform for modern urban operations**

![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Database-orange?style=for-the-badge&logo=databricks&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-Academic-blue?style=for-the-badge)

*CSE311 Database Systems Project — North South University*

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Database Schema](#-database-schema)
- [ER Diagrams](#-er-diagrams)
- [Tables](#-tables)
- [Queries & Analytics](#-queries--analytics)
- [Getting Started](#-getting-started)
- [Team](#-team)
- [Acknowledgments](#-acknowledgments)

---

## 🌐 Overview

The **Smart City Information Management System (SCIMS)** is an advanced, database-driven solution designed to streamline the management of modern urban operations. Built entirely on **MySQL**, SCIMS serves as a centralized platform for integrating, monitoring, and optimizing city administration — from citizen registration to IoT-powered traffic analysis.

> SCIMS empowers city administrators to identify trends, predict challenges, and implement proactive solutions by centralizing all urban data in one place.

### 🎯 Key Objectives

| Objective | Description |
|-----------|-------------|
| **Centralized Data Integration** | Single MySQL repository for all urban operations |
| **Citizen-Centric Services** | Streamlined profiles, registrations, and service requests |
| **Data-Driven Decision Making** | Real-time info on transportation, infrastructure, and utilities |
| **Operational Transparency** | Well-organized tables for clear insights on service delivery |
| **Smart Urban Planning** | Location, weather, and maintenance data for urban development |
| **Regulatory Compliance** | Automated workflows and triggers for governance policies |

---

## ✨ Features

- 🏛️ **Citizen Management** — Registration approval workflow with automated triggers
- 🚌 **Transportation Tracking** — Bus routes, frequencies, and real-time status
- 📡 **IoT Device Integration** — Monitor sensors, traffic data, and utility consumption
- 🌦️ **Weather Monitoring** — Location-based weather condition data and analytics
- 🔧 **Infrastructure Maintenance** — Track planned, ongoing, and completed maintenance
- 📊 **Advanced Analytics** — 36 pre-built SQL queries for actionable insights
- 🔔 **Automated Triggers** — Auto-populate `Citizen_Info` on registration approval
- 📍 **Location Intelligence** — Citizen-location mapping across 30+ Dhaka areas

---

## 🗄️ Database Schema

SCIMS consists of **15 normalized tables** organized across functional domains:

```
SCIMS Database
├── 👤 Citizen Management
│   ├── CitizenRegistration       — Application intake & approval workflow
│   ├── Citizen_Info              — Approved citizen profiles
│   └── Citizen_Location          — Citizen-to-area mapping (junction table)
│
├── 🏢 Administration
│   ├── Department                — City departments with budgets & responsibilities
│   └── City_Official             — Officials with roles, supervisors & qualifications
│
├── 🛎️ Services
│   ├── Public_Services           — Available city services with fees & response times
│   ├── Service_Request           — Citizen service requests & status tracking
│   └── Feedback                  — Ratings, complaints, and resolutions
│
├── 📍 Location & Environment
│   ├── Location                  — 30+ Dhaka areas with type & postal codes
│   ├── Weather_Condition_Data    — Temperature, humidity, wind speed by location
│   └── Infrastructure_Maintenance — Road, bridge & building maintenance records
│
└── 📡 Smart City / IoT
    ├── IoT_Devices               — Sensors, trackers & monitors across the city
    ├── Traffic_Data              — Vehicle counts, speeds & congestion levels
    ├── Utility_Monitoring        — Electricity, water & gas consumption
    └── Bus_Transportation        — Routes, stations, frequency & on-time status
```

---

## 📐 ER Diagrams

<details>
<summary><b>Conceptual ER Diagram</b></summary>
<br>

> High-level entity-relationship diagram showing all major entities and their associations.

![Conceptual ER Diagram](https://github.com/user-attachments/assets/7ea9f90a-fa5a-4b86-b5dc-da15350b8500)

</details>

<details>
<summary><b>Logical ER Diagram</b></summary>
<br>

> Detailed logical design with all attributes, primary keys, and foreign key relationships.

![Logical ER Diagram](https://github.com/user-attachments/assets/e8ffec91-910d-4d95-9976-57c3eee95914)

</details>

<details>
<summary><b>Physical ER Diagram</b></summary>
<br>

> Physical schema showing exact data types, constraints, and indexes.

<img width="1011" height="730" alt="Physical ER Diagram" src="https://github.com/user-attachments/assets/91d245f6-a855-4419-b552-fb761ad46628" />

</details>

---

## 📦 Tables

### CitizenRegistration
Handles incoming registration applications before approval.

```sql
CREATE TABLE CitizenRegistration (
    Registration_ID   VARCHAR(500) PRIMARY KEY,
    Full_Name         VARCHAR(200) NOT NULL,
    Gender            VARCHAR(10)  CHECK (Gender IN ('Male', 'Female', 'Others')),
    Date_Of_Birth     DATE         NOT NULL,
    Address           VARCHAR(300) NOT NULL,
    Contact_Number    VARCHAR(15),
    Email             VARCHAR(100) UNIQUE,
    National_ID       VARCHAR(30)  UNIQUE NOT NULL,
    Approval_Status   VARCHAR(20)  CHECK (Approval_Status IN ('Pending', 'Approved', 'Rejected'))
                                   DEFAULT 'Pending',
    Registration_Date TIMESTAMP    DEFAULT CURRENT_TIMESTAMP
);
```

### Citizen_Info
Auto-populated from `CitizenRegistration` via trigger on approval.

```sql
CREATE TABLE Citizen_Info (
    Citizen_ID             VARCHAR(30)  PRIMARY KEY,
    Name                   VARCHAR(200) NOT NULL,
    Address                VARCHAR(300) NOT NULL,
    Contact_Number         VARCHAR(15)  NOT NULL,
    Date_Of_Birth          DATE         NOT NULL,
    Email                  VARCHAR(100) UNIQUE,
    Gender                 VARCHAR(10)  CHECK (Gender IN ('Male', 'Female', 'Others')),
    National_ID            VARCHAR(30)  UNIQUE NOT NULL,
    City_Registration_Date TIMESTAMP    DEFAULT CURRENT_TIMESTAMP NOT NULL
);
```

> 📁 Full DDL for all 15 tables is in [`SCIMS.sql`](SCIMS.sql)

---

## 🔍 Queries & Analytics

SCIMS includes **36 SQL queries** across multiple analytical categories:

### 🔔 Triggers & Workflows
```sql
-- Auto-insert approved citizens into Citizen_Info
CREATE TRIGGER After_Approval_Update
AFTER UPDATE ON CitizenRegistration
FOR EACH ROW
BEGIN
    IF NEW.Approval_Status = 'Approved' THEN
        INSERT INTO Citizen_Info (Citizen_ID, Name, Address, ...)
        VALUES (NEW.Registration_ID, NEW.Full_Name, NEW.Address, ...)
        ON DUPLICATE KEY UPDATE Name = VALUES(Name), Address = VALUES(Address);
    END IF;
END$$
```

### 👥 Citizen Queries
| Query | Description |
|-------|-------------|
| Pending Applicants | Citizens registered but not yet approved |
| Monthly Registrations | Count of new citizens this month |
| Citizens by Location | Residents mapped to each city area |
| Locations with No Citizens | Areas with zero registered residents |

### 🏢 Department & Services
| Query | Description |
|-------|-------------|
| Department Heads | Lists head official for each department |
| Services per Department | All services offered by each department |
| IoT Usage by Department | Active device count ranked by department |
| Services with Feedback | Joins services with ratings & feedback types |

### 🚌 Transportation
| Query | Description |
|-------|-------------|
| Bus Route Analysis | Frequency classification (High/Moderate/Low) |
| Route Status Distribution | On-time vs delayed vs cancelled per route |
| High Vehicle Count Locations | Areas with total vehicles > 100 |
| Low Vehicle Count Locations | Areas with total vehicles < 100 |

### 🌦️ Weather & Environment
| Query | Description |
|-------|-------------|
| Extreme Weather Locations | Stormy/Foggy areas or wind speed > 50 |
| Avg Temp & Humidity | Grouped by weather condition type |
| Monthly Weather Stats | Average temperature, humidity, wind speed by month |

### 🔧 Maintenance
| Query | Description |
|-------|-------------|
| Ongoing Maintenance | Active maintenance work by location |
| Maintenance per Department | Count of maintenance tasks per department |
| Maintenance by Department | Type and status of each department's work |

### 📋 Service Requests
| Query | Description |
|-------|-------------|
| Requests by Status | Count breakdown: Pending / In Progress / Completed / Rejected |
| Officials with Most Pending | Ranked officials by pending request volume |
| Highest-Request Locations | Areas with the most service requests |
| Most Frequent Services | Service demand by location with assigned official details |

---

## 🚀 Getting Started

### Prerequisites
- MySQL 8.0+
- MySQL Workbench (recommended) or any MySQL client

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/withalindo/Smart_City_Information_Management_System___CSE311_Project.git
cd SCIMS
```

**2. Create the database**
```sql
CREATE DATABASE SCIMS;
USE SCIMS;
```

**3. Run the SQL script**
```bash
mysql -u root -p SCIMS < SCIMS.sql
```

Or import via MySQL Workbench:
> `Server` → `Data Import` → `Import from Self-Contained File` → select `SCIMS.sql`

**4. Verify the setup**
```sql
SHOW TABLES;
SELECT COUNT(*) FROM CitizenRegistration;
SELECT COUNT(*) FROM Citizen_Info;
```

### Quick Test — Run a Sample Query
```sql
-- Top departments by IoT device usage
SELECT d.Department_Name AS "Department Name", 
       COUNT(iot.Device_ID) AS "Active Devices"
FROM IoT_Devices iot
JOIN Department d ON iot.Department_ID = d.Department_ID
WHERE iot.Device_Status = 'Active'
GROUP BY d.Department_ID
ORDER BY COUNT(iot.Device_ID) DESC;
```

---

## 📅 Project Timeline

| Phase | Task | Duration |
|-------|------|----------|
| Phase 1 | Project Planning | Sep 5–20, 2024 |
| Phase 2 | Project Description Writing | Sep 21–28, 2024 |
| Phase 3 | Conceptual & Logical Design | Sep 29 – Oct 20, 2024 |
| Phase 4 | Table Creation & Data Entry | Oct 21 – Nov 13, 2024 |
| Phase 5 | Query Selection & Implementation | Nov 12–25, 2024 |
| Phase 6 | Database Testing | Nov 26 – Dec 9, 2024 |
| Phase 7 | Final Report | Dec 10–18, 2024 |

---

## 👥 Team

| Student ID | Name | Contribution |
|------------|------|:---:|
| 2211275042 | Hasnat Karibul Islam | 33% |
| 2211022042 | Md. Minhajul Islam | 32% |
| 2212021042 | Nur Ibna Kawsar Zitu | 25% |
| 2212642042 | Md. Sabbir Hossain | 10% |

**Course:** CSE311 — Database Systems  
**Institution:** North South University  
**Semester:** Fall 2024

---

## 🙏 Acknowledgments

We extend our sincere gratitude to our course instructor **Prof. Dr. Kamruddin Nur**, Professor at the Department of Electrical and Computer Engineering, North South University, for his invaluable guidance and support throughout this project.



---


