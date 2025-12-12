# Project Quotation: Timezone Alignment & Data Quality Audit

## Project Overview

Fix critical timezone mismatch between Meta (Facebook) and Google Analytics data causing incorrect daily reporting analysis. Meta data currently in Los Angeles time needs alignment to Amsterdam time (matching Google Analytics), including proper handling of daylight saving time transitions. Additionally, comprehensive review of the full data pipeline (Connectors → BigQuery → Looker Studio) to identify data quality issues and recommend improvements.

---

## Problem Statement

### Current Issues:
1. **Timezone Mismatch:** Meta data in Los Angeles (PST/PDT) vs Google Analytics in Amsterdam (CET/CEST)
2. **Incorrect Daily Totals:** Day boundaries misaligned between data sources
3. **Wrong Day-of-Week Analysis:** Events attributed to wrong days when comparing Meta vs GA data
4. **Daylight Saving Complexity:** Both timezones have different DST transition dates (US vs EU)
5. **Unknown Data Quality Issues:** Potential hidden problems in the full reporting pipeline
---

## Scope of Work

### Phase 1: Discovery & Assessment
**Activities:**
- Initial consultation to understand current reporting setup and pain points
- Documentation review (existing data models, ETL pipelines, reports, dashboards)
- Data lineage mapping to trace data flow from source to reports
- Identify all data sources, transformations, and dependencies
- Audit Meta connector configuration and timezone settings
- Review Google Analytics connector and timezone settings
- Analyze sample data to quantify the timezone mismatch impact
- Document current BigQuery table schemas and timestamp fields
- Identify all reports/dashboards affected by timezone issues

**Deliverables:**
- Current state architecture diagram
- Timezone discrepancy analysis report with sample comparisons
- List of affected tables, fields, and reports
- Impact assessment (which metrics are wrong and by how much)
- Initial findings document with high-level observations

---

### Phase 2: Full Pipeline Data Quality Review

**2.1 Solution Design**
- Design timezone conversion logic for Meta data (Los Angeles → Amsterdam)
- Handle daylight saving time transitions correctly:
  - US DST: Second Sunday in March to First Sunday in November
  - EU DST: Last Sunday in March to Last Sunday in October
  - Address the 2-3 week periods where DST doesn't align
- Create conversion logic that maintains data integrity
- Design solution for historical data backfill

**Deliverables:**
- Technical design document with timezone conversion approach
- SQL functions for timezone conversion with DST handling
- Data transformation architecture diagram

**2.2 BigQuery Implementation**

**Implementation Approach:**
- Create production-ready BigQuery tables/views with Meta data converted to Amsterdam timezone
- Implement one of these approaches (based on your preference):
  - **Option A (Recommended):** Create new timezone-corrected tables that auto-update daily via scheduled queries
  - **Option B:** Add timezone conversion views on top of existing tables (no data duplication)
  - **Option C:** Modify your existing ETL pipeline to convert at ingestion time

**Daily Aggregation Logic:**
- Convert Meta event timestamps from Los Angeles to Amsterdam time
- Re-aggregate all metrics to Amsterdam day boundaries
- Handle metrics appropriately:
  - **Summable metrics** (spend, impressions, clicks, conversions): Sum by Amsterdam date
  - **Calculated metrics** (CPM, CTR, CPC): Recalculate correctly after re-aggregation
  - **Unique user metrics** (reach, frequency): Handle deduplication across timezone boundaries

**Backfill & Go-Forward:**
- Backfill ALL historical Meta data with correct Amsterdam timezone
- Set up automated daily refresh to maintain corrected data going forward
- Implement data quality checks to ensure ongoing accuracy

**Deliverables:**
- ✅ **Fully implemented and tested timezone conversion solution in BigQuery**
- ✅ **Complete historical data backfill with Amsterdam timezone**
- ✅ **Automated scheduled queries for ongoing daily updates**
- ✅ **SQL scripts and documentation for maintenance**

---

### Phase 3: Data Quality Analysis

**3.1 Consistency Checks**
- Cross-validate numbers across different reports
- Compare aggregated data with source data
- Check for discrepancies between similar metrics in different dashboards
- Validate time series data for continuity and completeness

**3.2 Common Issue Detection**
- **Double Counting:** Analyze join logic, many-to-many relationships, duplicate records
- **Missing Data:** Check for gaps in time series, null values, incomplete dimensions
- **Wrong Filters:** Review filter logic, date range calculations, partition pruning
- **Wrong Definitions:** Validate business metric definitions against actual calculations
- **Incorrect Joins:** Audit join keys, join types (inner/left/right), fanout issues
- **Data Type Issues:** Check for implicit conversions, precision loss
- **Granularity Mismatches:** Verify aggregation levels align with business requirements

**3.3 Statistical Analysis**
- Distribution analysis to detect anomalies
- Volume trend analysis (row counts, null rates, distinct value counts)
- Compare expected vs actual data volumes
- Identify outliers and suspicious patterns

**Deliverables:**
- Detailed data quality report with findings categorized by severity (Critical/High/Medium/Low)
- SQL queries/scripts used for validation (reusable for ongoing monitoring)

---

### Phase 4: Technical Deep Dive

**4.1 Connector Configuration Audit**

**Meta Connector Review:**
- Verify API configuration and data extraction settings
- Check field mapping and data type conversions
- Review incremental load logic and primary keys
- Validate that all required metrics are being captured
- Check for API rate limiting or sampling issues

**Google Analytics Connector Review:**
- Verify GA4 or Universal Analytics configuration
- Check dimension and metric selections
- Review session and user ID mapping
- Validate event tracking completeness

**General Connector Issues:**
- Authentication and access token refresh
- Error handling and retry logic
- Data latency and freshness
- Missing data detection mechanisms

**4.2 BigQuery Data Layer Review**
- Review table structures and naming conventions
- Validate primary keys and uniqueness constraints
- Check for proper partitioning and clustering
- Analyze data types and field definitions

**4.3 ETL Logic Review**
- Review ETL/ELT pipeline code (SQL, Python, Airflow DAGs, dbt models, etc.)
- Analyze transformation logic for correctness
- Check error handling and data validation mechanisms
- Review incremental load logic and change data capture

**4.4 Looker Studio Dashboard Review**
- Compare dashboard metrics with BigQuery source data
- Verify filters are working correctly
- Check date range selectors and default values
- Validate calculated fields in Looker Studio
- Review blended data sources for correct join logic

**Deliverables:**
- Connector configuration audit report
- Code review report with specific issues and recommendations
- Data model optimization suggestions
- Performance improvement recommendations
- Dashboard audit report with issues and fixes

---

### Phase 5: Recommendations & Remediation Plan

**Activities:**
- Prioritize issues based on business impact and effort
- Create detailed remediation plan with step-by-step fixes
- Recommend data quality monitoring framework
- Design automated data quality checks for ongoing validation
- Suggest best practices for data governance
- BigQuery cost optimization opportunities
- Query performance improvements

**Deliverables:**
- Executive summary report (non-technical for leadership)
- Technical remediation roadmap with effort estimates
- Data quality monitoring framework proposal
- Best practices documentation

---

### Phase 6: Knowledge Transfer & Support

**Activities:**
- Presentation of findings to stakeholders
- Q&A session to clarify recommendations
- Knowledge transfer on how to maintain data quality
- Handover of all documentation and scripts

**Deliverables:**
- Presentation slides
- Video recording of walkthrough session
- 2 weeks of post-project support for questions

---
## Project Timeline

### Total Estimated Hours: **13 hours**

- Phase 1: Discovery & Assessment (2 hours)
- Phase 2: Full Pipeline Data Quality Review (4 hours)
- Phase 3: Data Quality Analysis (3 hours)
- Phase 4: Technical Deep Dive (2 hours)
- Phase 5: Recommendations & Remediation Plan (1 hours)
- Phase 6: Knowledge Transfer & Support (1 hour)
  
**Completion Date: 2 days from start**

*Timeline assumes timely access to all systems and stakeholder availability.*


## FAQ

**Q: How do you handle Meta metrics like reach and frequency when converting daily data?**  

A: I handle different metric types appropriately:
- **Summable metrics** (spend, impressions, clicks): Sum directly by Amsterdam date
- **Calculated metrics** (CPM, CTR, CPC): Recalculate from re-aggregated base metrics
- **Unique user metrics** (reach, frequency): 
  - First, I'll check if hourly data is available for perfect deduplication
  - If not, I'll use daily values with ~2-5% approximation (documented clearly)
  - For weekly/monthly reporting, the approximation becomes negligible
  - I'll provide validation showing the impact of any approximation

**Q: Can we test the timezone fix before applying to production?**

A: Absolutely. I'll create test tables/views so you can validate the changes before updating production dashboards.
