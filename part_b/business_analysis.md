
### B1. Problem Formulation

# Target Variable: "items_sold" — the number of items sold in a store in a given month.

# (a) ML Problem Formulation:
This project uses computer model to predict on how many items a store will sell based on promotions, locations and time of the year. We can call it a regression problem because we are a specific number rather than putting things into categories.
By predicting these numbers for every possible sale strategy, the marketing team can easily pick the model that predicst will sell the most stuff.

# (b) Why items_sold is Better Than Revenue:
Predicting number of items sold is better than predicting total sale revenue because prices change frequenthy that can confuse the model. High revenue can mean selling few expensive items. Total number of items sold proves if promotion actually helped convincing customers to buy more.Selecting this will keep the model focused on customer behaviour instead of distracting them by varied prices.


# (c) Alternative to a Single Global Model:
Instead of having one giant model that treats every store the same, its better to build smaller models grouped by location (Ex: one model for city store and one for rural areas). Having a single model can lead to missing out most of the unique details specific to locations. At the same time, model for every single store may not have enough data to learn everything. A middle ground approach will ensure the model to be smart enough to understand different types of customers and their behaviours.





### B2.(a) Data and EDA Strategy


# Fact table — Transaction 
Transactions Table: `transaction_id`, `store_id`, `date`, `promotion_id`, `items_sold` 
# Dimention 1 - Store table
Store Table:  `store_id`, `store_size`, `location_type`, `competition_density` 
# Dimention 2 - Promotion table
Promotion  Table : `promotion_id`, `promotion_type`, `discount_rate` 
# Dimention 3 - Calendar table  
Calendar Teble:  `date`, `is_weekend`, `is_festival`, `month`, `year`

# How the Tables Are Joined
Transaction table is 'Fact Table' and all other three tables are 'Dimention Tables' joined to it.
Transaction table should be the left table and all three dimention tables are joined using left join so that no transaction record is never lost even if dimention entries are missing.


# What is the grain of the final modelling:
**One row = one store × one calendar month × one promotion type**


# what aggregations would you perform 
Once joined, the enriched transaction table is grouped by store_id × month × promotion_type`.
1. Each row represents exactly one actionable business decision
2. The target variable (`items_sold`) reflects the full impact of a promotion over a realistic deployment period
3. The model learns promotion effects at the level they are actually deployed — not at the noise-heavy individual transaction level





## B2.(b) Exploratory Data Analysis
g transformation before fitting linear mode;
**1. Hostogram of 'items sold'**
Look for Skewness, outliers and multimadality.
Check if the target is skewed or contains outliers. A right skewed distribution suggest a log transformation before fitting linear mode.

**2. Box plot - promotion type vs 'items sold'**
Look for which promotion have highest median sales volume
Identify which promotions drive the highest median sales and whether performance varies by context. Large variance signs the need for interaction features.

**3.Corelarion heatmap - numerical features vs target** 
Look for which features have strong linear relationship with items_sold
Check which features have the strongest linear relationship with items sold. Weak corelation across the board refers to tree-based model over linear regression. 

**4 Monthly sales trens - line chart over time**
Look for seasonal peaks and structural breaks. Strong seasonability confirms 'Month' and 'is_festival' features that must not be dropped

**5 Store size × location type heatmap**
Check for interaction effects between the two categorical features a disproportionate gap between combinations justifies creating an explicit interaction feature.



## B2.(c) Discribe how imbalance effects the model

When 80% of the dataset consist of transcations without any promotions, we are dealing with feature imbalance that can significantly impacts model's predective power. Model struggles to learn promotion specific effect.

Steps to address:
1. Feature engineering: Create binary 'is_promoted' flag. This allows the model to firdt distinguish between presence (or) absence of offer before trying to calculate the weight of promotion type.
2. Startified splitting: When performing temporal or random split, ensure that ratio of promoted to non-promoted transactions is preserved in both training ans testing sets to avoid evaluating on a non representative sample.
3.Resampling Technique:  Over sampling technique - Increase the number of promoted rows in the training set so that model seees them frequently. / under sampling technique - reduce the number of no-promotion records to create more balance (50-50 or 50-60 split)


Stratified Splitting: When performing your temporal or random split, ensure that the ratio of promoted to non-promoted transactions is preserved in both the training and testing sets to avoid evaluating on a non-representative sample.