
# 🧹 Layoffs Data Cleaning Project (MySQL)

This project focuses on cleaning and standardizing a raw layoffs dataset using SQL in MySQL. The cleaned data is prepared for further analysis and visualization.

---

## 📁 Dataset Overview

- **Source File:** [`layoffs.csv`](sandbox:/mnt/data/layoffs.csv)
- **Total Columns:** 9
- **Columns:**
  - `company`
  - `location`
  - `industry`
  - `total_laid_off`
  - `percentage_laid_off`
  - `date`
  - `stage`
  - `country`
  - `funds_raised_millions`

---

## 🎯 Objectives

1. Preserve raw data using staging tables.
2. Remove duplicates using `ROW_NUMBER()` and CTEs.
3. Standardize data fields (e.g., company, country, industry).
4. Handle missing and null values.
5. Convert the `date` field from text to proper `DATE` format.
6. Drop unnecessary or helper columns.

---

## 🛠️ Steps Performed

### 1. Create Staging Tables

```sql
CREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;
```

### 2. Remove Duplicates

```sql
WITH duplicate_cte AS (
  SELECT *, ROW_NUMBER() OVER (
    PARTITION BY company, location, industry, total_laid_off,
    percentage_laid_off, date, stage, country, funds_raised_millions
  ) AS row_num
  FROM layoffs_staging
)
DELETE FROM layoffs_staging2 WHERE row_num > 1;
```

### 3. Standardize Fields

- Trim whitespace from `company`
- Normalize `industry` (e.g., change `Crypto/Web3` to `Crypto`)
- Clean up `country` values (e.g., remove trailing `.`)

### 4. Format `date` Field

```sql
UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;
```

### 5. Handle Missing Values

- Set `''` (empty string) in `industry` to `NULL`
- Use self-joins to fill missing `industry` values from other rows
- Delete rows where both `total_laid_off` and `percentage_laid_off` are null

### 6. Drop Temporary Columns

```sql
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```

---

## ✅ Final Output

The `layoffs_staging2` table contains cleaned, deduplicated, and well-formatted data ready for querying and visualization.

---

## 🧠 Learnings

- How to use SQL window functions (`ROW_NUMBER()`)
- Data normalization and consistency practices
- Handling missing values using update joins
- Changing column data types safely in SQL

---

## 🧰 Tools & Tech

- MySQL
- SQL (Window Functions, CTEs, Joins)
- Dataset: `layoffs.csv`

---

## 📌 Author

- **Project by:** *Aaditya Aanand*

---

## 📎 Notes

You can import the dataset into MySQL using tools like MySQL Workbench or CLI:

```sql
LOAD DATA INFILE 'path/to/layoffs.csv'
INTO TABLE layoffs
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

Make sure `secure_file_priv` is configured correctly, or use a GUI like DBeaver for easier import.
