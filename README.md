# Customer 360 Accelerator

## Overview

The **Customer 360 Accelerator** is a turnkey Snowflake Native App that transforms your identity resolution and customer data into actionable intelligence — entirely within your Snowflake account. Connect your existing identity graph, transaction, and event tables to a polished, interactive Streamlit dashboard spanning five analytics modules. No data leaves your environment, and no external tools or credentials are required.

## Key Features

- **Identity Resolution Health** — Monitor match rates, confidence distributions, deterministic match sources (email/phone/loyalty), cluster sizes, and duplicate reduction metrics
- **Customer 360 Profiles** — Unified customer view with CLV, churn probability, retention scores, RFM segmentation, and spend/engagement scatter analysis
- **Journey & Engagement** — Conversion funnel analysis, cross-channel journey tracking, engagement segmentation, and channel revenue trends
- **Activation & Revenue** — Campaign readiness scoring, revenue uplift estimation, personalization potential, and segment-level deep dives
- **Data Quality** — Completeness monitoring, freshness tracking, and column-level quality metrics across source tables

## Required References

After installation, bind the following 13 views/tables via the app configuration page:

### Base Tables

#### `CUSTOMER_360` — Unified Customer Profiles
| Column | Type | Description |
|--------|------|-------------|
| MASTER_CUSTOMER_ID | VARCHAR | Primary key — unified customer identifier |
| CUSTOMER_SEGMENT | VARCHAR | RFM segment (e.g., Champions, Loyal Customers, At Risk) |
| TOTAL_SPEND | NUMBER(18,2) | Lifetime total spend |
| TOTAL_ORDERS | INTEGER | Lifetime order count |
| AVG_ORDER_VALUE | NUMBER(10,2) | Average order value |
| CUSTOMER_LIFETIME_VALUE | NUMBER(18,2) | Predicted CLV |
| CHURN_PROBABILITY | NUMBER(6,4) | Churn probability (0.0–1.0) |
| ENGAGEMENT_SCORE | NUMBER(5,2) | Engagement score (0–100) |
| RETENTION_SCORE | NUMBER(5,2) | Retention score (0–100) |
| PREFERRED_CHANNEL | VARCHAR | Preferred channel (online/mobile/in-store/social) |
| PREFERRED_CATEGORY | VARCHAR | Preferred product category |
| IDENTITY_COUNT | INTEGER | Number of linked identities |
| LAST_PURCHASE_DATE | DATE | Date of most recent purchase |
| UPDATED_AT | TIMESTAMP | Last profile update timestamp |

#### `IDENTITY_GRAPH` — Identity Resolution Graph Edges
| Column | Type | Description |
|--------|------|-------------|
| MASTER_CUSTOMER_ID | VARCHAR | Unified customer identifier |
| LINKED_ID | VARCHAR | Linked identity ID |
| MATCH_TYPE | VARCHAR | Match type (email/phone/loyalty_id/device_id/self) |
| CONFIDENCE_SCORE | NUMBER(6,4) | Match confidence (0.0–1.0) |

#### `DETERMINISTIC_MATCHES` — Deterministic Match Pairs
| Column | Type | Description |
|--------|------|-------------|
| MATCH_TYPE | VARCHAR | Match type (email/phone/loyalty_id) |
| SOURCE_A | VARCHAR | Source system A (e.g., CRM, ECOMMERCE, POS) |
| ID_A | VARCHAR | Source customer ID from system A |
| SOURCE_B | VARCHAR | Source system B |
| ID_B | VARCHAR | Source customer ID from system B |
| CONFIDENCE | NUMBER(6,4) | Match confidence score |

#### `IDENTITY_CLUSTERS` — Cluster Membership
| Column | Type | Description |
|--------|------|-------------|
| SOURCE_SYSTEM | VARCHAR | Source system name |
| SOURCE_CUSTOMER_ID | VARCHAR | Source customer ID |
| MASTER_CUSTOMER_ID | VARCHAR | Unified cluster identifier |

#### `CUSTOMER_METRICS` — RFM & Retention Metrics
| Column | Type | Description |
|--------|------|-------------|
| MASTER_CUSTOMER_ID | VARCHAR | Unified customer identifier |
| CUSTOMER_SEGMENT | VARCHAR | RFM segment label |
| RECENCY | INTEGER | Days since last purchase |
| FREQUENCY | INTEGER | Purchase frequency |
| MONETARY | NUMBER(10,2) | Monetary value |
| CHURN_PROBABILITY | NUMBER(6,4) | Churn probability (0.0–1.0) |
| RETENTION_SCORE | NUMBER(5,2) | Retention score (0–100) |
| CUSTOMER_LIFETIME_VALUE | NUMBER(18,2) | Predicted CLV |

#### `FACT_TRANSACTIONS` — Transaction Facts
| Column | Type | Description |
|--------|------|-------------|
| TRANSACTION_ID | VARCHAR | Unique transaction ID |
| MASTER_CUSTOMER_ID | VARCHAR | Unified customer identifier |
| SOURCE_CUSTOMER_ID | VARCHAR | Original source customer ID |
| AMOUNT | NUMBER(10,2) | Transaction amount |
| CHANNEL | VARCHAR | Transaction channel (online/mobile/in-store/social) |
| CATEGORY | VARCHAR | Product category |
| TRANSACTION_DATE | DATE | Transaction date |

#### `FACT_EVENTS` — Event Facts
| Column | Type | Description |
|--------|------|-------------|
| EVENT_ID | VARCHAR | Unique event ID |
| MASTER_CUSTOMER_ID | VARCHAR | Unified customer identifier |
| EVENT_TYPE | VARCHAR | Event type (view/click/cart/purchase) |
| CATEGORY | VARCHAR | Product category |
| EVENT_DATE | DATE | Event date |

#### `RAW_CUSTOMERS` — Source Customer Profiles
| Column | Type | Description |
|--------|------|-------------|
| SOURCE_CUSTOMER_ID | VARCHAR | Source system customer ID |
| SOURCE_SYSTEM | VARCHAR | Source system name (CRM/ECOMMERCE/POS) |
| EMAIL | VARCHAR | Email address (nullable) |
| PHONE | VARCHAR | Phone number (nullable) |
| LOYALTY_ID | VARCHAR | Loyalty program ID (nullable) |

#### `MART_DATA_QUALITY` — Data Quality Metrics
| Column | Type | Description |
|--------|------|-------------|
| TABLE_NAME | VARCHAR | Source table name |
| COLUMN_NAME | VARCHAR | Column name |
| COMPLETENESS_PCT | NUMBER(5,1) | Completeness percentage (0–100) |

### Derived Views

#### `V_CUSTOMER_360` — Semantic View Over Unified Profiles
Same schema as `CUSTOMER_360` above.

#### `V_CHANNEL_PERFORMANCE` — Channel Revenue by Month
| Column | Type | Description |
|--------|------|-------------|
| CHANNEL | VARCHAR | Transaction channel |
| TRANSACTION_MONTH | DATE | Month (first day) |
| TOTAL_REVENUE | NUMBER(18,2) | Total revenue for channel/month |
| UNIQUE_CUSTOMERS | INTEGER | Distinct customer count |
| TRANSACTION_COUNT | INTEGER | Transaction count |

#### `V_KPI_DATA_QUALITY` — Summary Data Quality KPIs
| Column | Type | Description |
|--------|------|-------------|
| AVG_COMPLETENESS | NUMBER(5,1) | Average completeness across all columns |
| COMPLETENESS_FLAG | VARCHAR | 'OK' if avg >= 90%, else 'BELOW' |
| HOURS_SINCE_LAST_CUSTOMER_UPDATE | INTEGER | Hours since last CUSTOMER_360 update |
| HOURS_SINCE_LAST_EVENT | INTEGER | Hours since last event |

#### `V_DATA_QUALITY` — Column-Level Completeness Detail
| Column | Type | Description |
|--------|------|-------------|
| TABLE_NAME | VARCHAR | Source table name |
| COLUMN_NAME | VARCHAR | Column name |
| COMPLETENESS_PCT | NUMBER(5,1) | Completeness percentage (0–100) |

## Installation Steps

1. **Install the app** from the Marketplace listing
2. **Grant privileges** — Grant `SNOWFLAKE.CORTEX_USER` database role for future AI enhancements (optional)
3. **Bind your data** — Navigate to the configuration page and map each of the 13 required references
4. **Launch the dashboard** — Open the Streamlit app from the installed application

## Architecture

```
Application Package
├── manifest.yml          — App metadata, version, 13 references
├── scripts/
│   └── setup.sql         — Roles, schemas, reference-backed views, Streamlit
├── streamlit/
│   ├── streamlit_app.py  — Main dashboard (5 tabs)
│   └── environment.yml   — Python dependencies
└── readme.md             — This file
```

## Support

For issues or feature requests, contact the provider through the Marketplace listing page.
