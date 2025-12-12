# Project Quotation: Timezone Alignment & Data Quality Audit

## Project Overview

Fix critical timezone mismatch between Meta (Facebook) and Google Analytics data causing incorrect daily reporting and day-of-week analysis. Meta data currently in Los Angeles time needs alignment to Amsterdam time (matching Google Analytics), including proper handling of daylight saving time transitions. Additionally, comprehensive review of the full data pipeline (Connectors → BigQuery → Looker Studio) to identify data quality issues and recommend improvements.

---

## Problem Statement

### Current Issues:
1. **Timezone Mismatch:** Meta data in Los Angeles (PST/PDT) vs Google Analytics in Amsterdam (CET/CEST)
2. **Incorrect Daily Totals:** Day boundaries misaligned between data sources
3. **Wrong Day-of-Week Analysis:** Events attributed to wrong days when comparing Meta vs GA data
4. **Daylight Saving Complexity:** Both timezones have different DST transition dates (US vs EU)
5. **Unknown Data Quality Issues:** Potential hidden problems in the full reporting pipeline

### Business Impact:
- Unreliable cross-channel performance comparison
- Incorrect day-parting analysis and insights
- Wrong attribution of campaign performance by day
- Compromised decision-making based on inaccurate daily trends

---

## Scope of Work

### Phase 1: Timezone Issue Analysis & Resolution

**1.1 Current State Assessment**
- Audit Meta connector configuration and timezone settings
- Review Google Analytics connector and timezone settings
- Analyze sample data to quantify the mismatch impact
- Document current BigQuery table schemas and timestamp fields
- Identify all reports/dashboards affected by timezone issues

**Deliverables:**
- Timezone discrepancy analysis report with sample comparisons
- List of affected tables, fields, and reports
- Impact assessment (which metrics are wrong and by how much)

**1.2 Timezone Conversion Solution Design (2 hours)**
- Design timezone conversion logic for Meta data (Los Angeles → Amsterdam)
- Handle daylight saving time transitions correctly:
  - US DST: Second Sunday in March to First Sunday in November
  - EU DST: Last Sunday in March to Last Sunday in October
  - Address the 2-3 week periods where DST doesn't align
- Create conversion logic that maintains data integrity

**Deliverables:**
- Technical design document with timezone conversion approach
- SQL functions for timezone conversion with DST handling
- Data transformation architecture diagram

**1.3 Implementation & Testing**

**GUARANTEED DELIVERABLE: Full Implementation of Timezone Fix**

I will implement the complete timezone conversion solution. This includes:

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
  - **Non-summable metrics** (reach, frequency, CPM, CTR): Recalculate correctly after re-aggregation
  - **Unique user metrics** (reach, unique users): Handle deduplication across timezone boundaries

**Backfill & Go-Forward:**
- Backfill ALL historical Meta data with correct Amsterdam timezone
- Set up automated daily refresh to maintain corrected data going forward
- Implement data quality checks to ensure ongoing accuracy

**Validation:**
- Test timezone conversion accuracy with sample data covering:
  - Regular days across all months
  - DST transition days (both US and EU)
  - Edge cases (midnight crossover, week start/end boundaries)
  - Month-end and year-end boundaries
- Compare daily totals before/after conversion
- Validate against Meta Ads Manager UI for spot-checks

**Deliverables:**
- ✅ **Fully implemented and tested timezone conversion solution in BigQuery**
- ✅ **Complete historical data backfill with Amsterdam timezone**
- ✅ **Automated scheduled queries for ongoing daily updates**
- ✅ **Validation test results showing before/after accuracy**
- ✅ **SQL scripts and documentation for maintenance**

**1.4 Reporting Layer Updates**

**Implementation:**
- Identify all Looker Studio dashboards using Meta data
- Update data source connections to use timezone-corrected tables/views
- Modify calculated fields that reference date/time fields
- Update all daily aggregation metrics to use Amsterdam dates
- Fix day-of-week calculations and filters
- Update date range filters and controls
- Add timezone indicator labels for clarity (e.g., "All dates in Amsterdam time (CET/CEST)")

**Verification:**
- Validate that daily totals now match between Meta and Google Analytics
- Test date filters work correctly across the date range
- Verify day-of-week analysis shows consistent patterns
- Confirm historical trends look reasonable after timezone correction
- Check that all dashboard interactions (filters, drill-downs) work properly

**Quality Assurance:**
- Create side-by-side comparison showing:
  - Old daily totals (LA time) vs New daily totals (Amsterdam time)
  - Sample weeks showing day-by-day differences
  - Impact of DST transitions on data
- Provide transition guide showing what changed and why
- Document any metrics that may show significant shifts

**Deliverables:**
- ✅ **ALL Looker Studio dashboards updated with timezone-corrected Meta data**
- ✅ **Verified alignment between Meta and GA daily metrics**
- ✅ **Updated date filters, day-of-week calculations, and time-based segments**
- ✅ **Before/after comparison report showing the impact of timezone correction**
- ✅ **User guide explaining changes for dashboard users**

---

### Phase 2: Full Pipeline Data Quality Review

**2.1 Connector Configuration Audit**

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
- Check for data sampling in reports

**General Connector Issues to Check:**
- Authentication and access token refresh
- Error handling and retry logic
- Data latency and freshness
- Missing data detection mechanisms
- Schema drift handling

**Deliverables:**
- Connector configuration audit report
- Issues found with severity ratings
- Recommended configuration changes

**2.2 BigQuery Data Layer Review**

**Schema & Data Model Analysis:**
- Review table structures and naming conventions
- Validate primary keys and uniqueness constraints
- Check for proper partitioning and clustering
- Analyze data types and field definitions
- Review slowly changing dimensions (if any)

**Data Quality Checks:**
- **Completeness:** Check for missing days, gaps in data
- **Consistency:** Validate numbers between raw and processed tables
- **Accuracy:** Cross-check sample data with source platforms
- **Duplication:** Detect duplicate records
- **Null Values:** Identify unexpected null patterns
- **Data Volume:** Compare expected vs actual row counts daily
- **Referential Integrity:** Validate relationships between tables

**Common Issues to Detect:**
- Double counting from incorrect joins
- Missing data from failed connector runs
- Wrong filters excluding valid data
- Incorrect aggregation logic
- Fanout issues in joins (1-to-many becoming many-to-many)
- Metric definition mismatches vs business requirements
- Currency or unit conversion errors
- Bot/spam traffic not filtered

**ETL Logic Review:**
- Examine transformation queries (SQL/dbt/Dataform)
- Review calculated fields and custom metrics
- Validate date filters and lookback windows
- Check incremental load logic for correctness
- Analyze performance of expensive queries

**Deliverables:**
- Detailed data quality report with findings by severity
- SQL validation queries for ongoing monitoring
- Data lineage diagram showing data flow
- Recommendations for data model improvements

**2.3 Looker Studio Dashboard Review**

**Report Accuracy Validation:**
- Compare dashboard metrics with BigQuery source data
- Verify filters are working correctly
- Check date range selectors and default values
- Validate calculated fields in Looker Studio
- Review blended data sources for correct join logic

**Common Dashboard Issues:**
- Incorrect metric definitions or formulas
- Wrong data source connections
- Misleading visualizations
- Performance issues with large datasets
- Missing filters causing incorrect aggregations
- Date comparison logic errors (YoY, MoM)

**Usability Review:**
- Check dashboard loading performance
- Identify confusing or ambiguous metrics
- Review drill-down and filter interactions
- Validate data freshness indicators

**Deliverables:**
- Dashboard audit report with issues and fixes
- Recommendations for dashboard improvements
- Best practices for Looker Studio optimization

**2.4 End-to-End Data Validation**
- Trace sample transactions from source to dashboard
- Validate that key metrics match across the pipeline
- Create data quality test suite covering:
  - Row count reconciliation
  - Sum/aggregation validation
  - Null rate monitoring
  - Duplicate detection
  - Freshness checks

**Deliverables:**
- End-to-end validation test results
- Automated data quality check scripts
- Data monitoring dashboard in Looker Studio

---

### Phase 3: Recommendations & Documentation

**3.1 Optimization Recommendations**
- BigQuery cost optimization opportunities
- Query performance improvements
- Partitioning and clustering strategies
- Connector scheduling optimization
- Dashboard refresh strategy

**3.2 Data Quality Framework**
- Recommended ongoing data quality checks
- Alerting strategy for data anomalies
- Data quality KPIs and SLAs
- Incident response procedures

**3.3 Documentation & Knowledge Transfer**
- Comprehensive documentation of current setup
- Runbook for common issues and fixes
- Best practices guide
- Presentation of findings

**Deliverables:**
- Executive summary report (non-technical)
- Technical recommendations document
- Data quality monitoring framework
- Complete documentation package
- Presentation slides

---

### Phase 4: Implementation Support & Handover

**Activities:**
- Walkthrough session with your team
- Q&A and clarifications
- Guide on implementing recommendations
- 2 weeks post-project support for questions

**Deliverables:**
- Recorded walkthrough session
- Follow-up support documentation

---

## Project Timeline

### Total Estimated Hours: **13 hours**

**Week 1 (6 hours)**
- Phase 1: Timezone issue resolution (5 hours)
- Phase 2: Begin connector audit (1 hours)

**Week 2 (3 hours)**
- Phase 2: BigQuery and dashboard review (3 hours)

**Week 3 (4 hours)**
- Phase 2: Final validation (2 hours)
- Phase 3: Recommendations & documentation (1 hours)
- Phase 4: Knowledge transfer (1 hours)

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
