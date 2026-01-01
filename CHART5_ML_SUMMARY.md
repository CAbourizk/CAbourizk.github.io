# Chart 5: ML Analysis - Predicting PT Demand from Demographics

## Summary for Google Colab Transfer

This document summarizes the machine learning analysis for Chart 5, ready to be transferred to Google Colab.

---

## Hypothesis (≤25 words)

**Working-age residents with higher education in dense areas drive PT demand; current service allocation matches demographic patterns imperfectly.**

---

## Methodology

### Data Sources
1. **2021 Census Data (ACT SA1 level)**:
   - G01: Age distribution, language spoken at home
   - G02: Median personal income
   - G43: Labour force participation, educational attainment

2. **Public Transport Data**:
   - SA1 accessibility (population density, distance to stops)
   - Weekday peak service frequency (departures per SA1)

### Target Variable (y)
- **Weekday peak departures per 1,000 residents** (proxy for PT demand/service intensity)
- Log-transformed and standardized to handle skewness

### Features (X matrix) - All Standardized
1. **% Working age population (15-64 years)** - Census G01
2. **% Bachelor degree or higher** - Census G43
3. **% Labour force participation** - Census G43
4. **Median personal income (weekly)** - Census G02
5. **% Non-English speakers at home** - Census G01
6. **Population density (persons/km²)** - Accessibility data
7. **Walking distance to nearest stop (m)** - Accessibility data

### Algorithm
- **Random Forest Regressor** (sklearn.ensemble.RandomForestRegressor)
  - n_estimators=100
  - max_depth=10
  - min_samples_split=5
  - min_samples_leaf=2
  - random_state=42

- **Why Random Forest?**
  - Captures non-linear relationships better than linear regression
  - Provides feature importance rankings
  - Robust to outliers
  - No strict assumptions about feature distributions

### Data Preprocessing
1. Merged census demographics with PT service data by SA1 code
2. Calculated percentage features from raw census counts
3. Standardized all features using StandardScaler
4. Log-transformed target variable (departures per 1k)
5. Removed SA1s with missing data or zero population
6. Final dataset: **1,161 SA1 areas**

### Train/Test Split
- **80% training** (929 SA1s)
- **20% testing** (232 SA1s)
- 5-fold cross-validation on training set

---

## Results

### Model Performance
| Metric | Value |
|--------|-------|
| **Test R²** | 0.128 |
| **Mean Absolute Error (MAE)** | 70.3 departures per 1k residents |
| **Root Mean Squared Error (RMSE)** | 387.5 departures per 1k residents |
| **Cross-validation R²** | 0.061 ± 0.120 |

### Feature Importance (Random Forest)
| Rank | Feature | Importance Score |
|------|---------|------------------|
| 1 | % Non-English speakers at home | 0.205 (20.5%) |
| 2 | Population density | 0.197 (19.7%) |
| 3 | % Bachelor+ education | 0.138 (13.8%) |
| 4 | % Working age (15-64) | 0.134 (13.4%) |
| 5 | Walking distance to stop | 0.115 (11.5%) |
| 6 | Median personal income | 0.114 (11.4%) |
| 7 | Labour force participation | 0.098 (9.8%) |

---

## Key Findings (≤25 words)

**Model shows modest fit. Non-English speakers and density are top predictors; weak fit reveals service allocation mismatches with demographic demand patterns.**

---

## Extended Interpretation

### What the Results Tell Us

1. **Modest Predictive Power (R²=0.13)**:
   - Service allocation is only weakly explained by demographics
   - This is actually policy-relevant: it suggests PT service doesn't follow a simple demographic formula
   - Points to historical/political factors, infrastructure constraints, or equity gaps

2. **Non-English Speakers Matter Most (20.5%)**:
   - Areas with diverse linguistic communities receive more service
   - Could indicate: (a) these areas have higher PT dependency, or (b) service routes historical immigrant settlement patterns
   - Potential equity consideration: culturally diverse communities may rely more on public transport

3. **Density is Critical (19.7%)**:
   - Unsurprising: denser areas justify more frequent service
   - But not perfectly correlated - some dense areas still underserved

4. **Education Level Matters (13.8%)**:
   - Higher education correlates with more service
   - Could reflect: university/college routes, white-collar employment centers, or historical development patterns

5. **Income Has Modest Effect (11.4%)**:
   - Weaker than expected
   - Suggests service isn't strongly biased toward wealthy or poor areas
   - More nuanced equity story than simple income segregation

### Policy Implications

**Underserved Areas** (positive residuals - above the diagonal line):
- High demographic demand but relatively low service
- Potential candidates for service expansion
- May represent equity gaps or recent population growth areas

**Overserved Areas** (negative residuals - below the diagonal line):
- More service than demographics would predict
- Could be: legacy routes, major transfer points, or areas with strategic importance

### Limitations

1. **Target Variable**: Using "departures per 1k residents" as demand proxy is imperfect
   - Actual ridership data would be better
   - Service intensity ≠ actual usage

2. **Causality**: Can't distinguish:
   - Does service follow demographics? OR
   - Do demographics follow service (people move to well-served areas)?

3. **Temporal Mismatch**: 2021 Census vs 2024/2025 PT service data
   - Demographics may have shifted
   - Service changes since census

4. **Missing Variables**:
   - Car ownership rates
   - Employment location (not home location)
   - Student populations
   - Disability/mobility needs

---

## Visualization Features

The interactive Vega-Lite chart includes:

1. **Color-by Options**:
   - District (SA3) - geographic patterns
   - % Bachelor+ Education - educational attainment patterns
   - Population Density - urban intensity patterns
   - Prediction Error (residual) - underserved (red) vs overserved (blue)

2. **District Filter**: Highlight individual SA3 districts

3. **Tooltip Information**: Full demographic and service details for each SA1

4. **Log Scale Axes**: Handle wide range of service intensities (1 to 6000 departures/1k)

---

## Files Generated

1. **Script**: `scripts/ml_pt_demand_demographics.py`
   - Loads and merges census data
   - Engineers demographic features
   - Trains Random Forest model
   - Outputs predictions

2. **Data**: `data/chart5_predictions.csv`
   - 1,161 rows (SA1 areas)
   - Actual service, predicted service, residuals
   - All demographic features for exploration

3. **Visualization**: `Project/json/chart5.json`
   - Vega-Lite specification
   - Interactive predicted vs actual scatter plot

---

## Next Steps for Google Colab

To recreate this analysis in Google Colab:

1. **Upload the Python script** (`ml_pt_demand_demographics.py`)

2. **Upload required data files**:
   - `data/chart2b_sa1_accessibility.csv`
   - `data/chart7_sa1_service.csv`
   - `data/raw/2021Census_G01_ACT_SA1.csv`
   - `data/raw/2021Census_G02_ACT_SA1.csv`
   - `data/raw/2021Census_G43_ACT_SA1.csv`

3. **Install dependencies**:
   ```python
   !pip install pandas numpy scikit-learn matplotlib seaborn
   ```

4. **Run the analysis**:
   ```python
   !python ml_pt_demand_demographics.py
   ```

5. **Create visualizations** in matplotlib/seaborn or export for Vega-Lite

6. **Share the Colab link** with read access

---

## Alternative Analyses to Consider

If you want to expand the analysis:

1. **Classification instead of regression**: Predict high/low service areas (binary)

2. **Clustering**: Group SA1s by demographic similarity, analyze service equity

3. **Residual analysis**: Deep dive into the most underserved areas

4. **Different algorithms**:
   - Gradient Boosting (XGBoost/LightGBM)
   - Neural networks
   - Compare performance

5. **Feature engineering**:
   - Age diversity (entropy measure)
   - Income inequality within SA1
   - Distance to CBD
   - Car ownership rates (if available)

---

**Analysis completed**: 2024-12-24
**Model**: Random Forest Regressor (sklearn)
**Dataset**: 1,161 ACT SA1 areas, 7 features
**Performance**: R²=0.128, MAE=70.3 departures/1k
