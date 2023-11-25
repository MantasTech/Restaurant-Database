# 🍕 Data Analysis Project for a Pizza Restaurant

## Introduction

In this project, I developed a database for a fictional pizza restaurant. The aim was to enhance understanding and improve operational aspects such as order processing, inventory management, and staff scheduling. I used SQL for detailed data work and Looker Studio for clear, visual reports. This project is all about using data smartly to make the restaurant run better.
## Project Overview

- **Data Modeling**: Designed a relational database model using QuickDBD.
- **Data Manipulation**: Wrote SQL queries for in-depth analysis and exploration.
- **Data Visualization**: Took advantage of Looker Studio and made interactive dashboard.

## 🔗 Data Modeling

I started by designing a data model using QuickDBD to make a data tables and clearly represent the relationships between different data elements.

 **Data Model Diagram**: 
   ![Entity Relationship Diagram](https://github.com/MantasTech/Restaurant-Database/blob/main/images/Schema.png)

## ✏️ Data Manipulation

To begin querying the data, first I had to import the data model and CSV files into MySQL.

![Entity Relationship Diagram](https://github.com/MantasTech/Restaurant-Database/blob/main/images/tables.png)

### 1.

For the first dashboard i extracted the data related to orders, items, and delivery.

```sql
-- Query for orders dashboard
SELECT
    o.order_id,
    i.item_price,
    o.quantity,
    i.item_cat,
    i.item_name,
    o.created_at,
    a.delivery_address1,
    a.delivery_address2,
    a.delivery_city,
    a.delivery_zipcode,
    o.delivery 
FROM
    orders o
    LEFT JOIN item i ON o.item_id = i.item_id
    LEFT JOIN address a ON o.add_id = a.add_id
```

![ ](https://github.com/MantasTech/Restaurant-Database/blob/main/images/orders.png)

### 2.

For the second dashboard i had to get the data that would show the stock and assist with inventory management.

```sql
-- Query for inventory dashboard nr1
SELECT
		s1.item_name,
		s1.ing_id,
		s1.ing_name,
		s1.ing_weight,
		s1.ing_price,
		s1.order_quantity,
		s1.recipe_quantity,
		s1.order_quantity*s1.recipe_quantity as ordered_weight,
		s1.ing_price/s1.ing_weight as unit_cost,
		(s1.order_quantity*s1.recipe_quantity)*(s1.ing_price/s1.ing_weight) as ingredient_cost

FROM 
(SELECT
	o.item_id,
	i.sku,
	i.item_name,
	r.ing_id,
	ing.ing_name,
	r.quantity AS recipe_quantity,
	sum(o.quantity) AS order_quantity,
	ing.ing_weight,
	ing.ing_price
FROM 
	orders o
	LEFT JOIN item i ON o.item_id=i.item_id
	LEFT JOIN recipe r ON i.sku = r.recipe_id
	LEFT JOIN ingredient ing ON ing.ing_id = r.ing_id
GROUP BY 
	o.item_id,
	i.sku,
	i.item_name,
	r.ing_id,
	r.quantity,
	ing.ing_name,
	ing.ing_weight,
	ing.ing_price) s1
```
![](https://github.com/MantasTech/Restaurant-Database/blob/main/images/stock1.png)

//
```sql
-- Query for inventory dashboard nr2
SELECT
	s2.ing_name,
	s2.ordered_weight,
	ing.ing_weight*inv.quantity as total_inv_weight,
	(ing.ing_weight * inv.quantity)-s2.ordered_weight as remaining_weight

FROM 
(SELECT
	ing_id,
	ing_name,
	sum(ordered_weight) as ordered_weight
FROM
	stock1
	GROUP BY ing_name,ing_id) s2
	
LEFT JOIN inventory inv ON inv.item_id = s2.ing_id
LEFT JOIN ingredient ing ON ing.ing_id = s2.ing_id
```
![](https://github.com/MantasTech/Restaurant-Database/blob/main/images/stock2.png)

### 3.
Final dashboard will be focused on staff related insights.

```sql
-- Query for staff dashboard
SELECT
	r.date,
	s.first_name,
	s.last_name,
	s.hourly_rate,
	sh.start_time,
	sh.end_time,
	((
			HOUR (
			timediff( sh.end_time, sh.start_time ))* 60 
			)+(
			MINUTE (
			timediff( sh.end_time, sh.start_time ))))/ 60 AS hours_in_shift,
	((
			HOUR (
			timediff( sh.end_time, sh.start_time ))* 60 
			)+(
			MINUTE (
			timediff( sh.end_time, sh.start_time ))))/ 60 * s.hourly_rate AS staff_cost 
FROM
	rota r
	LEFT JOIN staff s ON r.staff_id = s.staff_id
	LEFT JOIN shift sh ON r.shift_id = sh.shift_id
```
![](https://github.com/MantasTech/Restaurant-Database/blob/main/images/staff.png)

## 📊 Data Visualization

To finalize the data insights obtained from SQL queries, I chose the Looker Studio to make an interactive dashboard and present the key metrics.

Looker Studio [link](https://lookerstudio.google.com/reporting/ccbfff85-91a8-428c-901f-695bae4161c9)

![Orders](https://github.com/MantasTech/Restaurant-Database/blob/main/images/dashboard_orders.png)

![Inventory](https://github.com/MantasTech/Restaurant-Database/blob/main/images/dasboard_inventory.png)

![Staff](https://github.com/MantasTech/Restaurant-Database/blob/main/images/dashboard_staff.png)




