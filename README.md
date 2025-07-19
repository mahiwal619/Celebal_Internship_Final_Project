# Full and Incremental Load using COPY INTO (ADF Project)

> Internship Project - Azure Data Factory | Celebal Technologies

This project showcases the use of **Azure Data Factory** to implement a **Full and Incremental Load** system using the `COPY INTO` command. The project consists of multiple interconnected pipelines, including REST API ingestion and conditional logic-based data movements from Azure SQL Database to ADLS Gen2.

---

## 🙏 Acknowledgment

I would like to express my heartfelt gratitude to **[CSI (Celebal Summer Internship)](https://www.celebaltech.com/)** for providing me the opportunity to work on this end-to-end Data Engineering project. The mentorship, guidance, and practical exposure to real-world use cases have greatly enhanced my learning experience.

---

## 🔹 Task Breakdown

### ✅ Task 1: Fetch country data from REST API

- **Pipeline Name**: `CountryDataPipeline`

**Step 1:** Created Linked Service for REST API — `ls_RESTCountries`

<p align="center">
  <img src="./images/countrydata-1.png"/>
</p>

**Step 2:** Created Linked Service for ADLS Gen2 — `ls_ADLSGen2`

<p align="center">
  <img src="./images/countrydata-2.png"/>
</p>

**Step 3:** Created input dataset — `ds_REST_CountryData`

<p align="center">
  <img src="./images/countrydata-3.png"/>
</p>

**Step 4:** Created output dataset — `ds_CountryData`

<p align="center">
  <img src="./images/countrydata-4.png"/>
</p>

**Step 5:** Designed pipeline with `ForEach` activity to loop through multiple country names.

<p align="center">
  <img src="./images/countrydata-5.png"/>
</p>

**Step 6:** Inside `ForEach`, added `CopyData` activity with source pointing to REST dataset.

<p align="center">
  <img src="./images/countrydata-6.png"/>
</p>

**Step 7:** Sink was configured to write each country’s data to ADLS as `.json` files.

<p align="center">
  <img src="./images/countrydata-7.png"/>
</p>

**Step 8:** The pipeline ran successfully, triggering 5 executions of `CopyCountryData` inside the loop.

<p align="center">
  <img src="./images/countrydata-8.png"/>
</p>

**Step 9:** Verified output in ADLS container. All 5 country `.json` files were present.

<p align="center">
  <img src="./images/countrydata-9.png"/>
</p>

---

### ✅ Task 2: Trigger-based Copy

**Step 1:** Added a new trigger named `TwiceDailyTrigger` to run the pipeline every 12 hours.

**Step 2:** Start date and time configured as **18th July 2025, 12:00 AM IST**.

<p align="center">
  <img src="./images/countrydata-10.png"/>
</p>

---

### ✅ Task 3: Parent Pipeline - Conditional Customer Copy

- **Pipeline Name**: `parent_copy_customer_pipe`

**Step 1:** Created the `Customer` table with 605 records. (A separate `Product` table with 100 records was also created.)

<p align="center">
  <img src="./images/reccount-1.png"/>
</p>

**Step 2:** Created Linked Service for Azure SQL — `AzureSQL_LS`

<p align="center">
  <img src="./images/reccount-2.png"/>
</p>

**Step 3:** Created Linked Service for ADLS — `ADLS_LS`

<p align="center">
  <img src="./images/reccount-3.png"/>
</p>

**Step 4:** Defined datasets — `customer_sql_ds`, `product_sql_ds`, `customer_adls_ds`, `product_adls_ds`

<p align="center">
  <img src="./images/reccount-4.png"/>
</p>
<p align="center">
  <img src="./images/reccount-5.png"/>
</p>
<p align="center">
  <img src="./images/reccount-6.png"/>
</p>
<p align="center">
  <img src="./images/reccount-7.png"/>
</p>

**Step 5:** Used `Lookup` activity to get the count of customer records.

<p align="center">
  <img src="./images/reccount-8.png"/>
</p>

**Step 6:** Used `IfCondition` to check if count > 500. If true, copy to ADLS.

<p align="center">
  <img src="./images/reccount-9.png"/>
</p>
<p align="center">
  <img src="./images/reccount-10.png"/>
</p>
<p align="center">
  <img src="./images/reccount-11.png"/>
</p>

**Step 7:** Another `IfCondition` checks if count > 600. If true, call child pipeline.

<p align="center">
  <img src="./images/reccount-15.png"/>
</p>

**Step 8:** Execute Pipeline activity passes count to child pipeline using parameter.

<p align="center">
  <img src="./images/reccount-16.png"/>
</p>

**Step 9:** Final pipeline view and execution output — all activities succeeded.

<p align="center">
  <img src="./images/reccount-17.png"/>
</p>

---

### ✅ Task 4: Child Pipeline with Parameter

- **Pipeline Name**: `child_copy_product_pipe`

**Step 1:** Accepts parameter `customerCount` (Int).

**Step 2:** `IfCondition` checks if `customerCount >= 600`. If true, copy product data.

<p align="center">
  <img src="./images/reccount-12.png"/>
</p>

**Step 3:** Copy activity moves product data from SQL to ADLS.

<p align="center">
  <img src="./images/reccount-13.png"/>
</p>
<p align="center">
  <img src="./images/reccount-14.png"/>
</p>

---

## 📂 File Output Structure (in ADLS)

<p align="center">
  <img src="./images/reccount-18.png"/>
</p>

| File                | Location                | Condition                        |
| ------------------- | ----------------------- | -------------------------------- |
| `customer_full.csv` | `/customerproductdata/` | If Customer count > 500          |
| `product_full.csv`  | `/customerproductdata/` | If Customer count > 600          |
| `.json` files       | `/countrydata/`         | 5 sample countries from REST API |

---

## 🔢 Pipeline Summary Table

| Pipeline Name               | Purpose                                        | Condition      | Output                  |
| --------------------------- | ---------------------------------------------- | -------------- | ----------------------- |
| `CountryDataPipeline`         | Fetch country data via REST                    | Daily Trigger  | 5 `.json` files in ADLS |
| `parent_copy_customer_pipe` | Conditional copy of `Customers` table          | If count > 500 | `customer_full.csv`     |
| `child_copy_product_pipe`   | Copy of `Products` table based on parent count | If count > 600 | `product_full.csv`      |

---

## 📅 Author

**Mahiwal Vaishnav**\
B.Tech Final Year (2022–2026)\
Celebal Summer Internship Final Project
