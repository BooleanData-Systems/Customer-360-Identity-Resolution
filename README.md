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

| Reference | Description |
|-----------|-------------|
| `CUSTOMER_360` | Unified customer profiles (master_customer_id, CLV, engagement, churn, spend) |
| `IDENTITY_GRAPH` | Identity graph edges (master_customer_id, linked_id, match_type, confidence) |
| `DETERMINISTIC_MATCHES` | Deterministic match pairs (email, phone, loyalty_id) |
| `IDENTITY_CLUSTERS` | Cluster membership (source_system, source_customer_id, master_customer_id) |
| `CUSTOMER_METRICS` | RFM segmentation, churn, retention, monetary metrics |
| `FACT_TRANSACTIONS` | Transaction facts (amount, channel, transaction_date) |
| `FACT_EVENTS` | Event facts (view/click/cart/purchase, category, event_date) |
| `RAW_CUSTOMERS` | Source customer profiles (source_customer_id) |
| `MART_DATA_QUALITY` | Data quality metrics (completeness_pct by table/column) |
| `V_CUSTOMER_360` | Semantic view over unified profiles |
| `V_CHANNEL_PERFORMANCE` | Channel revenue and unique customers by month |
| `V_KPI_DATA_QUALITY` | Summary data quality KPIs |
| `V_DATA_QUALITY` | Column-level completeness detail |

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
