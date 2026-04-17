# 🍔 Swiggy Data Analysis — SQL Case Study

A end-to-end SQL case study analyzing restaurant data scraped from [Swiggy](https://www.swiggy.com/) — one of India's largest food delivery platforms. This project covers 14 business questions answered using MySQL, ranging from basic aggregations to subqueries and percentage analysis.

---

## 📁 Repository Structure

```
swiggy-data-analysis/
│
├── data/
│   ├── swiggy_data.csv          # Primary dataset (1000 rows, menu-level data)
│   └── swiggy.csv               # Secondary dataset (8680 rows, restaurant-level data)
│
├── Swiggy_data_analysis.sql     # All SQL queries with comments
├── swiggy_sql_case_study.pdf    # Original case study question document
└── README.md                    # Project documentation (this file)
```

---

## 🗃️ Dataset Overview

### `swiggy_data.csv` — Menu-Level Data (1,000 rows)
| Column | Description |
|---|---|
| `restaurant_no` | Unique restaurant ID |
| `restaurant_name` | Name of the restaurant |
| `city` | City where restaurant is located |
| `address` | Full address |
| `rating` | Restaurant rating (out of 5) |
| `cost_per_person` | Average cost per person (₹) |
| `cuisine` | Cuisine types offered |
| `restaurant_link` | Swiggy URL |
| `menu_category` | Menu section (e.g., Main Course, Recommended) |
| `item` | Dish name |
| `price` | Price of the item (₹) |
| `veg_or_nonveg` | Veg / Non-Veg classification |

### `swiggy.csv` — Restaurant-Level Data (8,680 rows)
| Column | Description |
|---|---|
| `ID` | Unique restaurant ID |
| `Area` | Area/locality |
| `City` | City |
| `Restaurant` | Restaurant name |
| `Price` | Average price |
| `Avg ratings` | Average rating |
| `Total ratings` | Number of ratings |
| `Food type` | Cuisine types |
| `Address` | Address |
| `Delivery time` | Estimated delivery time (minutes) |

---

## 🛠️ Setup Instructions

### Prerequisites
- MySQL Server 8.0+
- MySQL Workbench (or any SQL client)

### Steps

1. **Clone this repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/swiggy-data-analysis.git
   cd swiggy-data-analysis
   ```

2. **Create the database and table in MySQL**
   ```sql
   CREATE DATABASE SWIGGY1;
   USE SWIGGY1;

   CREATE TABLE SWIGGYNEW (
     restaurant_no    INT,
     restaurant_name  VARCHAR(200),
     city             VARCHAR(100),
     address          VARCHAR(300),
     rating           FLOAT,
     cost_per_person  INT,
     cuisine          VARCHAR(200),
     restaurant_link  VARCHAR(300),
     menu_category    VARCHAR(100),
     item             VARCHAR(200),
     price            INT,
     veg_or_nonveg    VARCHAR(20)
   );
   ```

3. **Import the CSV data**
   ```sql
   LOAD DATA INFILE '/path/to/swiggy_data.csv'
   INTO TABLE SWIGGYNEW
   FIELDS TERMINATED BY ','
   ENCLOSED BY '"'
   LINES TERMINATED BY '\n'
   IGNORE 1 ROWS;
   ```

4. **Run the analysis**
   Open and execute `Swiggy_data_analysis.sql` in your SQL client.

---

## 📊 Case Study Questions & SQL Solutions

---

### Q1. How many restaurants have a rating greater than 4.5?

```sql
SELECT COUNT(*)
FROM SWIGGYNEW
WHERE RATING > 4.5;
```

**Insight:** Filters restaurants using a simple `WHERE` clause on the rating column to find top-rated establishments.

---

### Q2. Which is the top 1 city with the highest number of restaurants?

```sql
SELECT CITY, COUNT(restaurant_name) AS restaurants
FROM SWIGGYNEW
GROUP BY CITY
ORDER BY restaurants DESC
LIMIT 1;
```

**Insight:** Groups data by city and ranks by restaurant count. Useful for identifying the most competitive market.

---

### Q3. How many restaurants have the word "Pizza" in their name?

```sql
SELECT COUNT(RESTAURANT_NAME)
FROM SWIGGYNEW
WHERE RESTAURANT_NAME LIKE '%PIZZA%';
```

**Insight:** Uses `LIKE` with wildcards to perform pattern matching on restaurant names.

---

### Q4. What is the most common cuisine among the restaurants in the dataset?

```sql
SELECT cuisine, COUNT(*)
FROM SWIGGYNEW
GROUP BY CUISINE
ORDER BY COUNT(*) DESC
LIMIT 1;
```

**Insight:** Aggregates cuisines and returns the single most frequent one — useful for understanding platform-wide food trends.

---

### Q5. What is the average rating of restaurants in each city?

```sql
SELECT CITY, AVG(RATING)
FROM SWIGGYNEW
GROUP BY CITY;
```

**Insight:** A grouped aggregation to compare customer satisfaction across different cities.

---

### Q6. What is the highest price of an item under the 'Recommended' menu category for each restaurant?

```sql
SELECT RESTAURANT_NAME, MENU_CATEGORY, MAX(PRICE)
FROM SWIGGYNEW
WHERE menu_category = 'RECOMMENDED'
GROUP BY RESTAURANT_NAME;
```

**Insight:** Filters to a specific menu category, then uses `MAX()` to find the premium item in each restaurant's recommended section.

---

### Q7. Find the top 5 most expensive restaurants that offer cuisine other than Indian.

```sql
SELECT DISTINCT RESTAURANT_NAME, COST_PER_PERSON
FROM SWIGGYNEW
WHERE cuisine NOT LIKE '%INDIAN%'
ORDER BY COST_PER_PERSON DESC
LIMIT 5;
```

**Insight:** Combines `NOT LIKE` filtering with `ORDER BY` and `LIMIT` to identify the priciest non-Indian dining options.

---

### Q8. Find restaurants with an average cost higher than the overall average cost of all restaurants.

```sql
SELECT restaurant_name, AVG(cost_per_person)
FROM swiggynew
GROUP BY restaurant_name
HAVING AVG(cost_per_person) > (SELECT AVG(cost_per_person) FROM swiggynew);
```

**Insight:** Uses a correlated subquery in the `HAVING` clause to compare each restaurant's average cost to the dataset-wide average.

> 📝 **Finding:** No restaurant in this dataset has an average cost higher than the overall average — suggesting prices are uniformly distributed.

---

### Q9. Retrieve details of restaurants that have the same name but are located in different cities.

```sql
SELECT a.restaurant_name, a.city, b.city
FROM swiggynew a
JOIN swiggynew b
  ON a.restaurant_name = b.restaurant_name
 AND a.city <> b.city;
```

**Insight:** A self-join on the same table to identify chain restaurants operating across multiple cities.

---

### Q10. Which restaurant offers the most number of items in the 'Main Course' category?

```sql
SELECT restaurant_name, COUNT(item)
FROM swiggynew
WHERE menu_category = 'Main Course'
GROUP BY restaurant_name
ORDER BY COUNT(item) DESC
LIMIT 1;
```

**Insight:** Filters by menu category, then counts items per restaurant to find the one with the widest main course selection.

---

### Q11. List the names of restaurants that are 100% vegetarian (alphabetical order).

```sql
SELECT restaurant_name
FROM swiggynew
GROUP BY restaurant_name
HAVING SUM(veg_or_nonveg = 'non-veg') = 0
ORDER BY restaurant_name ASC;
```

**Insight:** Uses a `HAVING` clause with a conditional `SUM` — restaurants with zero non-veg items are purely vegetarian.

---

### Q12. Which restaurant provides the lowest average price for all items?

```sql
SELECT restaurant_name, AVG(price)
FROM swiggynew
GROUP BY restaurant_name
ORDER BY AVG(price)
LIMIT 1;
```

**Insight:** Ranks restaurants by their mean item price to find the most budget-friendly option.

---

### Q13. Which top 5 restaurants offer the highest number of menu categories?

```sql
SELECT DISTINCT restaurant_name, COUNT(DISTINCT menu_category) AS category_count
FROM swiggynew
GROUP BY restaurant_name
ORDER BY category_count DESC
LIMIT 5;
```

**Insight:** Counts distinct menu categories per restaurant — a proxy for menu diversity and range.

---

### Q14. Which restaurant provides the highest percentage of non-vegetarian food?

```sql
SELECT
  restaurant_name,
  ROUND(
    SUM(veg_or_nonveg = 'non-veg') * 100.0 / COUNT(*), 2
  ) AS non_veg_percentage
FROM swiggynew
GROUP BY restaurant_name
ORDER BY non_veg_percentage DESC
LIMIT 1;
```

**Insight:** Calculates a per-restaurant non-veg ratio using conditional aggregation — more accurate than dataset-level percentage calculations.

> 📝 **Finding (original query note):** The original subquery approach returned the same percentage for all restaurants since it computed a global ratio. The corrected version above calculates it at the restaurant level.

---

## 🔍 Key Findings Summary

| # | Question | Finding |
|---|---|---|
| 1 | Restaurants rated > 4.5 | Counted via simple filter |
| 2 | City with most restaurants | Bangalore leads |
| 3 | Restaurants with "Pizza" in name | Identified via LIKE |
| 4 | Most common cuisine | North Indian dominates |
| 5 | Avg rating by city | City-wise breakdown available |
| 6 | Highest recommended item price | Per restaurant |
| 7 | Top 5 priciest non-Indian | Identified |
| 8 | Restaurants above avg cost | None — prices are uniform |
| 9 | Same-name, different-city chains | Identified via self-join |
| 10 | Most main course items | Single restaurant leads |
| 11 | 100% veg restaurants | Listed alphabetically |
| 12 | Lowest avg price restaurant | Most affordable identified |
| 13 | Top 5 by menu variety | Widest category range found |
| 14 | Highest non-veg % restaurant | Identified with corrected query |

---

## 🧰 Tech Stack

- **Database:** MySQL 8.0
- **Language:** SQL
- **Tools:** MySQL Workbench
- **Data Source:** Swiggy (web-scraped dataset)

---

## 👤 Author

**Your Name**
- GitHub: [@YOUR_USERNAME](https://github.com/YOUR_USERNAME)
- LinkedIn: [Your LinkedIn](https://linkedin.com/in/YOUR_PROFILE)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
