# devdolphins-pyspark-assignment
# DevDolphins PySpark Data Engineer Assignment

**Candidate:** Rudavath  Sai
**Role:** PySpark Data Engineer  
**Submission Date:** 3 July 2025

---

## 📌 Problem Statement
You are given:
- **`transactions.csv`**: Transaction data between customers and merchants
- **`CustomerImportance.csv`**: Transaction weights for different transaction types and customers

### Goal:
Design two real-time data processing mechanisms:

- **Mechanism X**:
  - Every second, read the next 10,000 transactions
  - Upload them to an S3 folder (`transactions_stream/`)

- **Mechanism Y**:
  - Monitor S3 for new transaction chunks
  - Detect specific patterns in transactions
  - Write 50 detections at a time to an S3 folder (`detections/`)
  - Track processed files using PostgreSQL

---

## 🧱 Architecture Diagram

```
DBFS (CSV) 
   │
   ▼
Mechanism X ──▶ S3 (transactions_stream/)
                         │
                         ▼
                   Mechanism Y
                         │
      ┌──────────────────┴──────────────────┐
      ▼                                     ▼
S3 (detections/)                    PostgreSQL (processed_files)
```

---

## 🧪 Pattern Detection Rules

### ✅ Pattern 1: `PatId1 - UPGRADE`
- Top 10% customer by number of transactions
- Bottom 10% weight (avg of transaction types)
- Merchant must have ≥ 50,000 transactions
- **ActionType:** `UPGRADE`

### ✅ Pattern 2: `PatId2 - CHILD`
- Avg transaction value < ₹23
- At least 80 transactions with that merchant
- **ActionType:** `CHILD`

### ✅ Pattern 3: `PatId3 - DEI-NEEDED`
- More male than female customers overall
- But female customers > 100
- **ActionType:** `DEI-NEEDED`

---

## 🗂 Project Structure

```
devdolphins-pyspark-assignment/
├── mechanism_x_upload_chunks.py     # Upload chunks to S3
├── mechanism_y_detect_patterns.py   # Detect patterns and write to S3
├── zip_detections_and_upload.py     # Compress and upload detections
├── README.md
├── sample_output/
│   └── detect_*.csv
```

---

## 📦 Output Example (Detection File)
```csv
YStartTime,detectionTime,patternId,actionType,customerName,MerchantId
2025-07-03 16:01:52,2025-07-03 16:01:52,PatId2,CHILD,customer_12,merchant_8
2025-07-03 16:01:52,2025-07-03 16:01:52,PatId3,DEI-NEEDED,,merchant_6
```
- Each detection file contains **50 records max**
- Stored in `s3://<bucket>/detections/`

---



## 🧑‍💻 How to Run

### ⚙️ Step 1: Setup AWS & PostgreSQL Credentials
Export AWS keys as environment variables or set up `.aws/credentials` locally.

### 🏃 Step 2: Run Mechanism X
Uploads 10K transactions per second to S3.
```bash
python mechanism_x_upload_chunks.py
```

### 🤖 Step 3: Run Mechanism Y
Continuously monitors S3 for chunks and writes detections.
```bash
python mechanism_y_detect_patterns.py
```

### 📦 Step 4: Zip Detection Files
```bash
python zip_detections_and_upload.py
```

---



---

## ✅ Assumptions Made
- `customer` in transactions = `CustomerName`
- `merchant` = `MerchantId`
- `category` = `TransactionType`
- No fraud field was used as per instructions
- Data is clean and schema matches given CSVs

---

## 🙌 Thank You!
> Looking forward to contributing to your data engineering team 🚀
