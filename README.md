# 🍕 Pizza Sales Performance & Customer Analytics Dashboard

## 📊 Interactive Dashboard
> 🔗 **Explore the Live Dashboard:** You can visit the [Pizza Sales Analytics Dashboard on Tableau Public](https://public.tableau.com/views/PizzaSalesAnalyticsSalesProductBasketAnalysis/PizzaSalesOverview?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) to explore the live interactive dashboard.

---

## 📌 Project Overview
This project was developed using **1 year of operational Point of Sale (POS) transaction data** from a pizza restaurant to analyze sales performance, customer purchasing habits, and product mix.

Consisting of **3 interactive dashboard pages**, this study provides deep insights into financial performance summaries, menu positioning matrices, and market basket analysis to guide restaurant management in making data-driven decisions.

---

## 📑 Data Architecture & Schema

The 4 different `.csv` datasets used in the project were joined in Tableau to form a **Relational Data Model**. The dataset consists of 12 main columns obtained by joining 4 relational files:

### 1. Table and Column Structure (Data Dictionary)

* **`orders.csv`** *(Contains high-level timestamp and ID information for orders)*
  * `order_id`: Unique identifier assigned to each order **(Primary Key)**.
  * `date`: Date the order was placed (`YYYY-MM-DD`).
  * `time`: Time the order was placed (`HH:MM:SS`).

* **`order_details.csv`** *(Contains line-item details and quantities for each order)*
  * `order_details_id`: Unique ID for each pizza item within an order **(Primary Key)**.
  * `order_id`: Foreign key linking the line item to the main order **(Foreign Key -> orders.order_id)**.
  * `pizza_id`: Key identifying the specific pizza variation ordered **(Foreign Key -> pizzas.pizza_id)**.
  * `quantity`: Quantity ordered for each pizza of the same type and size.

* **`pizzas.csv`** *(Contains pizza size and price definitions)*
  * `pizza_id`: Unique identifier for each pizza type and size combination **(Primary Key)**.
  * `pizza_type_id`: Foreign key linking each pizza to its broader type **(Foreign Key -> pizza_types.pizza_type_id)**.
  * `size`: Size of the pizza (`S`, `M`, `L`, `XL`, `XXL`).
  * `price`: Unit price of the pizza in USD.

* **`pizza_types.csv`** *(Contains menu details including name, category, and ingredients)*
  * `pizza_type_id`: Unique identifier for each pizza type **(Primary Key)**.
  * `name`: Full name of the pizza as shown on the menu.
  * `category`: Menu category (`Classic`, `Chicken`, `Supreme`, `Veggie`).
  * `ingredients`: Comma-delimited list of ingredients.

---

### 2. Data Relationships & Join Logic

The relationships and cardinality structures between tables in Tableau / SQL are structured as follows:

orders (1) ───────< (N) order_details : Joined on orders.order_id = order_details.order_id
order_details (N) >─────── (1) pizzas : Joined on order_details.pizza_id = pizzas.pizza_id
pizzas (N) >─────── (1) pizza_types   : Joined on pizzas.pizza_type_id = pizza_types.pizza_type_id

---

## 🎯 Key Metrics & Executive Summary

| Total Revenue | Total Orders | Total Pizzas Sold |
| :---: | :---: | :---: |
| **USD 1,063,618** | **13,149** | **64,979** |

* **Lunch Peak:** The revenue surge between **12:00 - 13:00** is driven by corporate group orders with high basket depth (**11.1 pizzas/order**).
* **Flagship Products:** *The Barbecue Chicken* and *The California Chicken* are the absolute leaders in both quantity and revenue.
* **Complementary Star:** *Pepperoni* appears in **80% of paired orders**, serving as the primary cross-sell driver for the restaurant.

> 💡 **Key Product Dynamic (Volume vs. Revenue Leverage):**
> High sales volume across the menu does not always translate directly to high revenue. For instance, while Pepperoni ranks **9th in total units sold (2,604)**, it fails to enter the Top 10 Revenue list because its sales are concentrated in Medium and Small sizes. Conversely, *Italian Capocollo* achieves lower overall sales volume (2,219 units) but successfully enters the Top 10 Revenue list at **9th place**, leveraging higher unit prices due to over **50% of its sales occurring in Large (L) size (1,127 units)**.

---

## 🛠️ Technical Data Architecture & Modeling

### 🔗 Physical Layer Self-Join (Page 3 - Market Basket Analysis)
To identify product combinations purchased together within the same order, the `order_details` table was self-joined (**Inner Join**) at Tableau's Physical Layer level on the `Order ID` field.

* **Join Condition:** `order_details_A.order_id = order_details_B.order_id`
* **Cross-Logic Filter:** `order_details_A.pizza_name < order_details_B.pizza_name`
* **Description:** Applying this logical filter eliminated duplicate matches (e.g., *Pepperoni & Hawaiian* vs. *Hawaiian & Pepperoni*) and prevented items from matching with themselves.

### ⚙️ Calculated Fields & Dynamic Filters
* **Average Order Value (AOV):** `SUM([Revenue]) / COUNTD([Order ID])`
* **Dynamic Top N Context Filter:** To ensure the Top 10 ranking dynamically calculates popular pizzas per hour rather than remaining static, hour filters were elevated to **Context Filters** in Tableau.
* **Dynamic Quadrant Segmentation:** In the price elasticity matrix, reference lines were fixed to the vertical axis for average unit price (**USD 16.48**) and the horizontal axis for average units sold, dynamically splitting the product portfolio into 4 quadrants.

---

## 📊 Detailed Dashboard Analysis

### 📈 Page 1: Executive Overview (Performance & Trends)

<img width="1357" height="694" alt="Pizza Sales Overview" src="https://github.com/user-attachments/assets/f968a70c-4180-43ce-bd08-b72374ad8677" />


#### 📅 1. Monthly & Daily Trends
* **Volume and Revenue Peaks:** **July** represents the highest order volume with **1,935 orders**, whereas **November** reached the annual revenue peak at **USD 96,283**. October was the lowest-performing month (USD 83,547 / 1,646 orders).
* **Daily Volume & AOV Paradox:** **Friday** is the busiest day of the week with 3,538 orders but records the lowest Average Order Value at **USD 47.18 AOV**. Conversely, **Tuesday** generates the highest average basket value at **USD 52.17 AOV** despite lower order counts.

#### ⏰ 2. Hourly Volume & Revenue Anomaly (Basket Depth Analysis)

| Time Slot | Total Orders | Total Pizzas Sold | Basket Depth | Total Revenue |
| :--- | :---: | :---: | :---: | :---: |
| **12:00 (Lunch Shift)** | 1,327 | 14,742 | **11.1 Pizzas / Order** | **USD 241,663** |
| **18:00 (Dinner Shift)** | 1,705 | 4,665 | **2.7 Pizzas / Order** | **USD 75,860** |

* **Lunch Rush (12:00 - 13:00):** 1,327 individual orders resulted in 14,742 pizzas sold. The high basket depth of **11.1 pizzas per order** proves this window is heavily driven by corporate and department-wide group orders.
* **After-Work Rush (17:00 - 18:00):** Although transaction traffic rises to 1,705 orders, basket depth drops to **2.7 pizzas** (individual/family purchases), keeping total volume at 4,665 pizzas and revenue at USD 75,860.
* **Product Mix Impact:** While basket depth accounts for the majority of the **3.18x revenue gap**, the shift toward higher-priced premium pizzas (*The Four Cheese Pizza* ranking 3rd in revenue) at lunch versus standard classic pizzas (*Pepperoni, Hawaiian*) at dinner serves as a secondary revenue driver.

---

### 🍕 Page 2: Product Performance (Menu Positioning)

<img width="1356" height="696" alt="Product Performance" src="https://github.com/user-attachments/assets/b77efd5c-7f10-4723-a0cc-c2152bced1e4" />


#### 🎯 1. Price vs. Volume Matrix
* **Star / Flagships (High Price & High Volume):** *The Barbecue Chicken* (USD 16.75 / 6,198 units) and *The California Chicken* (USD 16.75 / 5,285 units) lead the menu as high-margin, high-volume star performers.
* **Volume Drivers (Low Price & High Volume):** *Pepperoni* and *Classic Deluxe* sit to the left of the average price line, driving core customer traffic.
* **Gourmet / Niche Products (High Price & Low Volume):** *The Brie Carre* (USD 23.65) and *The Greek Pizza* (USD 21.00 / 162 units) exhibit high price with low volume, making them primary candidates for menu optimization (recipe or pricing revision).

---

### 🛒 Page 3: Market Basket Analysis (Co-Occurrence)

<img width="1362" height="697" alt="Basket Analysis" src="https://github.com/user-attachments/assets/454c2353-bc2b-4145-a766-172d2673b182" />


#### 🤝 1. Co-Occurrence Matrix
* **Top Pair:** *Hawaiian & Thai Chicken* were ordered together **270 times**, generating **USD 4,436.69** in revenue.
* **Pepperoni's Cross-Sell Power:** Pepperoni appears in **4 out of the top 5 paired orders** (with BBQ Chicken, Hawaiian, Thai Chicken, and Cali Chicken), solidifying its role as the core complementary product.

#### 📦 2. Basket Size Distribution
* **4+ Pizza Dominance:** Orders containing **4+ pizzas** lead with **12,003 transactions** overall (Single orders: 6,615).
* While 4+ pizza orders dominate both 12:00 and 18:00 peak hours, the massive portion depth during lunch (**11.1 pizzas/order**) remains the primary engine of total revenue.

---

## 💡 Strategic Business Recommendations

1. 💼 **Corporate Lunch Packages (12:00 - 13:00):** Introduce tailored *"Corporate / Catering Department Bundles"* for the lunch hour to capitalize on high basket depth (11.1 pizzas/order) and lock in high-revenue orders.
2. 🌙 **Evening Up-Selling Campaigns (17:00 - 18:00):** To convert high dinner transaction traffic (1,705 orders) into higher revenue, deploy *"50% Off Second Pizza"* or side-add-on promotions to expand basket depth beyond 2.7 pizzas.
3. 🍕 **Strategic Menu Bundling:** Pair *Pepperoni* as a discounted add-on alongside flagship items (BBQ / Cali Chicken), and re-evaluate pricing or recipe composition for low-volume niche items like *The Greek Pizza*.
