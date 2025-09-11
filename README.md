# **AdventureWorks Bicycles Manufacturer Customer Behaviour Analysis Dashboard with Power BI**

Author: Nguyen Luu Bao Duy

Tool Used: Power BI

# **📑** Table of Contents

# **📌** Project Overview

## 🎯 Project Objectives

This project leverages the AdventureWorks dataset to develop a Power BI dashboard that enables the Executive Board to better understand customer dynamics and derive actionable segmentation. The dashboard is designed to provide clarity on who the customers are, how they behave, and how the company can strategically respond to these insights.

Our objectives are to:

- Build a comprehensive profile of the company’s customer demographics.
- Analyze purchasing patterns and behavioral trends.
- Segment customers using the RFM (Recency, Frequency, Monetary) framework.
- Recommend strategies to retain valuable customers, attract new ones, and nurture long-term growth across segments.

## 👥 Target audience

- Sales & Marketing Team
- Product Team
- Customer Service Team
- Analytics and Data Team

## **🛰️ Scope of Analysis**

- Focus exclusively on **individual (B2C) customers**.
- Rationale: B2C customers demonstrate distinct characteristics, purchasing behavior, spending power, and motivations compared to B2B customers.

## **🧩 Assumptions**

- **Reference date for analysis**: the last recorded date in the dataset.
    - *Rationale*: Since the data was dropped in June 2014, selecting the latest available date ensures meaningful insights, particularly for time-sensitive metrics such as *Age* and *Recency*.
- **Fiscal year**: January to December.
- **Currency**: All monetary values are standardized in **USD** (as the Currency table only provides conversion from USD to other currencies).

# **📂** Dataset

## **📝** Data Description

- Context: **Adventure Works Cycles** is a fictional global manufacturing company that produces and distributes bicycles and related accessories to commercial markets.
- Database: AdventureWorks
- DMBS: SQL Server
- Provided by: Microsoft
- The dataset includes a variety of modules:
    - **Business Entities:** Stores core information about companies, vendors, and contacts that AdventureWorks interacts with. Acts as the foundation for linking business partners across modules.
    - **People:** Contains individual-level information (customers, employees, sales representatives).
    - **Human Resources:** Covers employee records, departments, pay history, and job roles.
    - **Products:** Stores the product catalog, including categories, models, and details of bicycles and related items.
    - **Manufacturing:** Captures production data, work orders, and processes required to manufacture products.
    - **Purchasing:** Holds data on vendors, purchase orders, and procurement activities.
    - **Inventory:** Manages stock levels, locations, and movement of products.
    - **Sales:** Contains sales transactions, customer orders, and sales territory data.
    - **Admin:** Stores system-level and configuration data used to support operations across the database.

## **⚙️ Data Structure & Relationships**

**1️⃣ Tables Used**

| Dashboard Table Name            | Database Table Name                 | Description                                                                                   |
|---------------------------------|-------------------------------------|-----------------------------------------------------------------------------------------------|
| Dim_Customer                    | Sales.Customer                      | Stores customer master data such as ID, account number, and related attributes.               |
| Dim_Demographics                | Person.Person                       | Contains customer demographic details (gender, marital status, income, etc.), parsed from XML in the original Person.Person table. |
| Dim_Location                    | Sales.Location                      | Defines sales territories and their locations for geographic-based analysis.                  |
| Dim_Product                     | Production.Product                  | Provides detailed product information including name, number, and attributes.                 |
| Dim_ProductCategory             | Production.ProductCategory          | Groups products into high-level categories.                                                   |
| Dim_ProductSubcategory          | Production.ProductSubcategory       | Further breaks down product categories into subcategories.                                    |
| Dim_SalesReason                 | Sales.SalesReason                   | Contains reasons associated with customer purchases.                                          |
| Dim_SpecialOffer                | Sales.SpecialOffer                  | Stores information about special offers, discounts, and promotions applied to products.       |
| Fact_SalesOrderHeader           | Sales.SalesOrderHeader              | Captures header-level sales order data such as order date, customer, sales person, and territory. |
| Fact_SalesOrderDetail           | Sales.SalesOrderDetail              | Contains line-level detail for each order such as product, quantity, unit price, discount.    |
| Fact_SalesOrderHeaderSalesReason| Sales.SalesOrderHeaderSalesReason   | Bridges sales orders with sales reasons, allowing analysis of why customers made purchases.   |


**2️⃣ Table Schemas**

For detailed table schemas, kindly reach the [Data Dictionary](https://drive.google.com/file/d/1oYm32cdutBwHhkds4l-HznDYPZ5_WCxu/view)

**3️⃣ Data Relationships**

![image.png](attachment:b13e2e5c-aa53-438e-82ad-06d206328a55:image.png)

**Relationship between tables**

| From Table                      | Relationship   | To Table                         | Join Keys                                                                 | Status   |
|---------------------------------|----------------|----------------------------------|----------------------------------------------------------------------------|----------|
| `Dim_Customer`                  | Many-to-One    | `Dim_Demographics`               | `Dim_Customer.PersonID` = `Dim_Demographics.BusinessEntityID`              | Active   |
| `Dim_Customer`                  | Many-to-One    | `Dim_Location`                   | `Dim_Customer.TerritoryID` = `Dim_Location.TerritoryID`                    | Active   |
| `Dim_Demographics`              | Many-to-One    | `_Date Table`                    | `Dim_Demographics.DateFirstPurchase` = `_Date Table.Date`                  | Inactive |
| `Dim_Product`                   | Many-to-One    | `Dim_ProductSubcategory`         | `Dim_Product.ProductSubcategoryID` = `Dim_ProductSubcategory.ProductSubcategoryID` | Active   |
| `Dim_ProductSubcategory`        | Many-to-One    | `Dim_ProductCategory`            | `Dim_ProductSubcategory.ProductCategoryID` = `Dim_ProductCategory.ProductCategoryID` | Active   |
| `Fact_SalesOrderDetail`         | Many-to-One    | `Dim_Product`                    | `Fact_SalesOrderDetail.ProductID` = `Dim_Product.ProductID`                | Active   |
| `Fact_SalesOrderDetail`         | Many-to-One    | `Fact_SalesOrderHeader`          | `Fact_SalesOrderDetail.SalesOrderID` = `Fact_SalesOrderHeader.SalesOrderID` | Active   |
| `Fact_SalesOrderDetail`         | Many-to-One    | `Dim_SpecialOffer`               | `Fact_SalesOrderDetail.SpecialOfferID` = `Dim_SpecialOffer.SpecialOfferID` | Active   |
| `Fact_SalesOrderHeader`         | Many-to-One    | `Dim_Customer`                   | `Fact_SalesOrderHeader.CustomerID` = `Dim_Customer.CustomerID`             | Active   |
| `Fact_SalesOrderHeader`         | Many-to-One    | `_Date Table`                    | `Fact_SalesOrderHeader.OrderDate` = `_Date Table.Date`                     | Active   |
| `Fact_SalesOrderHeaderSalesReason` | Many-to-One | `Dim_Demographics`               | `Fact_SalesOrderHeaderSalesReason.CustomerID` = `Dim_Demographics.BusinessEntityID` | Inactive |
| `Fact_SalesOrderHeaderSalesReason` | Many-to-One | `Fact_SalesOrderHeader`          | `Fact_SalesOrderHeaderSalesReason.SalesOrderID` = `Fact_SalesOrderHeader.SalesOrderID` | Active   |
| `Fact_SalesOrderHeaderSalesReason` | Many-to-One | `Dim_SalesReason`                | `Fact_SalesOrderHeaderSalesReason.SalesReasonID` = `Dim_SalesReason.SalesReasonID` | Active   |

## **🗓️** Time Frame

- The dataset includes records spanning from 2011 to 2014.
- The analysis is conducted for the same period.

# **🧠 Design Thinking Process**

This analysis uses a process called “**Design Thinking**” to generate ideas and solve problems.

## **What is Design Thinking?**

Design Thinking is a **human-centered, iterative approach to problem-solving** that emphasizes understanding users, redefining problems, and creating innovative solutions through rapid prototyping and testing.

It is built on 5 stages:

1. **Empathize** – deeply understand the needs, pain points, and goals of users.
2. **Define** – clearly articulate the problem statement.
3. **Ideate** – generate a wide range of creative solutions.
4. **Prototype** – build quick, tangible versions of the solution.
5. **Test** – validate with users, gather feedback, and refine.

Unlike linear methods, Design Thinking is flexible and iterative, allowing teams to circle back when new insights emerge. It balances **desirability (user needs)**, **feasibility (technical capabilities)**, and **viability (business goals)**.

## **Why Design Thinking for BI projects?**

Business Intelligence (BI) initiatives often struggle when they focus too much on **tools, data models, and dashboards** while overlooking **actual user needs**. Design Thinking ensures BI solutions are not just technically correct but **practically impactful**.

Key reasons:

- **User-centric dashboards & reports**: BI outputs directly address decision-makers’ real challenges rather than overwhelming them with unused metrics.
- **Alignment with business goals**: Encourages defining the “why” behind every visualization or KPI, preventing data for data’s sake.
- **Collaboration across roles**: Breaks silos between business users, analysts, and IT by co-creating solutions.
- **Faster iteration**: Prototyping dashboards early helps validate insights before heavy data engineering investment.
- **Higher adoption**: When end-users are involved from the start, BI solutions are more intuitive, actionable, and widely adopted.

👉 In short, Design Thinking makes BI projects move from **“just reporting”** to **“strategic decision support”** by combining data with empathy, creativity, and business context.

# **📊 Key Insights & Visualizations**

## **🔍 Dashboard Preview**

### **1️⃣ Customer Demographics Overview**

🎯 **Objective**: The purpose of this section is to provide a comprehensive overview of the demographic profile of AdventureWorks’ individual (B2C) customers, highlighting key patterns that can inform marketing, product, and customer engagement strategies.

**📌 Key Findings**

AdventureWorks serves approximately 18,500 individual consumers, with the customer base largely composed of **middle-aged** individuals who demonstrate strong **long-term loyalty**. The majority of customers are concentrated in **North America**, particularly in the Southwest and Northwest regions. Income distribution is centered within the **$25,000–$75,000** range, suggesting a stable but not overly affluent customer segment. Overall, the profile indicates a **loyal**, **stable** group of consumers who are **less mobile** and highly suitable for l**ifecycle marketing strategies**, **upselling initiatives**, and **geographically targeted activations**.

**⚧ Gender and Marital Status**

- **The gender split is nearly balanced**, with 51% male and 49% female, suggesting that marketing messages do not need to be strongly gender-specific.
- A majority of 54% of customers are married, which opens **opportunities for couple-oriented or family-oriented campaigns**.

**🎓 Education**

- The majority of customers hold either a **Bachelor’s degree** or **Partial College** education, indicating a broad mid-level educational base.
- A notable proportion possess **Graduate-level degrees**, highlighting a valuable segment for **premium products** with technical or value-driven positioning.
- Marketing strategies should **balance** between **emphasizing practical product value** for those with lower formal education and **showcasing sophistication or premium attributes** for the more highly educated.

**📆 First Order Year and Tenure**

- Customer acquisition peaked in 2003, when about 7,700 individuals—representing 42% of the base—made their first purchase.
- The customer base demonstrates **loyalty**, with an average tenure of 21 years, reflecting **long-term engagement** and **strong brand history**.

**👥 Age Group**

- The customer base is heavily concentrated in the **middle-aged** segment, with 60% between **35 and 54 years old**. The largest subgroup is ages 45–54 (33%), followed by ages 35–44 (27%), both of which typically exhibit **stable income** and **consistent purchasing power**.
- **Younger consumers are underrepresented**, with ages 25–34 making up only 2% of the base, emphasizing the need for strategies to capture younger demographics.

**💼 Occupation**

- **Professionals** and **Skilled Manual** workers are the two most common occupational groups, together reflecting **stable income levels** and **solid purchasing capacity** to spend on mid-tier to high-tier products.
- Marketing approaches may need differentiation: **Professionals** are likely to respond to messages emphasizing **design**, **innovation**, while **Skilled Manual** workers may value **durability** and **performance,** and **practical utility** more highly.

**🚗 Commute and Distance to Store**

- Approximately 34% of customers live within one mile of a store. This indicates that one-third of the company’s customers are **local customers** who do not take too much effort to find a store.
- This proximity offers strong potential for **in-store promotions**, c**ommunity-based events**, and **geo-targeted advertising**.

**💵 Income**

- The majority of customers fall within the **$25,000–$75,000** income bracket, representing about 61% of the base. This group forms the **primary target** for **pricing strategies** and **promotional offers** tailored to **mid-level affordability**.
- A smaller, high-income segment (earning above **$100,000**, around 9% of the base) presents a clear opportunity for **upselling premium products** and **offering exclusive services**.

**🌍 Geography**

- **North America is the dominant market**, accounting for 51% of customers, with the Southwest (24%) and Northwest (18%) as key subregions.
- **The Pacific region**, primarily **Australia**, contributes nearly 20% of the customer base, highlighting its importance as a **secondary market**.
- **European markets -** including the **United Kingdom**, **France**, and **Germany -** also make up a meaningful share, providing opportunities for region-specific strategies.

### **2️⃣ Customer Performance Analysis**

🎯 **Objective**: The goal of this analysis is to evaluate customer purchasing performance, including order value, purchase drivers, and revenue contribution.

**📌 Key Findings**

- On average, each customer generates approximately **$1,760 in revenue** and **$632 in gross profit**. With 18,500 customers placing 28,000 orders, this equates to **1 - 2 orders per customer**, with an average order value of **$1,170**. This indicates that the purchasing behaviour appears relatively limited, with few repeat transactions. However, this pattern is reasonable given the nature of bicycles - a product with a long usage lifecycle and low replacement frequency within a short period.
- Revenue and profit growth both recorded **positive year-over-year growth from 2012 to 2013**. However, both metrics declined from 2013 to 2014. This decline is partly explained by a **data limitation**: the dataset only covers up to **June 2014.** The fact that we don’t have sufficient data for the last two quarters of 2014 indicates that making comparisons of the first two quarters of 2014 with the full-year 2013 is misleading. When viewed on a **month-by-month basis**, revenue and profit continued to show **steady growth through May 2014**.
- A similar trend is observed in **order volume growth**. In 2013 and 2014, order counts were nearly **triple the combined totals of 2011 and 2012**. While 2014 appears to show a decline, this is again due to incomplete data. Monthly analysis confirms that order volumes were growing consistently, with strong momentum particularly from **June 2013 through May 2014**.
- Analysis of **purchase motivations** reveals that **82% of customers are primarily price-driven**. Other factors, such as promotions, product quality, and brand reputation play a much smaller role. Interestingly, in 2011 and 2012, most customers purchased based on quality and brand reputation, whereas in 2013 and 2014, **price became the dominant driver**. This shift highlights **increasing price sensitivity**, suggesting the need for a well-calibrated pricing strategy or strong value-added justifications for price increases.
- **Discounts accounted for only 6–8% of sales annually**, indicating that the company does not heavily rely on promotions. This reflects a positive signal of **brand strength and pricing power**.
- Customer contribution analysis shows **no clear linear correlation between the number of orders and the revenue generated**. Instead, three distinct clusters emerge:
    1. Customers with few orders and low revenue (general customers).
    2. Customers with many orders but low revenue (primarily accessory buyers).
    3. Customers with few orders but high revenue (premium customers purchasing high-value bicycles or multiple items in a single order).
    
     👉 This reflects the dual nature of AdventureWorks’ business model - **bicycles versus bicycle accessories -** and indicates that value creation lies more in **maximizing order value** rather than driving frequent repeat purchases.
    

### **3️⃣ RFM Analysis**

🎯 **Objective**: The goal of this analysis is to segment customers using the RFM model into 11 groups.

**🏗️ What is an RFM Framework?**

RFM (Recency – Frequency – Monetary) is a customer segmentation model based on transaction history. It helps assess both the value of customers and their level of engagement with the business. Each customer is scored across three dimensions:

- **Recency (R):** The time elapsed since the customer’s most recent purchase. The shorter the gap, the higher the likelihood of repeat purchases. A longer gap, in contrast, indicates a higher risk of churn.
- **Frequency (F):** The number of transactions made within a defined period. Customers who purchase more frequently tend to have a stronger relationship with the brand and respond more actively to marketing efforts.
- **Monetary (M):** The total amount of money a customer has spent. This reflects their spending capacity and their overall contribution to company revenue.

**🌱 Benefits of RFM Analysis**

Applying the RFM framework provides several advantages:

- Increases the effectiveness of personalized marketing campaigns.
- Improves Customer Lifetime Value.
- Supports segmentation for targeted product launches.
- Strengthens customer loyalty and engagement.
- Reduces churn by identifying at-risk customers.
- Optimizes marketing spend and improves ROI.
- Enhances the performance of remarketing and retargeting campaigns.

**🛠️ Scoring Methodology**

Each customer is evaluated using the three RFM dimensions:

- **Recency:** Calculated as the difference between the analysis date and the customer’s last transaction date.
- **Frequency:** Measured by counting the total number of invoices or transactions per customer.
- **Monetary:** Measured by summing the total value of all purchases across the customer’s lifetime.

Once calculated, the values for each dimension are divided into five equal groups (quintiles), with scores assigned from 1 to 5. A score of 1 represents the lowest value, while a score of 5 represents the highest.

The scoring direction differs by dimension:

- **Recency:** Lower values (more recent activity) receive higher scores.
- **Frequency and Monetary:** Higher values receive higher scores.

By combining these three scores, each customer is assigned an **RFM score**, resulting in up to **125 unique combinations**. These combinations are then mapped into well-defined customer segments according to a segmentation framework.

| Segment                   | RFM Scores                                                                                                             | Definition                                                                                                                                                                                                    |
|----------------------------|------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 🏆 **Champions**          | 555, 554, 544, 545, 454, 455, 445                                                                                      | These are the most valuable customers who have purchased recently, buy frequently, and spend the most. They are highly loyal, willing to spend generously, and are very likely to make another purchase soon. |
| 💎 **Loyal Customers**    | 543, 444, 435, 355, 354, 345, 344, 335                                                                                 | These customers spend at a medium to high level and purchase very frequently.                                                                                                                                 |
| 💡 **Potential Loyalists**| 553, 551, 552, 541, 542, 533, 532, 531, 452, 451, 442, 441, 431, 453, 433, 432, 423, 353, 352, 351, 342, 341, 333, 323 | These are relatively new customers who have purchased recently, spend at a moderate level, and have already made more than one purchase.                                                                      |
| ✨ **New Customers**       | 512, 511, 422, 421, 412, 411, 311                                                                                      | These are brand-new customers who made their first purchase recently, with low transaction value and low frequency.                                                                                           |
| 🌱 **Promising**           | 525, 524, 523, 522, 521, 515, 514, 513, 425, 424, 413, 414, 415, 315, 314, 313                                         | These customers purchased recently with relatively high value but have not yet become frequent buyers.                                                                                                        |
| 👀 **Need Attention**      | 535, 534, 443, 434, 343, 334, 325, 324                                                                                 | These customers used to have decent purchase frequency and value but have not bought again recently.                                                                                                          |
| 😴 **About To Sleep**      | 331, 321, 312, 221, 213, 231, 241, 251                                                                                 | These customers have not purchased for quite a while and previously had low frequency and low transaction values.                                                                                             |
| ⚠️ **At Risk**             | 255, 254, 245, 244, 253, 252, 243, 242, 235, 234, 225, 224, 153, 152, 145, 143, 142, 135, 134, 133, 125, 124           | These customers have not purchased for a long time, but they used to buy very frequently with medium to high transaction values.                                                                              |
| 🚨 **Cannot Lose Them**    | 155, 154, 144, 214, 215, 115, 114, 113                                                                                 | These are long-inactive customers who previously purchased often and with high value. Without timely actions to re-engage, the business risks losing them permanently.                                        |
| ❄️ **Hibernating Customers**| 332, 322, 233, 232, 223, 222, 132, 123, 122, 212, 211                                                                 | These customers have been inactive for a long period, with low purchase frequency and low transaction value.                                                                                                  |
| ⛔ **Lost Customers**      | 111, 112, 121, 131, 141, 151                                                                                           | These are customers who have not returned for a very long time, with the lowest purchase frequency and transaction value. They are likely to have stopped using the product or switched to competitors.       |


**📌 Key Findings**

🔷 **1. Champions**

- Size: ~9%
- Characteristics: Lowest Recency, high Frequency, and highest Monetary.
- Contribution: Top contributor in both revenue and profit.
- Discounts: Receive the highest level of discounts, though unnecessary.
- Insight: Highly loyal, high-value customers who continue to purchase actively.

🔷 **2. Loyal Customers**

- Size: ~10%
- Characteristics: Strong Frequency, stable Recency, moderate Monetary.
- Contribution: Top 3 in revenue and profit, generating stable income streams.
- Discounts: Receive discounts regularly but are not highly dependent on them.
- Insight: Stable group with strong potential to grow into Champions.

🔷 **3. Potential Loyalists**

- Size: ~5%
- Characteristics: Highest Frequency, higher Recency, but low Monetary.
- Contribution: Limited due to lower spending.
- Discounts: Minimal impact from discounts, given the low number of transactions.
- Insight: These customers return often but spend relatively little.

🔷 **4. Recent Customers**

- Size: Largest group (~15%)
- Characteristics: Very recent purchases, but low Frequency and Monetary.
- Contribution: Small, with limited long-term value.
- Discounts: Used but with little impact.
- Insight: Newly acquired customers with low spend and frequency → high churn risk.

🔷 **5. Promising**

- Size: ~14% (second-largest group)
- Characteristics: Recently purchased, low Frequency, low spend, but strong potential.
- Contribution: Moderate.
- Discounts: Applied but with limited effectiveness.
- Insight: Similar to Recent Customers, except for significantly higher spend → require a small push to grow.

🔷 **6. At Risk**

- Size: ~12%
- Characteristics: High Recency (~~286 days), very high Monetary (~~$4.8K), medium Frequency.
- Contribution: High-value group but inactive for a long time.
- Discounts: Low, though they need it most.
- Insight: Previously frequent buyers with high basket sizes → extremely valuable if reactivated.

🔷 **7. Can’t Lose Them**

- Size: ~12%
- Characteristics: Previously frequent and high spenders, but almost inactive now.
- Contribution: Once high, now dropped significantly.
- Discounts: Offered but insufficient to retain.
- Insight: Long inactive, once premium customers → may have disengaged due to dissatisfaction.

🔷 **8. Hibernating & About to Sleep**

- Size: ~13–14%
- Characteristics: High Recency, low Frequency, low Monetary.
- Contribution: Minimal.
- Discounts: Ineffective, with low interaction.
- Insight: Inactive former customers with negligible value.

🔷 **9. Customers Needing Attention**

- Size: ~2%
- Characteristics: Average Recency, moderate Frequency and Monetary.
- Contribution: Almost negligible.
- Discounts: Ineffective, with low response.
- Insight: A small, undifferentiated group that risks being overlooked.

🔷 **10. Lost Customers**

- Size: ~8%
- Characteristics: Very high Recency, no purchases for several years.
- Contribution: Almost none.
- Insight: These customers have already left and are unlikely to return.

# **🚀 Final Conclusions and Recommendations**

## **📇 Customer Profile Overview**

AdventureWorks owns a **mid-aged customer base** (avg. age 52), with **stable income levels (USD 25k–75k)** and **long-term loyalty** (avg. tenure > 21 years).

Most customers are concentrated in the **Southwest and Northwest regions of North America**, making regional marketing strategies highly relevant.

Demographic characteristics suggest potential in **family/couple-based campaigns** and the **mid-to-upper income segments**.

## **🛍️ Purchasing Patterns and Customer Behaviour**

- On average, each customer makes **1-2 purchases**, with an **average order value of ~USD 1,170**. 
👉 Indicates relatively **low purchase frequency**, but reasonable for the product category (bicycles, accessories).
- Revenue and profit maintained steady monthly growth until **June 2014**. However, note that **data for the second half of 2014 is missing**, which limits full-year comparability.
- **Price is the primary purchase driver (82%)**, while factors such as quality and brand mattered more in earlier years. 
👉 Indicates a **shift toward price sensitivity**, requiring **repositioning of value perception** rather than only competing on price.
- **Discount usage remains low (6–8%)**, showing that the business is **not overly reliant on promotions**. 
👉 This is a **positive signal of brand strength** and also an opportunity to **optimize targeted discounts**.

## **💡 Recommendations**

- **Retain Champions – Loyal Customers – Potential Loyalists**: Strengthen their brand relationship via loyalty programs, exclusive offers, VIP privileges, personalized experiences, and premium positioning.
- **Nurture Recent Customers – Promising**: Encourage repeat purchases with incentives, tailored product recommendations, automated follow-ups, post-purchase care, and loyalty-building initiatives.
- **Re-activate At Risk – Cannot Lose Them**: Prioritize limited-time offers, personalized re-engagement campaigns, and direct feedback collection to address churn drivers.
- **De-prioritize Hibernating and About to Sleep**: Rescue only if resources allow, not a priority focus.
- **Do not reinvest in Lost and Customers Needing Attention**: Halt remarketing budgets; only consider a final one-time win-back or referral campaign.
- **Optimize discount allocation**: Focus discounts on segments requiring reactivation rather than applying them broadly.
