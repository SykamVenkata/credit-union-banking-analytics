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

# Retail Banking Logical Data Model (FTFCU-style) — Tables, Columns & Relationships

---

## Model Summary (Grain & Scope)

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

## dim_date — Sample Data (10 Records)

| date_key | full_date  | day_of_week | day_name | month_num | month_name | quarter_num | year_num | is_weekend | is_holiday |
|---------:|------------|-------------|----------|-----------|------------|-------------|----------|------------|------------|
| 20250127 | 2025-01-27 | 1 | Monday   | 1 | January | 1 | 2025 | 0 | 0 |
| 20250128 | 2025-01-28 | 2 | Tuesday  | 1 | January | 1 | 2025 | 0 | 0 |
| 20250129 | 2025-01-29 | 3 | Wednesday| 1 | January | 1 | 2025 | 0 | 0 |
| 20250130 | 2025-01-30 | 4 | Thursday | 1 | January | 1 | 2025 | 0 | 0 |
| 20250131 | 2025-01-31 | 5 | Friday   | 1 | January | 1 | 2025 | 0 | 0 |
| 20250201 | 2025-02-01 | 6 | Saturday | 2 | February| 1 | 2025 | 1 | 0 |
| 20250202 | 2025-02-02 | 7 | Sunday   | 2 | February| 1 | 2025 | 1 | 0 |
| 20250203 | 2025-02-03 | 1 | Monday   | 2 | February| 1 | 2025 | 0 | 0 |
| 20250204 | 2025-02-04 | 2 | Tuesday  | 2 | February| 1 | 2025 | 0 | 0 |
| 20250704 | 2025-07-04 | 5 | Friday   | 7 | July    | 3 | 2025 | 0 | 1 |

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

## dim_member (SCD Type 2) — Sample Data (10 Records)

| member_sk | member_id | household_id | member_since_date_key | member_status | segment  | risk_rating | kyc_status | preferred_language | employer_industry | income_band | effective_start_date_key | effective_end_date_key | is_current |
|----------:|----------:|-------------:|-----------------------:|---------------|----------|-------------|------------|--------------------|-------------------|-------------|--------------------------:|------------------------:|-----------:|
| 100001 | 500001 | 90001 | 20180315 | Active | Standard | Low | Verified | English | Technology | 75K–100K | 20180315 | 20231231 | 0 |
| 100002 | 500001 | 90001 | 20180315 | Active | Premier  | Low | Verified | English | Technology | 100K–150K | 20240101 | 99991231 | 1 |
| 100003 | 500002 | 90002 | 20200620 | Active | Standard | Medium | Verified | English | Healthcare | 50K–75K | 20200620 | 99991231 | 1 |
| 100004 | 500003 | 90003 | 20190510 | Active | Standard | Low | Verified | English | Finance | 75K–100K | 20190510 | 99991231 | 1 |
| 100005 | 500004 | 90004 | 20170118 | Active | Premier | Low | Verified | English | Manufacturing | 150K+ | 20170118 | 99991231 | 1 |
| 100006 | 500005 | 90005 | 20210422 | Active | Standard | Medium | Pending | Spanish | Retail | 50K–75K | 20210422 | 99991231 | 1 |
| 100007 | 500006 | 90006 | 20191202 | Frozen | Standard | High | Verified | English | Transportation | 25K–50K | 20191202 | 20241031 | 0 |
| 100008 | 500006 | 90006 | 20191202 | Active | Standard | Medium | Verified | English | Transportation | 50K–75K | 20241101 | 99991231 | 1 |
| 100009 | 500007 | 90007 | 20220714 | Active | Startup | Medium | Pending | English | Startup | 75K–100K | 20220714 | 99991231 | 1 |
| 100010 | 500008 | 90008 | 20160209 | Closed | Standard | High | Verified | English | Hospitality | 25K–50K | 20160209 | 20230630 | 0 |

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

## dim_address (SCD Type 2) — Sample Data (10 Records)

| address_sk | member_id | address_line1        | city       | state | zip   | county       | country | effective_start_date_key | effective_end_date_key | is_current |
|-----------:|----------:|----------------------|------------|:-----:|:-----:|--------------|---------|--------------------------:|------------------------:|-----------:|
| 200001 | 500001 | 1123 SW Alder St Apt 5 | Portland   | OR | 97205 | Multnomah    | USA | 20180315 | 20231231 | 0 |
| 200002 | 500001 | 450 Pine St Unit 12     | Seattle    | WA | 98101 | King         | USA | 20240101 | 99991231 | 1 |
| 200003 | 500002 | 89 N 4th St             | San Jose   | CA | 95112 | Santa Clara  | USA | 20200620 | 99991231 | 1 |
| 200004 | 500003 | 210 Congress Ave        | Austin     | TX | 78701 | Travis       | USA | 20190510 | 99991231 | 1 |
| 200005 | 500004 | 1700 Main St            | Dallas     | TX | 75201 | Dallas       | USA | 20170118 | 20210630 | 0 |
| 200006 | 500004 | 7800 Preston Rd         | Plano      | TX | 75024 | Collin       | USA | 20210701 | 99991231 | 1 |
| 200007 | 500005 | 3400 N Central Ave      | Phoenix    | AZ | 85012 | Maricopa     | USA | 20210422 | 99991231 | 1 |
| 200008 | 500006 | 1550 17th St            | Denver     | CO | 80202 | Denver       | USA | 20191202 | 20241031 | 0 |
| 200009 | 500006 | 8700 E Florida Ave      | Denver     | CO | 80231 | Denver       | USA | 20241101 | 99991231 | 1 |
| 200010 | 500007 | 1200 N Westshore Blvd   | Tampa      | FL | 33607 | Hillsborough | USA | 20220714 | 99991231 | 1 |

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

## dim_branch — Sample Data (10 Records)

| branch_sk | branch_id | branch_name                 | city         | state | region        | open_date_key | status |
|----------:|-----------|-----------------------------|--------------|:-----:|---------------|---------------:|--------|
| 1 | BR-OR-001 | Portland Downtown Branch     | Portland     | OR | Pacific NW    | 20010515 | Open |
| 2 | BR-WA-002 | Seattle Central Branch       | Seattle      | WA | Pacific NW    | 20030420 | Open |
| 3 | BR-CA-003 | San Jose Main Branch         | San Jose     | CA | West          | 20070910 | Open |
| 4 | BR-CA-004 | San Francisco Market Branch  | San Francisco| CA | West          | 20110325 | Open |
| 5 | BR-TX-005 | Austin Central Branch        | Austin       | TX | Southwest     | 20150518 | Open |
| 6 | BR-TX-006 | Dallas Uptown Branch         | Dallas       | TX | Southwest     | 20170801 | Open |
| 7 | BR-AZ-007 | Phoenix Downtown Branch      | Phoenix      | AZ | Southwest     | 20190214 | Open |
| 8 | BR-CO-008 | Denver Union Station Branch  | Denver       | CO | Mountain West | 20200110 | Open |
| 9 | BR-FL-009 | Tampa Bay Branch             | Tampa        | FL | Southeast     | 20210630 | Open |
| 10 | BR-IL-010 | Chicago Loop Branch          | Chicago      | IL | Midwest       | 20120409 | Closed |

---

### `dim_channel`
**PK:** `channel_sk`

| Column | Type | Notes |
|---|---|---|
| channel_sk | INT | PK |
| channel_name | VARCHAR(30) | Branch/ATM/Mobile/Online/ACH/Debit/Zelle |
| channel_group | VARCHAR(30) | Physical/Digital/Payments |

## dim_channel — Sample Data (10 Records)

| channel_sk | channel_name | channel_group |
|-----------:|--------------|---------------|
| 1 | Branch | Physical |
| 2 | ATM | Physical |
| 3 | Mobile | Digital |
| 4 | Online | Digital |
| 5 | ACH | Payments |
| 6 | Debit | Payments |
| 7 | Zelle | Payments |
| 8 | Wire | Payments |
| 9 | Call Center | Physical |
| 10 | IVR | Digital |

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

## dim_product (Deposit Products) — Sample Data (10 Records)

| product_sk | product_code | product_name                   | product_type | tier      | has_rewards | monthly_fee_flag |
|-----------:|--------------|--------------------------------|--------------|-----------|-------------|------------------|
| 1 | CHK_STD | Free Checking                   | Checking | Standard | 0 | 0 |
| 2 | CHK_PRM | Premier Checking                | Checking | Premier  | 1 | 1 |
| 3 | CHK_STU | Student Checking                | Checking | Standard | 0 | 0 |
| 4 | CHK_BUS | Business Checking               | Checking | Standard | 0 | 1 |
| 5 | SAV_STD | Savings Account                 | Savings  | Standard | 0 | 0 |
| 6 | SAV_PRM | High-Yield Savings              | Savings  | Premier  | 1 | 0 |
| 7 | SAV_JR  | Junior Savings                  | Savings  | Standard | 0 | 0 |
| 8 | MMA_STD | Money Market Account            | Savings  | Standard | 0 | 1 |
| 9 | MMA_PRM | Premier Money Market            | Savings  | Premier  | 1 | 1 |
| 10 | CD_STD | Certificate of Deposit          | Savings  | Standard | 0 | 0 |

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

## dim_device (High-Cardinality) — Sample Data (10 Records)

| device_sk | device_fingerprint                     | device_type | os_version      | app_version | browser | first_seen_date_key |
|----------:|----------------------------------------|-------------|-----------------|-------------|---------|---------------------:|
| 900001 | a9f3c8d1e7b24f8a9c1d4e6b7a2f9012 | iOS | iOS 17.2 | 6.4.1 | Safari | 20240105 |
| 900002 | b1e4a7c9d8f2301a9c7b6e5d4f3a8210 | Android | Android 14 | 6.4.1 | Chrome | 20240108 |
| 900003 | c7d2f1a8e9b4035c6a7d5f4e1b290876 | Web | Windows 11 | NA | Edge | 20240110 |
| 900004 | d9a1c2f8e7b6045a3f1d2e9c8b7a0123 | Web | macOS 14 | NA | Safari | 20240112 |
| 900005 | e3b7a9d4c8f1e2056a7b2d9c410a6587 | iOS | iOS 16.6 | 6.3.8 | Safari | 20231222 |
| 900006 | f5a1c7d8b2e9f0346c7a5d9012e4b386 | Android | Android 13 | 6.3.5 | Chrome | 20231115 |
| 900007 | 1a9d5c4f2b8e7a0c6d3f9e41b570268d | iOS | iOS 17.1 | 6.4.0 | Safari | 20240102 |
| 900008 | 2f8d7e9c4a0b5d613e1c9a72f845b306 | Web | Windows 10 | NA | Chrome | 20231018 |
| 900009 | 3e7f1b4d8a9c0f562d1e7a9b4c058236 | Android | Android 12 | 6.2.9 | Chrome | 20230909 |
| 900010 | 4c1b7e9a3f0d8a5629c4e1b705d23689 | Web | macOS 13 | NA | Safari | 20230827 |

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

## fact_account (SCD2-lite) — Sample Data (10 Records)

> Notes:
> - Includes **SCD2-lite behavior** (same `account_id` with a historical row + current row).
> - Uses realistic banking attributes (OD flags/limits, APRs, statement cycles).
> - `99991231` denotes the open-ended current record.

| account_sk | account_id | member_sk | product_sk | primary_branch_sk | open_date_key | close_date_key | account_status | overdraft_flag | overdraft_limit | statement_cycle_day | interest_rate_apr | effective_start_date_key | effective_end_date_key | is_current |
|-----------:|-----------:|----------:|-----------:|------------------:|--------------:|---------------:|----------------|----------------|-----------------:|-------------------:|------------------:|--------------------------:|------------------------:|-----------:|
| 300001 | 700001 | 100001 | 2 | 1 | 20180315 | NULL | Active | 1 | 500.00 | 15 | 0.0150 | 20180315 | 20231231 | 0 |
| 300002 | 700001 | 100001 | 2 | 1 | 20180315 | NULL | Active | 1 | 750.00 | 15 | 0.0150 | 20240101 | 99991231 | 1 |
| 300003 | 700002 | 100003 | 1 | 2 | 20200620 | NULL | Active | 0 | 0.00 | 10 | 0.0000 | 20200620 | 99991231 | 1 |
| 300004 | 700003 | 100004 | 5 | 3 | 20190510 | NULL | Active | 0 | 0.00 | 20 | 0.0200 | 20190510 | 99991231 | 1 |
| 300005 | 700004 | 100005 | 6 | 4 | 20170118 | NULL | Active | 0 | 0.00 | 25 | 0.0250 | 20170118 | 99991231 | 1 |
| 300006 | 700005 | 100006 | 1 | 5 | 20210422 | NULL | Active | 1 | 300.00 | 5 | 0.0000 | 20210422 | 99991231 | 1 |
| 300007 | 700006 | 100008 | 3 | 6 | 20191202 | NULL | Dormant | 0 | 0.00 | 12 | 0.0000 | 20191202 | 99991231 | 1 |
| 300008 | 700007 | 100009 | 4 | 7 | 20220714 | NULL | Active | 1 | 1000.00 | 18 | 0.0100 | 20220714 | 99991231 | 1 |
| 300009 | 700008 | 100010 | 1 | 8 | 20160209 | 20230630 | Closed | 0 | 0.00 | 8 | 0.0000 | 20160209 | 20230630 | 0 |
| 300010 | 700009 | 100003 | 5 | 9 | 20240201 | NULL | Active | 0 | 0.00 | 22 | 0.0180 | 20240201 | 99991231 | 1 |

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


## fact_transaction (Core Ledger) — Sample Data (10 Records)

> Notes:
> - Mix of deposits, withdrawals, transfers, fees, and interest.
> - `branch_sk` is **nullable** (e.g., online/ACH/card-originated items).
> - Merchant fields are populated mainly for **debit purchases**.
> - Includes a realistic **reversal pair** using `is_reversal` + `related_txn_sk`.

| txn_sk | txn_id | post_date_key | txn_datetime           | account_sk | channel_sk | branch_sk | txn_type     | txn_subtype      | direction | amount   | running_balance | merchant_name              | merchant_category | status  | is_reversal | related_txn_sk |
|------:|--------|--------------:|------------------------|-----------:|-----------:|----------:|--------------|------------------|----------|---------:|----------------:|----------------------------|------------------|---------|-----------:|---------------:|
| 800001 | TXN-20250201-000001 | 20250201 | 2025-02-01 09:12:45.000 | 300002 | 5 | NULL | Deposit      | ACH_IN           | CREDIT   | 2500.00 | 12500.00 | NULL                       | NULL             | Posted  | 0 | NULL |
| 800002 | TXN-20250201-000002 | 20250201 | 2025-02-01 12:34:10.000 | 300002 | 6 | NULL | Withdrawal   | DEBIT_PURCHASE   | DEBIT    | -45.90  | 12454.10 | Starbucks                  | Coffee Shops      | Posted  | 0 | NULL |
| 800003 | TXN-20250202-000003 | 20250202 | 2025-02-02 08:05:22.000 | 300003 | 3 | NULL | Transfer     | INTERNAL_XFER     | DEBIT    | -300.00 | 2200.50  | NULL                       | NULL             | Posted  | 0 | NULL |
| 800004 | TXN-20250202-000004 | 20250202 | 2025-02-02 08:05:23.000 | 300010 | 3 | NULL | Transfer     | INTERNAL_XFER     | CREDIT   | 300.00  | 8750.75  | NULL                       | NULL             | Posted  | 0 | NULL |
| 800005 | TXN-20250203-000005 | 20250203 | 2025-02-03 14:18:09.000 | 300006 | 2 | NULL | Withdrawal   | ATM_WD           | DEBIT    | -120.00 | 980.25   | NULL                       | NULL             | Posted  | 0 | NULL |
| 800006 | TXN-20250204-000006 | 20250204 | 2025-02-04 17:41:55.000 | 300007 | 4 | NULL | Fee          | MONTHLY_MAINT_FEE | DEBIT    | -12.00  | 410.10   | NULL                       | NULL             | Posted  | 0 | NULL |
| 800007 | TXN-20250205-000007 | 20250205 | 2025-02-05 10:09:31.000 | 300004 | 1 | 3    | Deposit      | CASH_DEPOSIT     | CREDIT   | 800.00  | 5600.00  | NULL                       | NULL             | Posted  | 0 | NULL |
| 800008 | TXN-20250206-000008 | 20250206 | 2025-02-06 06:45:00.000 | 300005 | 7 | NULL | Transfer     | ZELLE            | DEBIT    | -75.00  | 15425.00 | NULL                       | NULL             | Posted  | 0 | NULL |
| 800009 | TXN-20250206-000009 | 20250206 | 2025-02-06 23:59:59.000 | 300010 | 4 | NULL | Interest     | MONTHLY_INT      | CREDIT   | 12.35   | 8763.10  | NULL                       | NULL             | Posted  | 0 | NULL |
| 800010 | TXN-20250207-000010 | 20250207 | 2025-02-07 11:22:14.000 | 300002 | 6 | NULL | Withdrawal   | DEBIT_PURCHASE   | DEBIT    | -89.99  | 12364.11 | Amazon                     | Online Retail     | Posted  | 1 | 800002 |

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

## fact_daily_balance (Snapshot) — Sample Data (10 Records)

> Notes:
> - Grain = **1 row per account per day**
> - `available_balance` reflects holds/overdraft usage.
> - Interest accrual shown mainly for savings/high-yield products (but can exist for any interest-bearing account).

| balance_sk | date_key  | account_sk | end_of_day_balance | available_balance | holds_amount | overdraft_used | interest_accrued_daily |
|----------:|----------:|-----------:|-------------------:|------------------:|-------------:|---------------:|------------------------:|
| 600001 | 20250201 | 300002 | 12500.00 | 12500.00 | 0.00  | 0.00  | 0.514000 |
| 600002 | 20250202 | 300002 | 12454.10 | 12454.10 | 0.00  | 0.00  | 0.512000 |
| 600003 | 20250203 | 300002 | 12454.10 | 12404.10 | 50.00 | 0.00  | 0.512000 |
| 600004 | 20250204 | 300007 | 410.10   | 410.10   | 0.00  | 0.00  | 0.000000 |
| 600005 | 20250205 | 300004 | 5600.00  | 5600.00  | 0.00  | 0.00  | 0.306000 |
| 600006 | 20250206 | 300005 | 15425.00 | 15425.00 | 0.00  | 0.00  | 1.056000 |
| 600007 | 20250206 | 300010 | 8750.75  | 8750.75  | 0.00  | 0.00  | 0.432000 |
| 600008 | 20250207 | 300002 | 12364.11 | 12364.11 | 0.00  | 0.00  | 0.508000 |
| 600009 | 20250207 | 300006 | 980.25   | 980.25   | 0.00  | 0.00  | 0.000000 |
| 600010 | 20250202 | 300003 | 2200.50  | 2150.50  | 50.00 | 0.00  | 0.000000 |

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

## fact_digital_session — Sample Data (10 Records)

> Notes:
> - Grain = **1 row per digital banking session**
> - Covers **Mobile** and **Online** channels only
> - Includes **successful and failed logins**
> - MFA usage varies by risk and device

| session_sk | member_sk | device_sk | date_key | channel_sk | session_start_ts        | session_end_ts          | ip_address       | geo_city   | geo_state | mfa_used | login_success | failure_reason        |
|-----------:|----------:|----------:|---------:|-----------:|--------------------------|--------------------------|------------------|------------|:---------:|---------:|--------------:|-----------------------|
| 910001 | 100001 | 900001 | 20250201 | 3 | 2025-02-01 08:12:45.000 | 2025-02-01 08:28:10.000 | 73.162.45.10     | Portland   | OR | 1 | 1 | NULL |
| 910002 | 100001 | 900003 | 20250201 | 4 | 2025-02-01 20:45:02.000 | 2025-02-01 20:50:31.000 | 98.214.66.77     | Portland   | OR | 0 | 1 | NULL |
| 910003 | 100003 | 900002 | 20250202 | 3 | 2025-02-02 07:15:19.000 | 2025-02-02 07:22:03.000 | 184.22.119.55    | San Jose   | CA | 1 | 1 | NULL |
| 910004 | 100004 | 900004 | 20250202 | 4 | 2025-02-02 22:01:40.000 | 2025-02-02 22:04:55.000 | 54.188.120.99    | Austin     | TX | 0 | 1 | NULL |
| 910005 | 100006 | 900006 | 20250203 | 3 | 2025-02-03 06:58:10.000 | 2025-02-03 06:58:35.000 | 99.76.211.44     | Phoenix    | AZ | 1 | 0 | Invalid OTP |
| 910006 | 100006 | 900006 | 20250203 | 3 | 2025-02-03 07:01:02.000 | 2025-02-03 07:15:44.000 | 99.76.211.44     | Phoenix    | AZ | 1 | 1 | NULL |
| 910007 | 100007 | 900008 | 20250204 | 4 | 2025-02-04 19:33:27.000 | 2025-02-04 19:33:52.000 | 172.58.91.18     | Denver     | CO | 1 | 0 | Account Frozen |
| 910008 | 100008 | 900007 | 20250205 | 3 | 2025-02-05 09:42:11.000 | 2025-02-05 09:49:20.000 | 23.201.87.66     | Denver     | CO | 1 | 1 | NULL |
| 910009 | 100009 | 900009 | 20250206 | 3 | 2025-02-06 14:26:33.000 | 2025-02-06 14:35:08.000 | 67.81.190.121    | Tampa      | FL | 0 | 1 | NULL |
| 910010 | 100010 | 900010 | 20250207 | 4 | 2025-02-07 21:58:04.000 | 2025-02-07 22:01:17.000 | 18.212.45.200    | Chicago    | IL | 0 | 1 | NULL |

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

## fact_digital_event — Sample Data (10 Records)

> Notes:
> - Grain = **1 row per in-app event**
> - Events are tied to **sessions, members, and dates**
> - Covers **navigation, payments, and security actions**
> - `event_value` is populated only when context is needed

| event_sk | session_sk | member_sk | date_key | event_ts               | event_name            | event_category | event_value |
|---------:|-----------:|----------:|---------:|------------------------|-----------------------|----------------|-------------|
| 920001 | 910001 | 100001 | 20250201 | 2025-02-01 08:13:05.000 | ViewBalance           | Navigation     | Checking |
| 920002 | 910001 | 100001 | 20250201 | 2025-02-01 08:14:22.000 | ViewTransactions      | Navigation     | Last 30 Days |
| 920003 | 910001 | 100001 | 20250201 | 2025-02-01 08:16:48.000 | TransferInitiated     | Payments       | Internal Transfer |
| 920004 | 910002 | 100001 | 20250201 | 2025-02-01 20:46:10.000 | CardFreeze            | Security       | Debit Card |
| 920005 | 910003 | 100003 | 20250202 | 2025-02-02 07:16:02.000 | BillPayInitiated      | Payments       | Electricity |
| 920006 | 910004 | 100004 | 20250202 | 2025-02-02 22:02:15.000 | ProfileUpdated        | Navigation     | Address Change |
| 920007 | 910006 | 100006 | 20250203 | 2025-02-03 07:05:44.000 | MFAChallengeCompleted | Security       | SMS OTP |
| 920008 | 910008 | 100008 | 20250205 | 2025-02-05 09:43:18.000 | ZellePaymentInitiated | Payments       | $75.00 |
| 920009 | 910009 | 100009 | 20250206 | 2025-02-06 14:27:50.000 | ViewStatements        | Navigation     | Jan 2025 |
| 920010 | 910010 | 100010 | 20250207 | 2025-02-07 22:00:05.000 | Logout                | Navigation     | NULL |

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

## fact_debit_card (SCD Type 2) — Sample Data (10 Records)

> Notes:
> - Demonstrates **card reissue / replacement** using SCD2.
> - Only one active (`is_current = 1`) card per physical card instance.
> - Tokenization indicates Apple Pay / Google Pay usage.

| card_sk | card_id | member_sk | linked_account_sk | card_status | issue_date_key | expiry_month | expiry_year | tokenized_flag | effective_start_date_key | effective_end_date_key | is_current |
|--------:|---------|----------:|------------------:|-------------|---------------:|-------------:|------------:|---------------:|--------------------------:|------------------------:|-----------:|
| 400001 | CARD-700001-A | 100001 | 300002 | Expired | 20180320 | 03 | 2023 | 0 | 20180320 | 20230331 | 0 |
| 400002 | CARD-700001-B | 100001 | 300002 | Active  | 20230401 | 04 | 2027 | 1 | 20230401 | 99991231 | 1 |
| 400003 | CARD-700002-A | 100003 | 300003 | Active  | 20200625 | 06 | 2026 | 0 | 20200625 | 99991231 | 1 |
| 400004 | CARD-700003-A | 100004 | 300004 | Active  | 20190515 | 05 | 2025 | 1 | 20190515 | 99991231 | 1 |
| 400005 | CARD-700004-A | 100005 | 300005 | Expired | 20170125 | 01 | 2021 | 0 | 20170125 | 20210131 | 0 |
| 400006 | CARD-700004-B | 100005 | 300005 | Active  | 20210201 | 02 | 2026 | 1 | 20210201 | 99991231 | 1 |
| 400007 | CARD-700005-A | 100006 | 300006 | Blocked | 20210430 | 04 | 2024 | 0 | 20210430 | 20240630 | 0 |
| 400008 | CARD-700005-B | 100006 | 300006 | Active  | 20240701 | 07 | 2028 | 1 | 20240701 | 99991231 | 1 |
| 400009 | CARD-700006-A | 100008 | 300007 | Active  | 20191210 | 12 | 2024 | 0 | 20191210 | 99991231 | 1 |
| 400010 | CARD-700007-A | 100009 | 300008 | Active  | 20220720 | 07 | 2027 | 1 | 20220720 | 99991231 | 1 |

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

## fact_card_transaction — Sample Data (10 Records)

> Notes:
> - Grain = **1 row per card authorization / posted item**
> - Mix of **card-present** and **card-not-present (CNP)** purchases
> - Includes **approved and declined** transactions
> - Fraud and dispute flags reflect realistic post-auth scenarios

| card_txn_sk | card_sk | date_key | txn_ts                 | channel_sk | merchant_name        | mcc  | amount | auth_result | decline_reason        | is_fraud_suspected | dispute_flag |
|------------:|--------:|---------:|------------------------|-----------:|----------------------|------|-------:|-------------|-----------------------|-------------------:|-------------:|
| 950001 | 400002 | 20250201 | 2025-02-01 12:41:22.000 | 6 | Amazon               | 5942 | 89.99  | Approved | NULL                  | 0 | 0 |
| 950002 | 400002 | 20250202 | 2025-02-02 09:18:44.000 | 6 | Starbucks            | 5814 | 6.45   | Approved | NULL                  | 0 | 0 |
| 950003 | 400003 | 20250202 | 2025-02-02 17:55:03.000 | 6 | Walmart              | 5411 | 124.60 | Approved | NULL                  | 0 | 0 |
| 950004 | 400004 | 20250203 | 2025-02-03 20:11:36.000 | 6 | Shell Oil            | 5541 | 52.30  | Approved | NULL                  | 0 | 0 |
| 950005 | 400006 | 20250204 | 2025-02-04 23:42:59.000 | 6 | Apple App Store      | 5817 | 14.99  | Approved | NULL                  | 0 | 0 |
| 950006 | 400008 | 20250205 | 2025-02-05 03:12:18.000 | 6 | Unknown Merchant     | 5999 | 980.00 | Declined | Suspected Fraud       | 1 | 0 |
| 950007 | 400008 | 20250205 | 2025-02-05 08:34:50.000 | 6 | Target               | 5331 | 215.75 | Approved | NULL                  | 0 | 0 |
| 950008 | 400009 | 20250206 | 2025-02-06 13:09:27.000 | 6 | Uber                 | 4121 | 22.40  | Approved | NULL                  | 0 | 0 |
| 950009 | 400010 | 20250206 | 2025-02-06 19:56:11.000 | 6 | Home Depot           | 5200 | 348.90 | Approved | NULL                  | 0 | 1 |
| 950010 | 400002 | 20250207 | 2025-02-07 01:21:07.000 | 6 | Netflix              | 4899 | 19.99  | Approved | NULL                  | 0 | 0 |

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

## fact_fee_assessment — Sample Data (10 Records)

> Notes:
> - Shows **monthly maintenance**, **overdraft**, and **ATM foreign** fees
> - Includes **waived fees** with realistic waiver reasons
> - Tied cleanly to `fact_account` and `dim_date`

| fee_sk | date_key | account_sk | fee_type            | fee_amount | waived_flag | waiver_reason              |
|------:|---------:|-----------:|---------------------|-----------:|------------:|----------------------------|
| 980001 | 20250201 | 300002 | MonthlyMaint        | 12.00 | 0 | NULL |
| 980002 | 20250202 | 300002 | Overdraft           | 35.00 | 1 | Premier Account Waiver |
| 980003 | 20250203 | 300003 | ATMForeign          | 3.50  | 0 | NULL |
| 980004 | 20250203 | 300006 | MonthlyMaint        | 10.00 | 0 | NULL |
| 980005 | 20250204 | 300007 | DormantAccountFee   | 5.00  | 0 | NULL |
| 980006 | 20250204 | 300004 | Overdraft           | 35.00 | 1 | First-Time Courtesy |
| 980007 | 20250205 | 300008 | MonthlyMaint        | 15.00 | 0 | NULL |
| 980008 | 20250206 | 300010 | ATMForeign          | 3.50  | 1 | ATM Fee Reimbursement |
| 980009 | 20250206 | 300005 | ExcessWithdrawal    | 10.00 | 0 | NULL |
| 980010 | 20250207 | 300002 | MonthlyMaint        | 12.00 | 1 | Balance Threshold Met |

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

## fact_interest_posting — Sample Data (10 Records)

> Notes:
> - Represents **interest credited to deposit accounts**
> - Mix of **daily accrual–based** and **EOD balance–based** calculations
> - Amounts align with balances shown in `fact_daily_balance`

| interest_sk | date_key | account_sk | interest_amount | interest_rate_apr | interest_basis        |
|------------:|---------:|-----------:|----------------:|------------------:|-----------------------|
| 990001 | 20250201 | 300002 | 0.514000 | 0.0150 | DailyAvgBalance |
| 990002 | 20250202 | 300002 | 0.512000 | 0.0150 | DailyAvgBalance |
| 990003 | 20250203 | 300002 | 0.512000 | 0.0150 | DailyAvgBalance |
| 990004 | 20250204 | 300004 | 0.306000 | 0.0200 | DailyAvgBalance |
| 990005 | 20250205 | 300005 | 1.056000 | 0.0250 | DailyAvgBalance |
| 990006 | 20250206 | 300005 | 1.052000 | 0.0250 | DailyAvgBalance |
| 990007 | 20250206 | 300010 | 0.432000 | 0.0180 | DailyAvgBalance |
| 990008 | 20250207 | 300010 | 0.430000 | 0.0180 | DailyAvgBalance |
| 990009 | 20250202 | 300003 | 0.000000 | 0.0000 | EOD |
| 990010 | 20250203 | 300006 | 0.000000 | 0.0000 | EOD |

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

