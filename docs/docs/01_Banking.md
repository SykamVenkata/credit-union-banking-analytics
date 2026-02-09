# Retail Banking Analytics
This document showcases Retail Banking domain knowledge mapped to data domains, KPIs, analytics use cases, and BI outputs.

**Retail Banking – Business Scope**
| Area          | Description                                     |
| ------------- | ----------------------------------------------- |
| Customer Type | Individual Members & Small Businesses           |
| Products      | Checking, Savings, IRAs, Digital Banking, Zelle |
| Channels      | Branch, Online Banking, Mobile App              |
| Primary Goals | Member growth, deposit growth, digital adoption |

**Retail Banking – Product Coverage**
| Product Category | Offerings                                       |
| ---------------- | ----------------------------------------------- |
| Deposit Accounts | Rewards Checking, Savings, Membership Savings   |
| Premium Banking  | Premier Rewards Banking                         |
| Business Banking | Business Checking & Savings                     |
| Digital Services | Online Banking, Mobile App, Zelle®, Debit Cards |
| Retirement       | IRAs                                            |

**Core Retail Banking Data Domains**
| Data Domain          | Description                             |
| -------------------- | --------------------------------------- |
| Member Master        | Member demographics, join date, segment |
| Account Master       | Checking & savings account details      |
| Transaction Data     | Deposits, withdrawals, transfers        |
| Digital Banking Logs | Login activity, device, channel         |
| Payment Services     | Zelle transactions                      |
| Business Accounts    | SMB account activity                    |

**Retail Banking – Key KPIs (Executive + Operational)**
| KPI Category | KPI Name            | Business Meaning          |
| ------------ | ------------------- | ------------------------- |
| Growth       | Total Members       | Active retail members     |
| Growth       | Net New Members     | New joins minus closures  |
| Deposits     | Avg Daily Balance   | Liquidity indicator       |
| Deposits     | Deposit Growth %    | Period-over-period growth |
| Digital      | Digital Adoption %  | Online/mobile users       |
| Payments     | Zelle Txn Volume    | Peer-to-peer usage        |
| Engagement   | Products per Member | Cross-sell indicator      |

**Retail Banking – Analytics Use Cases**
| Use Case                  | Description                        |
| ------------------------- | ---------------------------------- |
| Member Growth Analysis    | Track joins, exits, and net growth |
| Balance Trend Analysis    | Monitor deposit inflows/outflows   |
| Digital Adoption Analysis | Mobile vs web usage trends         |
| Zelle Monitoring          | Usage, volume, anomaly detection   |
| Segment Performance       | Retail vs Premier vs Business      |

**Retail Banking – Power BI Dashboards**
| Dashboard                    | Key Insights                |
| ---------------------------- | --------------------------- |
| Retail Banking KPI Dashboard | Members, deposits, adoption |
| Member Engagement Dashboard  | Digital usage & activity    |
| Deposit Trends Dashboard     | Balance movement over time  |
| Digital Banking Dashboard    | Channel performance         |

**Sample Retail Banking Tables**
**members**
| Column Name      | Description              |
| ---------------- | ------------------------ |
| member_id        | Unique member identifier |
| member_since     | Membership start date    |
| member_type      | Personal / Business      |
| segment          | Standard / Premier       |
| state            | Member location          |
| digital_enrolled | Y/N                      |

**accounts**
| Column Name  | Description        |
| ------------ | ------------------ |
| account_id   | Account identifier |
| member_id    | Linked member      |
| account_type | Checking / Savings |
| balance      | Current balance    |
| open_date    | Account open date  |
| status       | Active / Closed    |

**transactions**
| Column Name | Description             |
| ----------- | ----------------------- |
| txn_id      | Transaction ID          |
| account_id  | Related account         |
| txn_date    | Transaction date        |
| txn_type    | Deposit / Withdrawal    |
| amount      | Transaction amount      |
| channel     | Branch / Mobile / Zelle |

**Retail Banking – Data Flow (Conceptual)**
| Step      | Description                     |
| --------- | ------------------------------- |
| Source    | Core Banking, Digital Platforms |
| Staging   | Data ingestion & validation     |
| EDW       | Conformed retail banking facts  |
| Data Mart | Retail Banking Mart             |
| BI Layer  | Power BI semantic model         |
| Reporting | Dashboards & KPI cards          |

