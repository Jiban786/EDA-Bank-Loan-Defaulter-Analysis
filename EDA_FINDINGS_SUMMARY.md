# Bank Loan Defaulter Risk Analysis
## Exploratory Data Analysis (EDA) - Complete Findings & Outliers Report

---

## EXECUTIVE SUMMARY

This comprehensive EDA analyzed loan application records to identify patterns, data quality issues, and features that help assess borrower default risk. The analysis examined **307,511 current loan applications** and **1,670,214 historical previous applications** across **122 features** in the application dataset.

**Key Finding:** Default rate is **8.07%** (24,825 defaults out of 307,511 applications). The analysis identified several critical risk factors including financial stress indicators, employment instability, demographic patterns, and behavioral signals from application history.

---

## 1. DATASET OVERVIEW

### Data Volume
- **Application Records:** 307,511 borrowers
- **Previous Application Records:** 1,670,214 historical applications
- **Features (Application Dataset):** 122 columns
- **Features (Previous Application Dataset):** 37 columns

### Target Variable Distribution
| Class | Count | Percentage |
|-------|-------|-----------|
| Non-Default (0) | 282,686 | 91.93% |
| Default (1) | 24,825 | **8.07%** |

**Insight:** Imbalanced dataset with ~9:1 ratio of non-defaulters to defaulters. This requires careful model training with appropriate class weighting or resampling strategies.

---

## 2. DATA QUALITY & MISSING VALUES

### Application Data - Top 15 Missing Columns (% Missing)

1. COMMONAREA_AVG: **69.87%**
2. COMMONAREA_MODE: **69.87%**
3. COMMONAREA_MEDI: **69.87%**
4. NONLIVINGAPARTMENTS_MEDI: **69.43%**
5. NONLIVINGAPARTMENTS_MODE: **69.43%**
6. NONLIVINGAPARTMENTS_AVG: **69.43%**
7. FONDKAPREMONT_MODE: **68.39%**
8. LIVINGAPARTMENTS_AVG: **68.35%**
9. LIVINGAPARTMENTS_MEDI: **68.35%**
10. LIVINGAPARTMENTS_MODE: **68.35%**

**Action Taken:** All columns with >40% missing values were dropped in the cleaning phase. This removed approximately 15-20 high-missing columns from the working dataset.

### Previous Application Data - Top 15 Missing Columns (% Missing)

1. RATE_INTEREST_PRIVILEGED: **99.64%** ❌ (Dropped)
2. RATE_INTEREST_PRIMARY: **99.64%** ❌ (Dropped)
3. AMT_DOWN_PAYMENT: **53.64%** ❌ (Dropped)
4. RATE_DOWN_PAYMENT: **53.64%** ❌ (Dropped)
5. NAME_TYPE_SUITE: **49.12%** ❌ (Dropped)
6. DAYS_TERMINATION: **40.30%** ❌ (Dropped)
7. DAYS_FIRST_DRAWING: **40.30%** ❌ (Dropped)
8. DAYS_FIRST_DUE: **40.30%** ❌ (Dropped)
9. DAYS_LAST_DUE_1ST_VERSION: **40.30%** ❌ (Dropped)
10. DAYS_LAST_DUE: **40.30%** ❌ (Dropped)

---

## 3. OUTLIER DETECTION & ANALYSIS

### 3.1 Key Numeric Features - Outlier Summary

#### AMT_INCOME_TOTAL (Total Income)
- **Range:** $25,650 to $117,000,000
- **Mean:** $168,797.92
- **Median:** $147,150
- **Std Dev:** $237,123
- **Outliers:** 14,035 records (4.56%) beyond 1.5×IQR
  - **Extreme High Earners:** ~4.5% of applicants with exceptionally high income
  - **Risk:** High-income outliers may skew default predictions; consider capping or flagging

#### AMT_CREDIT (Credit Amount Requested)
- **Range:** $45,000 to $4,050,000
- **Mean:** $599,026
- **Median:** $513,531
- **Std Dev:** $402,490
- **Outliers:** 6,562 records (2.13%) beyond 1.5×IQR
  - **Very High Requests:** 2.13% of applicants requested loans >$1.5M
  - **Risk:** May indicate desperation or unrealistic repayment expectations

#### AMT_ANNUITY (Annual Payment Amount)
- **Range:** $1,615.50 to $258,025.50
- **Mean:** $27,108.57
- **Median:** $24,903
- **Std Dev:** $14,493.74
- **Outliers:** 7,504 records (2.44%) beyond 1.5×IQR
  - **Very High Annuities:** Borrowers with extreme payment obligations
  - **Risk:** May indicate overleveraged applicants

#### DAYS_EMPLOYED (Employment History)
- **Range:** -17,912 to +365,243 days
- **Mean:** 63,815 days (~175 years - DATA QUALITY ISSUE)
- **Median:** -1,213 days
- **Std Dev:** 141,275
- **Outliers:** 72,217 records (23.48%) beyond 1.5×IQR ⚠️
  - **CRITICAL ISSUE:** DAYS_EMPLOYED has extreme anomalies
  - Negative values indicate current employed status
  - Values >365,243 days are impossible (>1000 years employment)
  - **Only 55,376 valid records** out of 307,511 have valid employment history
  - **Data Quality:** Employment tenure data is severely corrupted (only 18% valid)

#### DAYS_BIRTH (Age Indicator)
- **Range:** -25,229 to -7,489 days
- **Mean:** -16,037 days (≈ 43.9 years)
- **Median:** -15,750 days (≈ 43.2 years)
- **Std Dev:** 4,363.99 days
- **Outliers:** 0 records (0.00%) - Clean, no outliers
  - **Age Distribution:** 20.5 to 69.1 years
  - **Consistent & Reliable:** No data quality issues

#### DAYS_REGISTRATION (Registration History)
- **Range:** -24,672 to 0 days
- **Mean:** -4,986 days (≈ 13.7 years)
- **Std Dev:** 3,522.89 days
- **Outliers:** 659 records (0.21%) - Very clean

---

## 4. DEFAULT RATE BY CATEGORICAL FEATURES

### 4.1 Contract Type (Previous Applications)

| Contract Type | Total Apps | Defaults | Default Rate |
|---------------|-----------|----------|--------------|
| **Revolving Loans** | 161,368 | 16,893 | **10.47%** ⚠️ |
| **Cash Loans** | 626,764 | 57,197 | **9.13%** |
| **Consumer Loans** | 625,256 | 48,207 | **7.71%** |
| XNA (Unknown) | 313 | 63 | 20.13% (small sample) |

**Insight:** Revolving credit shows highest default rate (10.47%), followed by cash loans (9.13%). Consumer loans are safest (7.71%).

### 4.2 Gender (Code Gender)

| Gender | Total Apps | Defaults | Default Rate |
|--------|-----------|----------|--------------|
| **Female (F)** | 202,448 | 14,170 | **7.00%** ✓ (Safer) |
| **Male (M)** | 105,059 | 10,655 | **10.14%** ⚠️ (Riskier) |
| XNA (Unknown) | 4 | 0 | 0% (negligible) |

**Key Finding:** Males default at **45% higher rate** (10.14% vs 7.00%) than females. Gender is a significant predictor.

### 4.3 Education Level

| Education Type | Total Apps | Defaults | Default Rate |
|---|---|---|---|
| **Secondary/Secondary Special** | 218,391 | 19,524 | **8.94%** |
| **Higher Education** | 74,863 | 4,009 | **5.36%** ✓ (Safest) |
| **Incomplete Higher** | 10,277 | 872 | **8.48%** |
| **Lower Secondary** | 3,816 | 417 | **10.93%** ⚠️ |
| **Academic Degree** | 164 | 3 | **1.83%** |

**Key Finding:** Higher education correlates with lower default risk (5.36%). Lower secondary education has highest default rate (10.93%).

### 4.4 Marital Status (Family Status)

| Status | Total Apps | Defaults | Default Rate |
|--------|-----------|----------|--------------|
| **Married** | 196,432 | 14,850 | **7.56%** ✓ |
| **Widow** | 16,088 | 937 | **5.82%** ✓ |
| **Separated** | 19,770 | 1,620 | **8.19%** |
| **Civil Marriage** | 29,775 | 2,961 | **9.94%** ⚠️ |
| **Single / Not Married** | 45,444 | 4,457 | **9.81%** ⚠️ |

**Key Finding:** Married applicants have lowest default (7.56%). Single/Civil marriage applicants have higher risk (9.81-9.94%).

### 4.5 Housing Type

| Housing Type | Total Apps | Defaults | Default Rate |
|---|---|---|---|
| **Rented Apartment** | 4,881 | 601 | **12.31%** ⚠️ (Highest) |
| **With Parents** | 14,840 | 1,736 | **11.70%** ⚠️ |
| **Municipal Apartment** | 11,183 | 955 | **8.54%** |
| **House / Apartment** | 272,868 | 21,272 | **7.80%** ✓ |
| **Office Apartment** | 2,617 | 172 | **6.57%** ✓ |
| **Co-op Apartment** | 1,122 | 89 | **7.93%** |

**Key Finding:** Home ownership (House/Apartment) is safest (7.80%). Renters have **58% higher default rate** (12.31% vs 7.80%).

### 4.6 Organization Type (Top 10 by Volume)

| Organization | Total Apps | Defaults | Default Rate |
|---|---|---|---|
| **Self-employed** | 38,412 | 3,908 | **10.17%** ⚠️ |
| **Trade: type 7** | 7,831 | 740 | **9.45%** |
| **Business Entity Type 3** | 67,992 | 6,323 | **9.30%** |
| **Business Entity Type 2** | 10,553 | 900 | **8.53%** |
| **Other** | 16,683 | 1,275 | **7.64%** |
| **Government** | 10,404 | 726 | **6.98%** |
| **Medicine** | 11,193 | 737 | **6.58%** |
| **School** | 8,893 | 526 | **5.91%** ✓ |
| **Kindergarten** | 6,880 | 484 | **7.03%** |
| **XNA** | 55,374 | 2,990 | **5.40%** ✓ |

**Key Finding:** Self-employed workers have highest default rate (10.17%). Government and education workers are safest (<7%).

---

## 5. DEMOGRAPHIC ANALYSIS

### 5.1 Age Distribution & Default Risk

**Age Statistics:**
- **Minimum Age:** 20.5 years
- **Maximum Age:** 69.1 years
- **Mean Age:** 43.9 years
- **Median Age:** 43.2 years

| Age Group | Total Apps | Defaults | Default Rate |
|-----------|-----------|----------|--------------|
| **18-25 years** | 12,159 | 1,496 | **12.30%** ⚠️ (Highest) |
| **25-35 years** | 72,302 | 7,717 | **10.67%** ⚠️ |
| **35-45 years** | 84,274 | 7,083 | **8.40%** |
| **45-55 years** | 70,077 | 4,946 | **7.06%** |
| **55-65 years** | 60,596 | 3,281 | **5.41%** ✓ |
| **65+ years** | 8,103 | 302 | **3.73%** ✓ (Lowest) |

**Key Finding:** 
- Younger applicants (18-25) default at **3.3× the rate** of seniors (65+): 12.30% vs 3.73%
- Default risk decreases steadily with age
- **Prime lending age:** 55-65 years (5.41% default rate)
- **Highest risk age:** Under 25 (12.30% default rate)

### 5.2 Employment Tenure Analysis

⚠️ **CRITICAL DATA QUALITY ISSUE:**
- Only **55,376 valid employment records** (18% of 307,511)
- Majority of DAYS_EMPLOYED values are invalid/corrupt
- Values include impossible data points (>1000 years employment)
- **Recommendation:** Use alternative employment/stability indicators if available

---

## 6. ENGINEERED FEATURES ANALYSIS

### 6.1 Loan-to-Income Ratio (AMT_CREDIT / AMT_INCOME_TOTAL)

**What It Measures:** Financial burden relative to income; higher = riskier

| Statistic | Value |
|-----------|-------|
| **Median Ratio** | **3.27** |
| **Mean Ratio** | **3.74** |
| **Min Ratio** | 0.02 |
| **Max Ratio** | 85.0 |
| **Distribution Shape** | Right-skewed (many applicants with moderate ratios, some extreme outliers) |

**Insight:**
- **Median 3.27** means typical borrower requests credit = 3.27× annual income
- Indicates **significant financial leverage** across portfolio
- Applicants with ratios >1.0 (credit exceeds income) = high-risk group
- Use as **primary numeric risk indicator**

### 6.2 Years Employed (Converted from DAYS_EMPLOYED)

⚠️ **Data Quality Issue:** Most values are corrupted/invalid (see Section 3.1)

**Clean Records Only:**
- **Valid Records:** 55,376 (18%)
- **Median Employment:** 10.0+ years (where data is valid)
- **Implication:** Cannot reliably use employment tenure for current analysis

**Alternative:** Use employment status flags and occupation type instead.

### 6.3 Age (Converted from DAYS_BIRTH)

**Statistics:**
- **Mean Age:** 43.9 years
- **Median Age:** 43.2 years
- **Age Range:** 20.5 - 69.1 years
- **Distribution:** Relatively uniform across age groups (see Section 5.1)

**Key Insight:** Age is a reliable, high-quality feature with strong predictive power for default risk.

---

## 7. NUMERIC FEATURES CORRELATION WITH DEFAULT

### Top 15 Features Most Correlated with Default Risk

| Rank | Feature | Correlation |
|------|---------|-------------|
| 1 | TARGET (Self) | 1.000 |
| 2 | **DAYS_BIRTH** (Age) | **+0.0782** ⭐ |
| 3 | **REGION_RATING_CLIENT_W_CITY** | **+0.0609** |
| 4 | **REGION_RATING_CLIENT** | **+0.0589** |
| 5 | **DAYS_LAST_PHONE_CHANGE** | **+0.0552** |
| 6 | **DAYS_ID_PUBLISH** | **+0.0515** |
| 7 | **REG_CITY_NOT_WORK_CITY** | **+0.0510** |
| 8 | **FLAG_EMP_PHONE** | **+0.0460** |
| 9 | **REG_CITY_NOT_LIVE_CITY** | **+0.0444** |
| 10 | **FLAG_DOCUMENT_3** | **+0.0443** |
| 11 | **DAYS_REGISTRATION** | **+0.0420** |
| 12 | **OWN_CAR_AGE** | **+0.0376** |
| 13 | **LIVE_CITY_NOT_WORK_CITY** | **+0.0325** |
| 14 | **DEF_30_CNT_SOCIAL_CIRCLE** | **+0.0322** |
| 15 | **DEF_60_CNT_SOCIAL_CIRCLE** | **+0.0313** |

**Key Insights:**
- All correlations are **weak-to-moderate** (all <0.10)
- **Age (DAYS_BIRTH)** is the strongest numeric correlator
- **Region rating** and **geographic mismatch** are meaningful signals
- **Document submission flags** and **phone information** have predictive power
- **Social circle defaults** (defaults in applicant's network) show correlation

---

## 8. KEY FINDINGS & DEFAULTER PROFILES

### 8.1 Who Are The Defaulters?

Based on the analysis, **high-risk defaulters typically have:**

1. **Demographic Profile:**
   - Age: 18-35 years (10-12% default rates)
   - Gender: Male (10.14% vs 7.00% for females)
   - Education: Lower secondary or secondary only (8-11% default rates)
   - Marital Status: Single or in civil marriage (9.8-9.9% default rates)

2. **Housing & Living Situation:**
   - Renting apartment (12.31% default rate - highest!)
   - Living with parents (11.70% default rate)
   - Geographic mismatch: Registration/residence/work in different cities
   - Unstable living situation

3. **Employment & Income:**
   - Self-employed (10.17% default rate)
   - Irregular income source
   - Lower education correlates with less stable employment

4. **Credit Profile:**
   - High Loan-to-Income Ratio (>3.0 means credit ≈ 3× income)
   - Large credit amounts requested
   - High annuity payments relative to income
   - Previous rejections or failed credit applications
   - Cash loans or revolving credit (vs consumer loans)

5. **Behavioral Signals:**
   - Defaults in social circle (friends/family who defaulted)
   - Infrequent document submissions
   - Recent phone number changes
   - Recent ID publication changes
   - Multiple previous applications (persistence = desperation?)

### 8.2 Lowest Risk Profile (Non-Defaulters)

**Safe borrowers typically have:**
- Age: 55-65+ years (3.7-5.4% default rates)
- Gender: Female (7.00%)
- Education: Higher education/Academic degree (<6% default)
- Marital Status: Married or widow (5.8-7.6%)
- Housing: Own house/apartment (7.80%)
- Employment: Government, education, medicine sectors
- Loan Type: Consumer loans (7.71%) vs cash/revolving
- Stable geographic presence (registry = residence = work city)
- Strong documentation and contact information

---

## 9. CATEGORICAL RISK SIGNALS

### 9.1 Highest Risk Categories

| Category Type | High-Risk Option | Default Rate |
|---|---|---|
| **Contract Type** | Revolving Loans | 10.47% |
| **Gender** | Male | 10.14% |
| **Housing Type** | Rented Apartment | 12.31% |
| **Age Group** | 18-25 years | 12.30% |
| **Education** | Lower Secondary | 10.93% |
| **Employment** | Self-employed | 10.17% |
| **Marital Status** | Single/Civil Marriage | 9.8-9.9% |

### 9.2 Lowest Risk Categories

| Category Type | Low-Risk Option | Default Rate |
|---|---|---|
| **Housing Type** | Office/Co-op Apartment | 6.57-7.93% |
| **Age Group** | 65+ years | 3.73% |
| **Education** | Academic Degree | 1.83% |
| **Organization** | School/Kindergarten | 5.91-7.03% |
| **Gender** | Female | 7.00% |
| **Marital Status** | Widow | 5.82% |
| **Contract Type** | Consumer Loans | 7.71% |

---

## 10. DATA QUALITY SUMMARY

### Issues Identified

| Issue | Severity | Columns Affected | Action Taken |
|-------|----------|-----------------|-------------|
| **High Missingness (>40%)** | 🔴 Critical | 15+ columns | Dropped from analysis |
| **DAYS_EMPLOYED Corruption** | 🔴 Critical | Employment data | Only 18% valid records |
| **Impossible Values** | 🟡 Warning | DAYS_EMPLOYED | Employment >1000 years |
| **Outliers in Income** | 🟡 Warning | AMT_INCOME_TOTAL | 4.56% extreme values |
| **Outliers in Credit** | 🟡 Warning | AMT_CREDIT | 2.13% extreme values |
| **Region Encoding** | 🟡 Warning | COMMONAREA, LIVING* | 68-70% missing data |

### Data Quality After Cleaning

✅ **After removing high-missing columns and filling remaining gaps:**
- **Zero missing values** in final dataset
- **All records cleaned and ready for modeling**
- **Unreliable features (DAYS_EMPLOYED) flagged** for caution

---

## 11. MODELING RECOMMENDATIONS

### 11.1 Feature Selection Priority

**Tier 1 (Must-Have Features):**
- Age (DAYS_BIRTH) - continuous + age groups
- Gender (CODE_GENDER) - categorical
- Housing Type (NAME_HOUSING_TYPE) - categorical
- Loan-to-Income Ratio - engineered
- Education (NAME_EDUCATION_TYPE) - categorical
- Marital Status (NAME_FAMILY_STATUS) - categorical
- Contract Type (from previous apps)

**Tier 2 (Important Features):**
- Region Rating
- Employment Type / Organization Type
- Credit Amount (AMT_CREDIT)
- Income (AMT_INCOME_TOTAL)
- Annuity (AMT_ANNUITY)
- Document flags
- Geographic mismatch flags

**Tier 3 (Use with Caution):**
- DAYS_EMPLOYED (too many corrupted records)
- Columns with >40% missing (already dropped)

### 11.2 Outlier Handling Strategy

**For Tree-Based Models (Random Forest, XGBoost):**
- ✅ Keep outliers as-is (robust to extreme values)
- Use engineered features like Loan-to-Income Ratio

**For Linear Models (Logistic Regression):**
- Cap/flag extreme income values (top 5%)
- Cap extreme credit amounts (>$2M)
- Use robust scaling instead of standard scaling
- Consider log-transformation of income and credit

### 11.3 Class Imbalance Handling

**Issue:** Only 8.07% defaults (highly imbalanced)

**Solutions:**
- Use class weights in model training
- Oversample minority class (defaults)
- Undersample majority class (non-defaults)
- Use stratified cross-validation
- Monitor F1-Score, Precision, Recall (not just accuracy)

### 11.4 Feature Engineering Ideas

**From Categorical Features:**
- Risk score based on contract type (revolving = higher risk)
- Gender-education interaction (highest risk: male + lower education)
- Housing stability score
- Age-employment interaction

**From Numeric Features:**
- Loan-to-Income Ratio ✓ (already created)
- Income-per-Annuity ratio
- Credit-per-Age ratio (younger applicants with large loans = risky)

**From Previous Applications:**
- Previous application count (frequency = persistence/desperation)
- Previous rejection rate
- Time since last application
- Contract type consistency
- Previous default rate in social circle

---

## 12. EXECUTIVE RECOMMENDATIONS

### Immediate Actions (Model Development)

1. **Focus on High-Impact Features:**
   - Age (clearly predictive, high-quality data)
   - Gender (45% difference in default rates)
   - Housing Type (58% range in default rates)
   - Loan-to-Income Ratio (primary financial stress indicator)

2. **Handle Data Quality Issues:**
   - Drop or flag DAYS_EMPLOYED due to corruption
   - Remove/drop all >40% missing columns
   - Create binary flags for geographic mismatches

3. **Address Class Imbalance:**
   - Use stratified sampling
   - Apply class weights during training
   - Focus on F1-Score and precision-recall

### Medium-Term Actions (Risk Management)

1. **Segment Portfolio by Risk:**
   - High-Risk (>10% default rate): Young males, renters, self-employed
   - Medium-Risk (8-10%): Civil marriage, lower education
   - Low-Risk (<6%): Older, married, higher education, home owners

2. **Target Approval Criteria:**
   - Tighter review for revolving loans (10.47% default)
   - Stricter income verification for high Loan-to-Income ratios
   - Require collateral/guarantor for renters (12.31% default)

3. **Pricing Adjustment:**
   - Higher interest rates for high-risk categories
   - Premium for young male applicants (+3-4% higher rate)
   - Premium for renters (+4-5% higher rate)

### Long-Term Actions (Data Collection)

1. **Improve Data Quality:**
   - Fix DAYS_EMPLOYED collection/encoding
   - Verify income documentation requirements
   - Standardize geographic information

2. **Collect Missing Signals:**
   - Employment stability indicators (tenure, job changes)
   - Savings/emergency fund presence
   - Debt-to-Income Ratio trends
   - Payment history on other debts

3. **Behavioral Tracking:**
   - Monitor application frequency
   - Track geographic/contact changes
   - Monitor social circle credit events
   - Develop alternative employment stability scores

---

## 13. TECHNICAL APPENDIX

### Outlier Definition Used
Statistical outliers identified using **1.5× Interquartile Range (IQR) method:**
- Values < Q1 - 1.5×IQR OR > Q3 + 1.5×IQR are flagged as outliers
- This is the industry-standard definition used in boxplots

### Default Rate Calculation
```
Default Rate = (Number of Defaults) / (Total Applications) × 100%
```

### Features Dropped Due to Missing Values
Threshold: >40% missing → 15+ columns dropped from application data, 5+ from previous applications

### Correlation Interpretation
- Correlation +0.10 to +0.30 = weak positive
- Most features show weak correlation with default
- Suggests **multivariate modeling** needed (features interact)

---

## 14. CONCLUSION

This EDA identified clear patterns in loan default risk:

✅ **Strongest Predictors:**
1. Age (younger = riskier)
2. Gender (males riskier)
3. Housing Type (renters riskier)
4. Loan-to-Income Ratio (higher = riskier)
5. Education (lower = riskier)

⚠️ **Data Quality Concerns:**
1. DAYS_EMPLOYED severely corrupted (82% invalid)
2. Multiple features with >40% missing (dropped)
3. Small outlier populations in income/credit

🎯 **Next Steps:**
1. Build classification model using Tier 1 features
2. Test ensemble methods (XGBoost handles outliers well)
3. Implement stratified cross-validation
4. Monitor F1-Score and recall (not just accuracy)
5. Develop risk scorecards based on top factors

---

**Report Generated from:** Bank Loan Defaulter Risk Analysis EDA  
**Analysis Date:** 2025  
**Dataset:** Application Data (307,511 records) + Previous Applications (1,670,214 records)  
**Target Variable:** Default Status (8.07% default rate)

