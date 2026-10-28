# Customer-Shopping-Behavior-Analysis

An end to end analytics project (Python, SQL and Power BI) on 3,900 retail purchases, built to understand what drives customer spending, loyalty and subscriptions.

### Dashboard Link : https://app.powerbi.com/groups/me/reports/ab712d29-a889-464f-8d35-a1ec3f05a8ae/34af425ccd009a991436?experience=power-bi

## Problem Statement

A retail company wants to better understand its customers' shopping behavior in order to improve sales, customer satisfaction and long term loyalty. The management team has noticed changes in purchasing patterns across demographics and product categories, and wants to know which factors, such as discounts, reviews, seasons, shipping or payment preferences, drive consumer decisions and repeat purchases.

The overarching business question is :

**"How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?"**

## Deliverables

1. **Data Preparation & Modeling (Python)** : clean and transform the raw dataset.
2. **Data Analysis (SQL)** : load the data into PostgreSQL and run queries on customer segments, loyalty and purchase drivers.
3. **Visualization & Insights (Power BI)** : an interactive dashboard of the key patterns.
4. **Report and Presentation** : a project report (PDF) and a slide deck with findings and recommendations.
5. **GitHub Repository** : all scripts, queries and dashboard files.

## Tools Used

- Python (pandas, SQLAlchemy, psycopg2) in Jupyter Notebook
- PostgreSQL (pgAdmin)
- Power BI Desktop and Power BI Service

## Repository Structure

```
├── customer_shopping_behavior.csv                 # raw dataset (3,900 rows, 18 columns)
├── Customer_Shopping_Behavior_Analysis.ipynb      # data cleaning, feature engineering, load to PostgreSQL
├── customer_behavior_sql_queries.sql              # 10 business questions in SQL
├── customer_behavior_dashboard.pbix               # Power BI dashboard
├── Customer_Shopping_Behavior_Analysis.pdf        # project report
├── Customer-Shopping-Behavior-Analysis.pptx       # presentation
└── Business_Problem__Document.pdf                 # business problem statement
```

## Dataset

3,900 purchases and 18 columns.

| Group | Columns |
|---|---|
| Customer | Customer ID, Age, Gender, Location (50 US states), Subscription Status |
| Purchase | Item Purchased (25 items), Category (4), Purchase Amount (USD), Size, Color, Season |
| Behavior | Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type, Payment Method |

Only the `Review Rating` column has missing values (37 rows).

## Steps followed

### Step 1 : Data preparation in Python

- Loaded the dataset using pandas and checked its structure with `df.info()` and `df.describe(include='all')`.
- Found 37 missing values in `Review Rating` and filled them with the **median rating of the product category** of that row.

        df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(lambda x: x.fillna(x.median()))

- Renamed all columns to snake case (for example `Purchase Amount (USD)` became `purchase_amount`).
- Created `age_group` by splitting age into four equal sized groups (quartiles) with `pd.qcut`.

  | Age group | Age range | Customers |
  |---|---|---|
  | Young Adult | 18 to 31 | 1,028 |
  | Adult | 32 to 44 | 942 |
  | Middle-aged | 45 to 57 | 986 |
  | Senior | 58 to 70 | 944 |

- Created `purchase_frequency_days` by converting `frequency_of_purchases` to a number of days (Weekly = 7, Fortnightly and Bi-Weekly = 14, Monthly = 30, Quarterly and Every 3 Months = 90, Annually = 365).
- Checked whether `discount_applied` and `promo_code_used` were the same in every row. They were identical, so `promo_code_used` was dropped.

### Step 2 : Load the data into PostgreSQL

The cleaned DataFrame was loaded into a table named `customer` in a database named `customer_behavior`, using SQLAlchemy. The notebook also includes the same connection code for MySQL and MS SQL Server.

### Step 3 : Analysis in SQL

Ten business questions were answered with SQL (see `customer_behavior_sql_queries.sql`). The results are in the Insights section below.

### Step 4 : Dashboard in Power BI

The `customer` table was imported into Power BI Desktop and a one page dashboard (**Customer Behavior Dashboard**) was built.

Three measures were created :

        Number of Customers = COUNT('public customer'[customer_id])

        Average Purchase Amount = AVERAGE('public customer'[purchase_amount])

        Average Review Rating = AVERAGE('public customer'[review_rating])

The page has,

- Three KPI cards : Number of Customers, Average Purchase Amount and Average Review Rating
- Donut chart : customers by subscription status
- Column chart : revenue by category
- Column chart : customers by category
- Bar chart : revenue by age group
- Bar chart : customers by age group
- Slicers for Subscription Status, Gender, Category and Shipping Type

The report was then published to Power BI Service.

# Snapshot of Dashboard (Power BI Service)

<img width="1917" height="1015" alt="Image" src="https://github.com/user-attachments/assets/df55121e-1f1a-4a0c-b242-bba7006f6cd9" />

# Report Snapshot (Power BI DESKTOP)

<img width="1917" height="1011" alt="Image" src="https://github.com/user-attachments/assets/cce9a29b-0cbc-4a6c-9363-0b92d29d78e2" />

# Insights

All figures were calculated from the cleaned data and match the SQL results in the project report.

### [1] Overall

   Total purchases = 3,900

   Total revenue = 233,081 USD

   Average purchase amount = 59.76 USD

   Average review rating = 3.75 / 5

   43 % of purchases used a discount (1,677 purchases)

### [2] Revenue by Gender (Q1)

   | Gender | Customers | Revenue | Share |
   |---|---|---|---|
   | Male | 2,652 (68 %) | 157,890 | 67.7 % |
   | Female | 1,248 (32 %) | 75,191 | 32.3 % |

           thus, male customers bring in about two thirds of the revenue, mainly because there are twice as many of them. The average purchase is almost the same (60.25 for female and 59.54 for male).

### [3] High Spending Discount Users (Q2)

   839 customers used a discount and still spent at least the average purchase amount (59.76 USD). These are half of all the 1,677 discounted purchases.

### [4] Top 5 Products by Average Rating (Q3)

   1) Gloves - 3.86
   2) Sandals - 3.84
   3) Boots - 3.82
   4) Hat - 3.80
   5) Skirt - 3.78 (tied with Handbag at 3.78)

### [5] Shipping Type (Q4)

   Express shipping = 60.48 USD average purchase

   Standard shipping = 58.46 USD average purchase

           thus, express customers spend about 3.5 % more per purchase, which is a small difference.

### [6] Subscribers vs. Non-Subscribers (Q5)

   | Status | Customers | Average spend | Total revenue | Revenue share |
   |---|---|---|---|---|
   | No | 2,847 | 59.87 | 170,436 | 73.1 % |
   | Yes | 1,053 | 59.49 | 62,645 | 26.9 % |

           thus, subscribers do not spend more than non-subscribers. Their average spend is slightly lower, and they are 27 % of customers.

### [7] Discount Dependent Products (Q6)

   Top 5 products by the share of purchases made with a discount :

   1) Hat - 50.00 %
   2) Sneakers - 49.66 %
   3) Coat - 49.07 %
   4) Sweater - 48.17 %
   5) Pants - 47.37 %

   The average purchase with a discount (59.28) is about the same as one without (60.13).

### [8] Customer Segments (Q7)

   Customers were grouped by the number of previous purchases (1 = New, 2 to 10 = Returning, more than 10 = Loyal).

   | Segment | Customers | Share |
   |---|---|---|
   | Loyal | 3,116 | 79.9 % |
   | Returning | 701 | 18.0 % |
   | New | 83 | 2.1 % |

           thus, almost 80 % of customers fall in the Loyal segment. Since previous purchases range from 1 to 50, this threshold puts most customers in one group, so the segment limits may need to be raised to be more useful.

### [9] Top 3 Products in Each Category (Q8)

   | Category | 1st | 2nd | 3rd |
   |---|---|---|---|
   | Accessories | Jewelry (171) | Sunglasses (161) | Belt (161) |
   | Clothing | Blouse (171) | Pants (171) | Shirt (169) |
   | Footwear | Sandals (160) | Shoes (150) | Sneakers (145) |
   | Outerwear | Jacket (163) | Coat (161) | - |

   Several products are tied on the number of orders, so the exact order within a category can vary.

### [10] Repeat Buyers & Subscriptions (Q9)

   Among customers with more than 5 previous purchases (3,476) : 958 are subscribed (27.6 %) and 2,518 are not.

   Customers with 5 or fewer previous purchases subscribe at 22.4 %, and all customers at 27.0 %.

           thus, repeat buyers are only slightly more likely to subscribe.

### [11] Revenue by Age Group (Q10)

   | Age group | Revenue | Share |
   |---|---|---|
   | Young Adult (18 to 31) | 62,143 | 26.7 % |
   | Middle-aged (45 to 57) | 59,197 | 25.4 % |
   | Adult (32 to 44) | 55,978 | 24.0 % |
   | Senior (58 to 70) | 55,763 | 23.9 % |

           thus, revenue is spread almost evenly across the four age groups, with Young Adults slightly ahead.

### [12] Revenue by Category

   | Category | Purchases | Revenue |
   |---|---|---|
   | Clothing | 1,737 | 104,264 |
   | Accessories | 1,240 | 74,200 |
   | Footwear | 599 | 36,093 |
   | Outerwear | 324 | 18,524 |

   Clothing gives 44.7 % of revenue. By season, Fall is the highest (60,018) and Summer the lowest (55,777).

# Business Recommendations

- **Review the subscription offer** : subscribers are only 27 % of customers and spend about the same as non-subscribers, so the benefits should be made worth subscribing for (exclusive products or early access) before promoting it more.
- **Loyalty programs** : since most customers already have more than 10 previous purchases, a reward program should focus on keeping them, and the 83 new customers should be encouraged to come back.
- **Review the discount policy** : 43 % of purchases use a discount but they do not raise the average purchase amount. Discounts on Hats, Sneakers and Coats (about half of their purchases) should be checked for margin.
- **Product positioning** : highlight the top rated products (Gloves, Sandals, Boots) and the best selling ones (Jewelry, Blouse, Pants, Jacket) in campaigns.
- **Targeted marketing** : female customers are only 32 % of the customer base with a similar average spend, which is a growth opportunity. Young Adults give the most revenue.

# Notes

1. **Dashboard measures** : the donut chart and the two "customers" column and bar charts use the sum of `customer_id` (the ID numbers added up) instead of a count of customers. These charts should use the `Number of Customers` measure.
2. **Online vs. offline** : the business problem mentions sales channels, but the dataset has no channel column, so this could not be analysed.
3. **No date column** : the data has no purchase date, so trends over time cannot be analysed. Only the season is available.
4. **Database password** : the notebook contains a real looking PostgreSQL password in plain text. It should be removed (or read from an environment variable) before the notebook is pushed to GitHub.
