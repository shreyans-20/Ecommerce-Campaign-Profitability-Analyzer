# Ecommerce-Campaign-Profitability-Analyzer<div align="center">

# 📊 E-commerce & Campaign Profitability Analyzer

### Closing the gap between what Marketing reports and what Finance actually earns.

[![SQL](https://img.shields.io/badge/SQL-CTEs%20%7C%20Window%20Functions-4479A1?style=flat-square&logo=mysql&logoColor=white)](#)
[![MySQL](https://img.shields.io/badge/Database-MySQL-00758F?style=flat-square&logo=mysql&logoColor=white)](#)
[![Power BI](https://img.shields.io/badge/Reporting-Power%20BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)](#)
[![Status](https://img.shields.io/badge/DB%20%26%20SQL-Complete-brightgreen?style=flat-square)](#)
[![Status](https://img.shields.io/badge/Dashboard-In%20Progress-yellow?style=flat-square)](#)

**Shreyans Jain** · [LinkedIn](https://www.linkedin.com/in/shreyansjainn) &nbsp;|&nbsp; **Aadhya Patel** · [LinkedIn](https://www.linkedin.com/in/aadhyapatel)

</div>

---

## 🧩 The Problem

Marketing looks at a campaign and sees checkout revenue against ad spend. Finance looks at the *same* campaign and sees what's left after discounts, product cost, refunds, and payment reconciliation.

Same campaign, two different numbers — and both teams end up making decisions on inconsistent data.

This project builds **one reconciled source of truth** that both teams can actually agree on, backed by a relational database and SQL logic that's been tested end-to-end.

```
Marketing → Customers → Orders → Products → Payments → Refunds → Financial Analysis
```

---

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Database | MySQL |
| Analysis | SQL — CTEs, `DENSE_RANK()`, `LAG()` |
| Visualization | Power BI |
| Documentation | BRD · FRD & SRS · NFR · UAT · Problem Statement |

---

## 🗂️ Data Model

**7 relational tables**, built to reflect a realistic mid-size e-commerce operation:

| Entity | Volume |
|---|---|
| Customers | 500 |
| Ad Campaigns | 40 |
| Products | 80 |
| Orders | ~1,445 |
| Order Line Items | ~3,555 |
| Payments | ~1,259 |
| Refunds | 104 |

```
customers ──┐
            ├──< orders >──┬──< order_items >──── products
ad_campaigns┘              ├──< payments
                            └──< refunds
```

---

## 📐 Key Business Rules

- **Cancelled orders are excluded** from all revenue, COGS, and customer-spend calculations — no exceptions.
- **Net Revenue** = Gross Product Revenue − Discounts − Refunds (calculated in aggregate; refund timing relative to the original order month is a documented simplification, not an oversight).
- **VIP threshold:** ≥ $1,500 lifetime qualifying spend.
- **Dormant customer:** no qualifying (Completed) order in 60+ days.
- **ROAS with zero ad spend returns `NULL`** — never a fabricated 0, never a divide-by-zero error.

---

## ✅ Analysis Delivered

Mapped directly to **FR-1.1 – FR-1.10** in the FRD & SRS:

1. **Core financials** — per-order Net Revenue, COGS, and discount/refund breakdown
2. **Campaign performance** — Contribution Profit and ROAS by platform, ranked with `DENSE_RANK()`
3. **Customer segmentation** — VIP/Regular tier × Active/Dormant/Never Ordered
4. **Reconciliation** — payment and refund exceptions report
5. **Revenue trend** — monthly revenue and Month-over-Month growth using `LAG()`

### Not yet built (scoped out, not dropped)

These were logged as candidate extensions in the Problem Statement and BRD — deliberately excluded to keep the current build fully validated end-to-end rather than partially covering a longer list:

- Refund rate by product/category
- Payment-method-level reconciliation
- Explicit high-spend/low-contribution campaign flag
- New/Returning/Dormant customer segmentation

---

## 💡 Key Insights

> **~20% of orders required reconciliation attention** (259 of ~1,259 payments) — a mix of failed payments, amount mismatches, and refund activity. A realistic exception rate, which confirms the reconciliation logic catches genuine discrepancies rather than returning an empty report.

> **At the $1,500 threshold, 88% of the customer base qualifies as VIP.** This is a flag, not a win — it signals the threshold likely needs recalibration for a dataset at this price scale and order volume in a production setting. Retained as-is to match the documented business rule, and called out explicitly here because the mismatch between rule and outcome is itself a finding.

> **Zero-spend campaigns correctly return `NULL` ROAS** — a deliberate edge case built into the mock data to confirm the analysis layer handles messy inputs rather than assuming clean ones.

> **Recency-weighted order generation was required** for a believable Active/Dormant split. An early version of the dataset showed 61% of customers as dormant — a data-generation artifact, not a business finding — and was corrected before drawing any conclusions from it.

---

## ⚠️ Known Limitations

Documented honestly, not buried:

| Limitation | Status |
|---|---|
| VIP threshold ($1,500) yields a majority-VIP customer base at current volume/pricing | Noted, not resolved — documented scope decision |
| Refund-to-revenue matching is aggregate-level, not month-matched | Documented simplification |
| BR-6 (Product-Level Economics) has no dedicated SQL — only partial coverage via FR-1.3/1.4 | **Not yet implemented** |
| NFR-2.2 performance benchmark | **Pending** — to be run against the current ~1,445-order dataset and logged in the NFR doc |

---

## 📁 Repository Structure

```
/docs
  01_Problem_Statement_and_Stakeholder_Analysis.docx
  02_BRD.docx
  03_FRD_and_SRS.docx
  04_NFR.docx
  05_UAT.docx
/sql
  schema.sql                  # Table definitions (DDL)
  mock_data/                  # Data generation scripts
  01_core_financials.sql
  02_contribution_campaigns.sql
  03_customer_segmentation.sql
  04_reconciliation.sql
  05_mom_growth.sql
/dashboard
  campaign_analyzer.pbix
README.md
```

---

## 🧪 Validation

- All **10 functional requirements** (FR-1.1 – FR-1.10) are covered by the SQL in `/sql`.
- All **7 UAT test cases** (UAT-001 – UAT-007) executed and marked **Passed** against the built dataset — see `/docs/05_UAT.docx`.

| Test ID | Scenario | Result |
|---|---|---|
| UAT-001 | Product COGS | ✅ Passed |
| UAT-002 | Cancelled Order Exclusion | ✅ Passed |
| UAT-003 | VIP Customer Classification | ✅ Passed |
| UAT-004 | VIP Dormancy Flag | ✅ Passed |
| UAT-005 | Payment Reconciliation | ✅ Passed |
| UAT-006 | Refund Treatment | ✅ Passed |
| UAT-007 | MoM Revenue Growth | ✅ Passed |

---

## 📚 Documentation

| Document | Contents |
|---|---|
| [Problem Statement & Stakeholder Analysis](docs/01_Problem_Statement_and_Stakeholder_Analysis.docx) | Business problem, objectives, stakeholder map, key business questions |
| [BRD](docs/02_BRD.docx) | Business requirements BR-1 – BR-6, success criteria |
| [FRD & SRS](docs/03_FRD_and_SRS.docx) | Functional requirements FR-1.1 – FR-1.10, data model, SQL/system requirements |
| [NFR](docs/04_NFR.docx) | Performance, integrity, auditability, and maintainability requirements |
| [UAT](docs/05_UAT.docx) | Test cases, results, and final acceptance status |

---

<div align="center">

**Built as an end-to-end Business Analyst lifecycle exercise:**
Business Problem → Requirements → Data Model → Business Rules → SQL → Testing → Reporting → Business Insights

Shreyans Jain · [LinkedIn](https://www.linkedin.com/in/shreyansjainn) &nbsp;&nbsp;|&nbsp;&nbsp; Aadhya Patel · [LinkedIn](https://www.linkedin.com/in/aadhyapatel)

</div>
