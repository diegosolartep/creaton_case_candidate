# BEES Smart Sales - Purchase Propensity & SKU Analytics Case

## 📋 Case Overview

This case study focuses on helping ABI's sales team maximize effectiveness by analyzing purchase patterns and generating SKU-level recommendations for Points of Consumption (POCs). The analysis involves building a customer propensity model and creating targeted SKU recommendations.

**Dataset:** ABI's BEES platform data with 500 POCs, 2,850 orders, 14,215 line items, and 25 SKUs across 4 categories.

**Time Estimate:** ~50 minutes

## 🎯 Learning Objectives

- Data exploration and multi-level aggregations
- Feature engineering for customer segmentation
- Classification modeling for purchase propensity
- Recommendation system development
- Business insight generation

## 📊 Dataset Description

### Data Files
- **`customers.csv`** - Customer master data (500 customers)
  - `customer_id`, `poc_type`, `avg_order_value`, `visit_frequency`, `loyalty_score`, etc.
- **`orders.csv`** - Order transactions (2,850 orders)
  - `order_id`, `customer_id`, `order_date`, `order_value`, `payment_method`, `delivery_method`
- **`order_skus.csv`** - Order line items (14,215 line items)
  - `order_id`, `customer_id`, `sku`, `product_name`, `category`, `quantity`, `unit_price`, `line_value`, `margin`
- **`products_catalog.csv`** - Product/SKU master (25 SKUs)
  - `sku`, `product_name`, `category`, `unit_price`, `margin`, `supplier`, `availability`
- **`sales_visits.csv`** - Sales visit history (8,814 visits)
  - `visit_id`, `customer_id`, `visit_date`, `sales_rep_id`, `visit_duration`, `visit_outcome`, `next_visit_scheduled`

### POC Types
- Bar
- Club
- Hotel
- Kiosk
- Restaurant
- Supermarket

### Product Categories
- Beer
- Spirits
- Wine
- NAB (Non-Alcoholic Beverages)
- Snacks

## 🚀 How to Solve the Case

### Prerequisites
- Python 3.7+
- Jupyter Notebook
- Required libraries: pandas, numpy, matplotlib, seaborn, scikit-learn

### Step 1: Environment Setup
```bash
# Install required packages
pip install pandas numpy matplotlib seaborn scikit-learn jupyter

# Start Jupyter Notebook
jupyter notebook
```

### Step 2: Data Exploration & POC × SKU Performance (20 points)

#### 2.1 Load & Explore Data (5 points)
1. **Load all 5 CSV files** into pandas DataFrames:
   - `customers` - Customer master data
   - `orders` - Order transactions
   - `order_skus` - Order line items
   - `products_catalog` - Product master
   - `sales_visits` - Visit history

2. **Perform basic exploration:**
   - Display shape, columns, and first few rows
   - Check for missing values
   - Convert date columns to datetime (`order_date`, `visit_date`)
   - Print summary statistics

#### 2.2 Data Joins & POC × SKU Analysis (10 points)
1. **Join datasets:**
   - `order_skus` + `customers` (to get POC type)
   - `order_skus` + `products_catalog` (to get category, margin)

2. **Calculate metrics by POC Type × SKU:**
   - Total revenue (`line_total`)
   - Total units sold (`quantity`)
   - Total margin (`revenue × margin_pct`)
   - Number of unique customers who bought
   - Penetration % (customers who bought / total customers in POC)

3. **Create `poc_sku_performance` DataFrame** with columns:
   - `poc_type`, `sku`, `product_name`, `category`
   - `total_revenue`, `total_units`, `total_margin`
   - `customer_count`, `penetration_pct`

#### 2.3 Key Insights & Export (5 points)
1. **Answer key questions:**
   - Top 3 SKUs by revenue for each POC type
   - Top 3 SKUs by penetration for each POC type
   - Which categories perform best in which POC types?
   - Which POC type generates the most total revenue?

2. **Export:** `outputs/poc_sku_performance.csv`

### Step 3: Customer Propensity Modeling (30 points)

#### 3.1 Feature Engineering (10 points)
Create features for each customer:

**RFM Features:**
- `recency` - Days since last order
- `frequency` - Total number of orders
- `monetary` - Total revenue generated

**POC-Specific Features:**
- `poc_type` - One-hot encoded POC type
- `visit_count` - Total sales visits
- `conversion_rate` - Orders / visits
- `avg_order_value` - Average $ per order

**SKU Pattern Features:**
- `unique_skus` - Number of different SKUs purchased
- `pct_beer`, `pct_spirits`, `pct_nab`, `pct_snacks` - Revenue percentages by category

**Target Variable:**
- `will_purchase_next_30` - Binary (1 if customer has order in next 30 days from cutoff)

#### 3.2 Model Training & Evaluation (15 points)
1. **Prepare data:**
   - Separate features (X) and target (y)
   - Handle POC type encoding
   - Split into train (80%) and test (20%) with `random_state=42`

2. **Train model:**
   - Use Random Forest or Logistic Regression
   - Fit on training data

3. **Evaluate:**
   - Calculate: Accuracy, Precision, Recall, F1 Score
   - Display confusion matrix
   - Show feature importance (top 10)

4. **Store:**
   - Trained model in `propensity_model`
   - Test metrics in `model_metrics`

#### 3.3 Propensity Scoring & Segmentation (5 points)
1. **Score all customers:**
   - Use trained model to predict probability
   - Add `propensity_score` column

2. **Segment customers:**
   - High: propensity ≥ 0.7
   - Medium: 0.4 ≤ propensity < 0.7
   - Low: propensity < 0.4

3. **Export:** `outputs/customer_propensity_scores.csv`

### Step 4: SKU Recommendations & Business Insights (25 points)

#### 4.1 SKU Gap Analysis & Opportunity Scores (10 points)
For HIGH propensity customers only (propensity ≥ 0.7):

1. **Identify SKU gaps:**
   - Find SKUs they haven't purchased yet
   - Only consider SKUs with >30% penetration in their POC type

2. **Calculate opportunity score:**
   ```
   opportunity_score = poc_penetration × margin_pct × avg_quantity × unit_price
   ```

3. **Create `sku_recommendations` DataFrame** with columns:
   - `customer_id`, `poc_type`, `recommended_sku`, `sku_name`, `category`
   - `opportunity_score`, `poc_penetration_pct`, `rationale`

#### 4.2 Generate Top Recommendations (8 points)
1. **Show top 20 recommendations** sorted by opportunity score
2. **Breakdown by POC type:**
   - Number of recommendations per POC
   - Top 3 recommended SKUs with counts
   - Total opportunity value per POC
3. **Export:** Top 100 recommendations to `outputs/sku_recommendations.csv`

#### 4.3 Business Insights & Strategic Actions (7 points)
Provide data-driven insights:

**By POC Type:**
- Which POC types have highest propensity to purchase?
- Which SKUs to prioritize for each POC type?
- Total opportunity value ($) for each POC
- Which POC types have best conversion rates?

**By SKU:**
- Which SKUs have broadest appeal across all POC types?
- Which are POC-specific "hero" products?
- Top 5 cross-sell opportunities

**Strategic Recommendations:**
- Top 3 actionable strategies for the sales team
- Resource allocation across POC types
- SKU category prioritization
- Immediate revenue-driving actions

## 📁 Expected Output Files

1. **`outputs/poc_sku_performance.csv`** - Performance metrics by POC × SKU
2. **`outputs/customer_propensity_scores.csv`** - Propensity scores per customer
3. **`outputs/sku_recommendations.csv`** - Top 100 SKU recommendations

## 🎯 Key Skills Demonstrated

- **Data Manipulation:** Loading, joining, and aggregating complex datasets
- **Feature Engineering:** Creating meaningful features from raw data
- **Machine Learning:** Building and evaluating classification models
- **Business Analytics:** Generating actionable insights and recommendations
- **Data Visualization:** Presenting findings clearly

## 💡 Tips for Success

1. **Start with data exploration** - Understand the data structure before jumping into analysis
2. **Focus on business logic** - Ensure your calculations make business sense
3. **Document your approach** - Explain your methodology and assumptions
4. **Validate results** - Check that your outputs are reasonable
5. **Think strategically** - Provide actionable recommendations, not just data summaries

## 🔍 Common Pitfalls to Avoid

- Not handling missing values properly
- Incorrect date parsing and calculations
- Forgetting to convert categorical variables for modeling
- Not validating model performance metrics
- Providing generic insights without data backing
- Not considering business context in recommendations

## 📈 Evaluation Criteria

**Total Points: 75**
- Question 1 (Data Exploration): 20 points
- Question 2 (Propensity Modeling): 30 points
- Question 3 (Recommendations): 25 points

**Key Evaluation Areas:**
- Code correctness and efficiency
- Data understanding and manipulation
- Model performance and interpretation
- Business insight quality
- Output file completeness and accuracy

## 🚀 Getting Started

1. Clone or download this repository
2. Open `bees_smart_sales.ipynb` in Jupyter Notebook
3. Follow the step-by-step instructions in each cell
4. Complete all required tasks and generate output files
5. Review the completion checklist before submitting

Good luck with your analysis! 🍺📊
