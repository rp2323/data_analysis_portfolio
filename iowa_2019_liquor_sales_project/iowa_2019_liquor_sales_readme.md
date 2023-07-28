# 2019 Iowa State Liquor Sales Analysis
This project is an effort to extract insights and patterns from the 2019 Iowa liquor sale dataset available on [Iowa's public data platform](https://data.iowa.gov/Sales-Distribution/2019-Iowa-Liquor-Sales/38x4-vs5h). 

The dataset was drawn from invoiced orders from Iowa Class 'E' liquor licensees dated 1/1/2019 to 12/31/2019. 


# Table of Contents
1. [Business Problem](#business_problem)
2. [Key Findings](#key_findings)
3. [Tools](#tools)
4. [Results at a Glance](#results_at_a_glance)
5. [Limitations](#limitations)
6. [Explore the Code](#explore_the_code)
7. [Explore the Dashboard](#explore_the_dashboard)

## Business Problem
The state of Iowa's Alcoholic Beverages Division wants to know more about the liquor purhcased by licensed businesses in 2019, including the top-selling categories and items as well as the counties, cities, and class 'E' licensees (grocery stores, liquor stores, etc.) that purchased the most liquor.  

## Key Findings 

### How Much Liquor Was Ordered by Iowa Liquor Sellers in 2019?  
**$348,279,944.00**. The worst month for total sales was January, after which purchases steadily climbed toward the best month of sales in December (with one outlier peak in May).   

### What Was the Best-Selling Item?
**American vodkas**, which composed **$51,657,037.00** of the orders.

### What Was the Best-Selling Single Item?
**Tito's Handmade Vodka**, which composed **$17,639,949.00** of the orders.

### What Was the Best-Selling Brand?
**Diageo Americas**, which composed **$72,462,812.00** of the orders.

### What Were the Best-Selling County, City, and Store?

* **County** - Polk County: **$82,772,509.00**

* **City** - Des Moines: **$44,514,350.00** 

* **Store** - Central City 2 (Des Moines): **$11,874,337**

## Tools
* **SQL** - initial exploration and analysis
* **Python** - supplementary analysis (confirming SQL findings) 
* **Tableau** - visualizations

## Results at a Glance  
![](https://github.com/rp2323/data_analysis_portfolio/blob/83097a6096d332977cf9ff4beee3a78982d9b344/iowa_2019_liquor_sales_project/images/totals.png)  

![](https://github.com/rp2323/data_analysis_portfolio/blob/83097a6096d332977cf9ff4beee3a78982d9b344/iowa_2019_liquor_sales_project/images/2019_overall_time_series.png) 

![](https://github.com/rp2323/data_analysis_portfolio/blob/83097a6096d332977cf9ff4beee3a78982d9b344/iowa_2019_liquor_sales_project/images/category_treemap.png) 

![](https://github.com/rp2323/data_analysis_portfolio/blob/83097a6096d332977cf9ff4beee3a78982d9b344/iowa_2019_liquor_sales_project/images/item_heatmap.png) 

![vendor_heatmap.png](https://github.com/rp2323/data_analysis_portfolio/blob/83097a6096d332977cf9ff4beee3a78982d9b344/iowa_2019_liquor_sales_project/images/vendor_heatmap.png) 

## Limitations
* **Documentation** - the counts for store number/names, category number/names, vendor number/names were all imbalanced by small amounts (no more than 10 in all cases). Ideally, I would have been able to check these inconsistencies against documentation around the coding system. Without that, I deferred to aggregating/computing using the names, which were distinct. 
* **Confirmation** - The order dates tended to be weekdays with orders rarely occurring on weekends (less than 2%). It's reasonable to assume that there's a reasonable explanation for the rarity of weekend orders, but confirming this with someone would have helped solidify confidence in the data.  
![](https://github.com/rp2323/data_analysis_portfolio/blob/2a07ff88e734cca3779b61baac6497e4017c91d1/iowa_2019_liquor_sales_project/images/orders_by_day_of_week.png)

## Explore the Code
* [SQL Code](https://github.com/rp2323/data_analysis_portfolio/blob/main/iowa_2019_liquor_sales_project/sql/iowa_2019_liquor_sales_sql)
* [Python Code](https://github.com/rp2323/data_analysis_portfolio/tree/main/iowa_2019_liquor_sales_project/python)

## Explore the Dashboard
[Tableau Link](https://public.tableau.com/views/2019IowaLiquorSales/Overview-Dash?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link)
 

