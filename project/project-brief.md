# End-to-End Project Brief

## 1. Dataset Card

| Field | Details |
|---|---|
| **Name** | TBD |
| **Publisher & URL** | TBD |
| **Licence** | TBD |
| **Download Date & Version** | TBD |
| **Grain (One Row Represents)** | TBD |
| **Period Covered** | TBD |
| **Size (Rows × Columns, File Size)** | TBD |
| **Key Columns** | Dates: TBD<br/>Dimensions: TBD<br/>Measures: TBD<br/>Target: TBD |
| **Known Quality Issues** | TBD |
| **Additional Sources to Join** | TBD |

---

## 2. Business Problem

### 2.1 Organisation, Department & Stakeholder
* **Organisation:** TBD
* **Department:** TBD
* **Stakeholder:** TBD

### 2.2 The Decision & Constraints
* **Decision to Support:** TBD
* **Constraints:** TBD

### 2.3 Hypotheses

| ID | Hypothesis Statement | Metric & Definition | Expected Direction | Test Method | Action if True | Action if False |
|---|---|---|---|---|---|---|
| **H1** | TBD | TBD | TBD | TBD | TBD | TBD |
| **H2** | TBD | TBD | TBD | TBD | TBD | TBD |

### 2.4 Key Performance Indicators (KPIs)
1. **KPI 1:** TBD
2. **KPI 2:** TBD
3. **KPI 3:** TBD

---

## 3. Solution Design & Architecture

### 3.1 Solution Type
* **Type:** TBD
* **Overview:** TBD

### 3.2 General Architecture

```mermaid
flowchart LR
    A[Source<br/>Open Data Portal] --> B[Ingestion<br/>Python / pandas]
    B --> C[Cleaning & Quality<br/>Types, Nulls, Duplicates]
    C --> D[Data Model<br/>Fact + Dimensions]
    D --> E[Analytics / Engine<br/>KPIs & Predictions]
    E --> F[Dashboard<br/>Power BI / Streamlit]
    F --> G((Stakeholder Decision))
