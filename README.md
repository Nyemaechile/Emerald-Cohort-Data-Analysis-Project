# Emerald-Cohort-Data-Analysis-Project
### SQL Data Analysis Capstone Project Report
### Dataset Overview and Structure
This analyzes data healthcare data from three related tables:
1. **Patient Demographics**:  Contains patient information including insurance provider, gender, age, payment status, and transaction details.
   - Columns: insurance_provider, gender, age, age_group, payment_status, transaction_count, average_bill_amount, payment_success_rate, failure_flag
   - 150+ records
2. **Appointment No-Shows**: Tracks doctor appointments and no-show statistics.
   - Columns: doctor_id, total_appointments, no_show_count, no_show_percentage, potential_revenue_lost
   - 10 doctor records

3. **Revenue Analysis**: Provides monthly revenue data by payment status.
   - Columns: month, payment_status, total_amount, transaction_count
   - 35 monthly records (Jan-Dec 2023)
The tables can be linked through payment status and transaction patterns.
## Research Questions and Methodology
### Research Questions:
1. How do payment success rates vary by insurance provider and demographic factors?
2. What is the financial impact of appointment no-shows on the healthcare provider?
3. How does revenue fluctuate monthly, and what payment status trends are observable?
### Methodology:
1. Data cleaning and preparation using SQL
2. Exploratory analysis with aggregate functions and joins
3. Trend analysis using window functions and date operations
4. Visualization of key metrics in Excel
## Key Findings and Insights

### 1. Payment Success Analysis:
- WellnessCorp has the lowest payment success rate (29.41%) among insurance providers
- Patients aged 51+ have the highest failure rate (40%) compared to other age groups
- Male patients have slightly higher failure rates (34.8%) than female patients (31.8%)

### 2. No-Show Impact:
- All doctors have 100% no-show rates in the dataset, indicating a systemic issue
- Potential revenue lost totals $143,726.30 across all doctors
- Doctor D005 accounts for the highest potential loss ($28,377.88)
### 3. Revenue Trends:
- Failed payments account for 35% of total revenue ($193,212.94)
- April 2023 had the highest total revenue ($64,271.54)
- December 2023 shows a significant drop in pending transactions
## Recommendations

1. **Payment System Improvements**:
   - Implement targeted payment reminders for WellnessCorp patients
   - Create payment plans for older patients (51+ age group)
   - Offer incentives for early payments

2. **No-Show Reduction**:
   - Implement a no-show fee policy
   - Send appointment reminders 24-48 hours in advance
   - Allow easy rescheduling options

3. **Revenue Optimization**:
   - Investigate reasons for high failed payments in April 2023
   - Analyze December drop to prepare for future year-ends
   - Focus on converting pending transactions to paid status
## Challenges and Solutions

### Challenges Faced:
1. **Data Quality Issues**:
   - Inconsistent formatting in CSV files
   - Some empty cells and summary rows mixed with data

2. **Analysis Complexity**:
   - Calculating accurate success rates required careful aggregation
   - Handling date-based analysis with incomplete months
### Solutions Applied:
1. **Data Cleaning**:
    ```sql
   -- Example cleaning query for patient data
   DELETE FROM patient_demographics 
   WHERE insurance_provider IS NULL OR payment_status = '';
## Complex calculations in SQL:
-- Example calculation for payment success rates by provider
```SELECT
insurance_provider,
SUM(CASE WHEN payment_status = 'Paid' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS success_rate
FROM patient_demographics
GROUP BY insurance_provider;
### SQL Queries Appendix
```sql
-- Query 1: Payment success by age group
SELECT age_group, 
       AVG(payment_success_rate) AS avg_success_rate, 
       AVG(failure_flag) AS failure_rate
FROM patient_demographics
GROUP BY age_group
ORDER BY failure_rate DESC;

-- Query 2: Monthly revenue trends
SELECT month, 
       payment_status, 
       SUM(total_amount) AS monthly_amount,
       SUM(SUM(total_amount)) OVER (PARTITION BY month) AS monthly_total
FROM revenue_analysis
GROUP BY month, payment_status
ORDER BY month, payment_status;

-- Query 3: No-show financial impact
SELECT doctor_id, 
       no_show_count, 
       potential_revenue_lost,
       potential_revenue_lost * 100.0 / SUM(potential_revenue_lost) OVER () AS percent_of_total_loss
FROM appointment_no_shows
ORDER BY potential_revenue_lost DESC;

