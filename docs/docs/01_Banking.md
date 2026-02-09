# Retail Banking Analytics
This document showcases Retail Banking domain knowledge mapped to data domains, KPIs, analytics use cases, and BI outputs.

**1 Business Scope**
| Area          | Description                                     |
| ------------- | ----------------------------------------------- |
| Customer Type | Individual Members & Small Businesses           |
| Products      | Checking, Savings, IRAs, Digital Banking, Zelle |
| Channels      | Branch, Online Banking, Mobile App              |
| Primary Goals | Member growth, deposit growth, digital adoption |

**2 Product Coverage**
| Product Category | Offerings                                       |
| ---------------- | ----------------------------------------------- |
| Deposit Accounts | Rewards Checking, Savings, Membership Savings   |
| Premium Banking  | Premier Rewards Banking                         |
| Business Banking | Business Checking & Savings                     |
| Digital Services | Online Banking, Mobile App, Zelle®, Debit Cards |
| Retirement       | IRAs                                            |

**3 Core Retail Banking Data Domains**
| Data Domain          | Description                             |
| -------------------- | --------------------------------------- |
| Member Master        | Member demographics, join date, segment |
| Account Master       | Checking & savings account details      |
| Transaction Data     | Deposits, withdrawals, transfers        |
| Digital Banking Logs | Login activity, device, channel         |
| Payment Services     | Zelle transactions                      |
| Business Accounts    | SMB account activity                    |

**4 Key KPIs (Executive + Operational)**
| KPI Category | KPI Name            | Business Meaning          |
| ------------ | ------------------- | ------------------------- |
| Growth       | Total Members       | Active retail members     |
| Growth       | Net New Members     | New joins minus closures  |
| Deposits     | Avg Daily Balance   | Liquidity indicator       |
| Deposits     | Deposit Growth %    | Period-over-period growth |
| Digital      | Digital Adoption %  | Online/mobile users       |
| Payments     | Zelle Txn Volume    | Peer-to-peer usage        |
| Engagement   | Products per Member | Cross-sell indicator      |

**5 Analytics Use Cases**
| Use Case                  | Description                        |
| ------------------------- | ---------------------------------- |
| Member Growth Analysis    | Track joins, exits, and net growth |
| Balance Trend Analysis    | Monitor deposit inflows/outflows   |
| Digital Adoption Analysis | Mobile vs web usage trends         |
| Zelle Monitoring          | Usage, volume, anomaly detection   |
| Segment Performance       | Retail vs Premier vs Business      |

**6 Power BI Dashboards**
| Dashboard                    | Key Insights                |
| ---------------------------- | --------------------------- |
| Retail Banking KPI Dashboard | Members, deposits, adoption |
| Member Engagement Dashboard  | Digital usage & activity    |
| Deposit Trends Dashboard     | Balance movement over time  |
| Digital Banking Dashboard    | Channel performance         |

**7 Sample Tables**

# 🏦 Retail Banking Logical Data Model (FTFCU-style) — Tables, Columns & Relationships

> **Purpose:** Copy-paste friendly data dictionary + relationships for a realistic Retail Banking analytics dataset (SQL Server / Power BI ready).

---

## ✅ Model Summary (Grain & Scope)

| Area | What it covers |
|---|---|
| Members | Member 360 attributes + history (SCD2) |
| Deposit Accounts | Checking/Savings account lifecycle + history (SCD2-lite) |
| Core Ledger | Posted transactions (high-volume) |
| Daily Snapshots | End-of-day balances per account |
| Digital Banking | Sessions + in-app events (clickstream) |
| Debit Card | Card master + card transactions |
| Revenue | Fees + interest postings |

---

## 1) Dimensions (Lookup / Descriptive)

### `dim_date`
**PK:** `date_key` (YYYYMMDD)

| Column | Type | Notes |
|---|---|---|
| date_key | INT | PK |
| full_date | DATE | |
| day_of_week | TINYINT | 1–7 |
| day_name | VARCHAR(10) | Mon… |
| month_num | TINYINT | 1–12 |
| month_name | VARCHAR(10) | Jan… |
| quarter_num | TINYINT | 1–4 |
| year_num | SMALLINT | |
| is_weekend | BIT | |
| is_holiday | BIT | Optional |

---

### `dim_member` (SCD Type 2)
**PK:** `member_sk` (surrogate)  
**Business Key:** `member_id`

| Column | Type | Notes |
|---|---|---|
| member_sk | BIGINT | PK |
| member_id | BIGINT | Business key |
| household_id | BIGINT | Group members |
| member_since_date_key | INT | FK → dim_date |
| member_status | VARCHAR(20) | Active/Closed/Frozen |
| segment | VARCHAR(20) | Standard/Premier/Startup |
| risk_rating | VARCHAR(10) | Low/Med/High |
| kyc_status | VARCHAR(20) | Verified/Pending |
| preferred_language | VARCHAR(20) | |
| employer_industry | VARCHAR(50) | |
| income_band | VARCHAR(20) | |
| effective_start_date_key | INT | FK → dim_date (SCD2) |
| effective_end_date_key | INT | FK → dim_date (SCD2) |
| is_current | BIT | SCD2 flag |

---

### `dim_address` (SCD Type 2)
**PK:** `address_sk`

| Column | Type | Notes |
|---|---|---|
| address_sk | BIGINT | PK |
| member_id | BIGINT | Business key link |
| address_line1 | VARCHAR(100) | |
| city | VARCHAR(50) | |
| state | CHAR(2) | |
| zip | VARCHAR(10) | |
| county | VARCHAR(50) | |
| country | VARCHAR(30) | |
| effective_start_date_key | INT | FK → dim_date |
| effective_end_date_key | INT | FK → dim_date |
| is_current | BIT | |

---

### `dim_branch`
**PK:** `branch_sk`

| Column | Type | Notes |
|---|---|---|
| branch_sk | INT | PK |
| branch_id | VARCHAR(20) | Business key |
| branch_name | VARCHAR(100) | |
| city | VARCHAR(50) | |
| state | CHAR(2) | |
| region | VARCHAR(30) | |
| open_date_key | INT | FK → dim_date |
| status | VARCHAR(20) | Open/Closed |

---

### `dim_channel`
**PK:** `channel_sk`

| Column | Type | Notes |
|---|---|---|
| channel_sk | INT | PK |
| channel_name | VARCHAR(30) | Branch/ATM/Mobile/Online/ACH/Debit/Zelle |
| channel_group | VARCHAR(30) | Physical/Digital/Payments |

---

### `dim_product` (Deposit products)
**PK:** `product_sk`

| Column | Type | Notes |
|---|---|---|
| product_sk | INT | PK |
| product_code | VARCHAR(20) | Business key |
| product_name | VARCHAR(100) | |
| product_type | VARCHAR(20) | Checking/Savings |
| tier | VARCHAR(20) | Standard/Premier |
| has_rewards | BIT | |
| monthly_fee_flag | BIT | |

---

### `dim_device` (High-cardinality)
**PK:** `device_sk`

| Column | Type | Notes |
|---|---|---|
| device_sk | BIGINT | PK |
| device_fingerprint | VARCHAR(64) | Unique |
| device_type | VARCHAR(20) | iOS/Android/Web |
| os_version | VARCHAR(30) | |
| app_version | VARCHAR(30) | |
| browser | VARCHAR(30) | |
| first_seen_date_key | INT | FK → dim_date |

---

## 2) Facts (High-volume)

### `fact_account` (SCD2-lite)
**PK:** `account_sk`  
**Business Key:** `account_id`  
**FKs:** member_sk, product_sk, branch_sk, date keys

| Column | Type | Notes |
|---|---|---|
| account_sk | BIGINT | PK |
| account_id | BIGINT | Business key |
| member_sk | BIGINT | FK → dim_member |
| product_sk | INT | FK → dim_product |
| primary_branch_sk | INT | FK → dim_branch |
| open_date_key | INT | FK → dim_date |
| close_date_key | INT | FK → dim_date (nullable) |
| account_status | VARCHAR(20) | Active/Closed/Dormant |
| overdraft_flag | BIT | |
| overdraft_limit | DECIMAL(12,2) | |
| statement_cycle_day | TINYINT | 1–31 |
| interest_rate_apr | DECIMAL(6,4) | |
| effective_start_date_key | INT | FK → dim_date |
| effective_end_date_key | INT | FK → dim_date |
| is_current | BIT | |

---

### `fact_transaction` (Core ledger)
**Grain:** 1 row per posted transaction  
**PK:** `txn_sk`  
**FKs:** account_sk, date_key, channel_sk, branch_sk

| Column | Type | Notes |
|---|---|---|
| txn_sk | BIGINT | PK |
| txn_id | VARCHAR(40) | Business id |
| post_date_key | INT | FK → dim_date |
| txn_datetime | DATETIME2 | |
| account_sk | BIGINT | FK → fact_account |
| channel_sk | INT | FK → dim_channel |
| branch_sk | INT | FK → dim_branch (nullable) |
| txn_type | VARCHAR(20) | Deposit/Withdrawal/Transfer/Fee/Interest |
| txn_subtype | VARCHAR(30) | ACH_IN/ACH_OUT/ATM_WD/DEBIT_PURCHASE/ZELLE |
| direction | VARCHAR(10) | CREDIT/DEBIT |
| amount | DECIMAL(14,2) | |
| running_balance | DECIMAL(14,2) | |
| merchant_name | VARCHAR(100) | Nullable |
| merchant_category | VARCHAR(50) | Nullable |
| status | VARCHAR(20) | Posted/Pending/Returned |
| is_reversal | BIT | |
| related_txn_sk | BIGINT | Nullable (reversal link) |

---

### `fact_daily_balance` (Snapshot)
**Grain:** account per day  
**PK:** `balance_sk`  
**FKs:** account_sk, date_key

| Column | Type | Notes |
|---|---|---|
| balance_sk | BIGINT | PK |
| date_key | INT | FK → dim_date |
| account_sk | BIGINT | FK → fact_account |
| end_of_day_balance | DECIMAL(14,2) | |
| available_balance | DECIMAL(14,2) | |
| holds_amount | DECIMAL(14,2) | |
| overdraft_used | DECIMAL(14,2) | |
| interest_accrued_daily | DECIMAL(12,6) | |

---

### `fact_digital_session`
**Grain:** 1 row per session  
**PK:** `session_sk`  
**FKs:** member_sk, device_sk, date_key, channel_sk

| Column | Type | Notes |
|---|---|---|
| session_sk | BIGINT | PK |
| member_sk | BIGINT | FK → dim_member |
| device_sk | BIGINT | FK → dim_device |
| date_key | INT | FK → dim_date |
| channel_sk | INT | FK → dim_channel (Mobile/Online) |
| session_start_ts | DATETIME2 | |
| session_end_ts | DATETIME2 | |
| ip_address | VARCHAR(45) | IPv4/IPv6 |
| geo_city | VARCHAR(50) | |
| geo_state | CHAR(2) | |
| mfa_used | BIT | |
| login_success | BIT | |
| failure_reason | VARCHAR(50) | Nullable |

---

### `fact_digital_event`
**Grain:** 1 row per in-app event  
**PK:** `event_sk`  
**FKs:** session_sk, member_sk, date_key

| Column | Type | Notes |
|---|---|---|
| event_sk | BIGINT | PK |
| session_sk | BIGINT | FK → fact_digital_session |
| member_sk | BIGINT | FK → dim_member |
| date_key | INT | FK → dim_date |
| event_ts | DATETIME2 | |
| event_name | VARCHAR(50) | ViewBalance/TransferInitiated/CardFreeze… |
| event_category | VARCHAR(30) | Navigation/Payments/Security |
| event_value | VARCHAR(100) | Nullable |

---

### `fact_debit_card` (SCD2)
**PK:** `card_sk`  
**FKs:** member_sk, linked_account_sk

| Column | Type | Notes |
|---|---|---|
| card_sk | BIGINT | PK |
| card_id | VARCHAR(40) | Business id |
| member_sk | BIGINT | FK → dim_member |
| linked_account_sk | BIGINT | FK → fact_account |
| card_status | VARCHAR(20) | Active/Blocked/Expired |
| issue_date_key | INT | FK → dim_date |
| expiry_month | TINYINT | 1–12 |
| expiry_year | SMALLINT | |
| tokenized_flag | BIT | ApplePay/GooglePay tokenized |
| effective_start_date_key | INT | FK → dim_date |
| effective_end_date_key | INT | FK → dim_date |
| is_current | BIT | |

---

### `fact_card_transaction`
**Grain:** 1 row per card auth/posted item  
**PK:** `card_txn_sk`  
**FKs:** card_sk, date_key, channel_sk

| Column | Type | Notes |
|---|---|---|
| card_txn_sk | BIGINT | PK |
| card_sk | BIGINT | FK → fact_debit_card |
| date_key | INT | FK → dim_date |
| txn_ts | DATETIME2 | |
| channel_sk | INT | FK → dim_channel (Debit/CardPresent/CNP) |
| merchant_name | VARCHAR(100) | |
| mcc | VARCHAR(10) | Merchant category code |
| amount | DECIMAL(14,2) | |
| auth_result | VARCHAR(20) | Approved/Declined |
| decline_reason | VARCHAR(30) | Nullable |
| is_fraud_suspected | BIT | |
| dispute_flag | BIT | |

---

### `fact_fee_assessment`
**PK:** `fee_sk`  
**FKs:** account_sk, date_key

| Column | Type | Notes |
|---|---|---|
| fee_sk | BIGINT | PK |
| date_key | INT | FK → dim_date |
| account_sk | BIGINT | FK → fact_account |
| fee_type | VARCHAR(30) | MonthlyMaint/OD/ATMForeign… |
| fee_amount | DECIMAL(12,2) | |
| waived_flag | BIT | |
| waiver_reason | VARCHAR(50) | Nullable |

---

### `fact_interest_posting`
**PK:** `interest_sk`  
**FKs:** account_sk, date_key

| Column | Type | Notes |
|---|---|---|
| interest_sk | BIGINT | PK |
| date_key | INT | FK → dim_date |
| account_sk | BIGINT | FK → fact_account |
| interest_amount | DECIMAL(12,6) | |
| interest_rate_apr | DECIMAL(6,4) | |
| interest_basis | VARCHAR(30) | DailyAvgBalance/EOD |

---

## 3) Relationship Map (PK → FK)

| Parent (PK) | Child (FK) | Relationship |
|---|---|---|
| dim_date.date_key | dim_member.member_since_date_key | 1 → many |
| dim_date.date_key | dim_member.effective_*_date_key | 1 → many |
| dim_date.date_key | dim_address.effective_*_date_key | 1 → many |
| dim_date.date_key | fact_account.open/close/effective_*_date_key | 1 → many |
| dim_date.date_key | fact_transaction.post_date_key | 1 → many |
| dim_date.date_key | fact_daily_balance.date_key | 1 → many |
| dim_date.date_key | fact_digital_session.date_key | 1 → many |
| dim_date.date_key | fact_digital_event.date_key | 1 → many |
| dim_date.date_key | fact_debit_card.issue/effective_*_date_key | 1 → many |
| dim_date.date_key | fact_card_transaction.date_key | 1 → many |
| dim_date.date_key | fact_fee_assessment.date_key | 1 → many |
| dim_date.date_key | fact_interest_posting.date_key | 1 → many |
| dim_member.member_sk | fact_account.member_sk | 1 → many |
| dim_product.product_sk | fact_account.product_sk | 1 → many |
| dim_branch.branch_sk | fact_account.primary_branch_sk | 1 → many |
| fact_account.account_sk | fact_transaction.account_sk | 1 → many |
| fact_account.account_sk | fact_daily_balance.account_sk | 1 → many |
| dim_channel.channel_sk | fact_transaction.channel_sk | 1 → many |
| dim_branch.branch_sk | fact_transaction.branch_sk | 1 → many (nullable) |
| dim_device.device_sk | fact_digital_session.device_sk | 1 → many |
| dim_channel.channel_sk | fact_digital_session.channel_sk | 1 → many |
| fact_digital_session.session_sk | fact_digital_event.session_sk | 1 → many |
| dim_member.member_sk | fact_digital_session.member_sk | 1 → many |
| dim_member.member_sk | fact_digital_event.member_sk | 1 → many |
| dim_member.member_sk | fact_debit_card.member_sk | 1 → many |
| fact_account.account_sk | fact_debit_card.linked_account_sk | 1 → many |
| fact_debit_card.card_sk | fact_card_transaction.card_sk | 1 → many |
| dim_channel.channel_sk | fact_card_transaction.channel_sk | 1 → many |
| fact_account.account_sk | fact_fee_assessment.account_sk | 1 → many |
| fact_account.account_sk | fact_interest_posting.account_sk | 1 → many |

---


**8 Data Flow (End-to-End)**

    -> Source Systems
      
      -> Ingestion
      
        -> Staging
        
          -> Enterprise Data Warehouse (EDW)
            
            -> Retail Banking Data Mart
          
              -> KPI Calculation Layer
            
                -> Semantic / BI Layer
              
                  -> Reporting Layer

