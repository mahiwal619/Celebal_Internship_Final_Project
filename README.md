# Full and Incremental Load using COPY INTO (ADF Project)

> Internship Project - Azure Data Factory | Celebal Technologies

This project showcases the use of **Azure Data Factory** to implement a **Full and Incremental Load** system using the `COPY INTO` command. The project consists of multiple interconnected pipelines, including REST API ingestion and conditional logic-based data movements from Azure SQL Database to ADLS Gen2.

---

## 🙏 Acknowledgment

I would like to express my heartfelt gratitude to **CSI (Celebal Summer Internship)** for providing me the opportunity to work on this end-to-end Data Engineering project. The mentorship, guidance, and practical exposure to real-world use cases have greatly enhanced my learning experience.

---

## 🔹 Task Breakdown

### ✅ Task 1: Fetch country data from REST API

- **Pipeline Name**: `CountryDataPipeline`

**Step 1:** Created Linked Service for REST API — `ls_RESTCountries`

![countrydata-1](./images/countrydata-1)

**Step 2:** Created Linked Service for ADLS Gen2 — `ls_ADLSGen2`

![countrydata-2](./images/countrydata-2)

**Step 3:** Created input dataset — `ds_REST_CountryData`

![countrydata-3](./images/countrydata-3)

**Step 4:** Created output dataset — `ds_CountryData`

![countrydata-4](./images/countrydata-4)

**Step 5:** Designed pipeline with `ForEach` activity to loop through multiple country names.

![countrydata-5](./images/countrydata-5)

**Step 6:** Inside `ForEach`, added `CopyData` activity with source pointing to REST dataset.

![countrydata-6](./images/countrydata-6)

**Step 7:** Sink was configured to write each country’s data to ADLS as `.json` files.

![countrydata-7](./images/countrydata-7)

**Step 8:** The pipeline ran successfully, triggering 5 executions of `CopyCountryData` inside the loop.

![countrydata-8](./images/countrydata-8)

**Step 9:** Verified output in ADLS container. All 5 country `.json` files were present.

![countrydata-9](./images/countrydata-9)

---

### ✅ Task 2: Trigger-based Copy

**Step 1:** Added a new trigger named `TwiceDailyTrigger` to run the pipeline every 12 hours.

**Step 2:** Start date and time configured as **18th July 2025, 12:00 AM IST**.

![countrydata-10](./images/countrydata-10)

---

### ✅ Task 3: Parent Pipeline - Conditional Customer Copy

- **Pipeline Name**: `parent_copy_customer_pipe`

**Step 1:** Created the `Customer` table with 605 records. (A separate `Product` table with 100 records was also created.)

![reccount-1](./images/reccount-1)

**Step 2:** Created Linked Service for Azure SQL — `AzureSQL_LS`

![reccount-2](./images/reccount-2)

**Step 3:** Created Linked Service for ADLS — `ADLS_LS`

![reccount-3](./images/reccount-3)

**Step 4:** Defined datasets — `customer_sql_ds`, `product_sql_ds`, `customer_adls_ds`, `product_adls_ds`

![reccount-4](./images/reccount-4)
![reccount-5](./images/reccount-5)
![reccount-6](./images/reccount-6)
![reccount-7](./images/reccount-7)

**Step 5:** Used `Lookup` activity to get the count of customer records.

![reccount-8](./images/reccount-8)

**Step 6:** Used `IfCondition` to check if count > 500. If true, copy to ADLS.

![reccount-9](./images/reccount-9)
![reccount-10](./images/reccount-10)
![reccount-11](./images/reccount-11)

**Step 7:** Another `IfCondition` checks if count > 600. If true, call child pipeline.

![reccount-15](./images/reccount-15)

**Step 8:** Execute Pipeline activity passes count to child pipeline using parameter.

![reccount-16](./images/reccount-16)

**Step 9:** Final pipeline view and execution output — all activities succeeded.

![reccount-17](./images/reccount-17)

---

### ✅ Task 4: Child Pipeline with Parameter

- **Pipeline Name**: `child_copy_product_pipe`

**Step 1:** Accepts parameter `customerCount` (Int).

**Step 2:** `IfCondition` checks if `customerCount >= 600`. If true, copy product data.

![reccount-12](./images/reccount-12)

**Step 3:** Copy activity moves product data from SQL to ADLS.

![reccount-13](./images/reccount-13)
![reccount-14](./images/reccount-14)

---

## 📂 File Output Structure (in ADLS)

![reccount-18](./images/reccount-18)

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

---