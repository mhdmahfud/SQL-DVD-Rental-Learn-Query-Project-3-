# SQL-DVD-Rental-Learn-Query-Project-3

This project aims to master SQL queries from basic to advanced using the **DVD Rental** case study.

## Exercise – Basic SQL & WHERE

### 1. Scenario: Casting Call
The casting team is looking for actors whose last name starts with the letter “A”.
---
Task:
Display first_name and last_name from the actor table.
``` sql
select 
	a.first_name, 
	a.last_name 
from actor a 
where a.last_name like 'A%'
```

### 2. Scenario: Modern Film Analysis
We only need data for newer films.
---
Task:
Display films released (release_year) after 2005.
```sql
select 
	f.film_id ,
	f.title ,
	f.release_year  
from film f 
where f.release_year > 2005;
```

### 3. Scenario: Account Audit
The marketing team wants to send emails only to active customers.
---
Task:
Display the ID and name of customers where active = 1.
```sql
select 
	c.customer_id,
	concat(c.first_name,' ',c.last_name) customer_name,
	c.active 
from customer c 
where c.active <= 1;
```


### 4. Scenario: Price Promotion
We want to check films with specific rental prices.
---
Task:
Find film titles with rental_rate exactly 0.99 or 2.99.
```sql
select 
	f.film_id,
	f.title,
	f.rental_rate  
from film f  
where f.rental_rate = 0.99 or f.rental_rate =2.99;
```
```sql
select 
	f.film_id ,
	f.title ,
	f.rental_rate 
from film f 
where f.rental_rate in(0.99, 2.99)	
```

### 5. Scenario: Data Validation
Cleaning address data that is incomplete.
---
Task:
Display addresses where address2 is not empty (NOT NULL).
```sql
select *
from address a 
where a.address2 is not null;
```
***

## Exercise - Aggregation & Group By

### 1. Scenario: Rating Inventory

How many films are available for each age rating?
---
Task:
Count the total number of films for each rating.
```sql
select 
	f.rating,
	count(f.film_id ) as total_film
from film f 
group by f.rating;
```

### 2. Scenario: Customer Revenue
Which customers contribute the most?
---
Task:
Calculate the total payment amount made by each customer_id.
```sql
select 
	c.customer_id ,
	concat(c.first_name , ' ', c.last_name ) as customer_name,
	sum(p.amount) as total_amount
from customer c 
inner join payment p on c.customer_id =p.customer_id 
group by c.customer_id 
order by sum(p.amount) desc ;
```
###3. Scenario: High Spenders
Filter only VIP customers.
---
Task:
Display customer_id with total payments above 120
(Use HAVING).
```sql
select 
	p.customer_id,
	sum(p.amount ) total_pembayaran
from payment p  
group by customer_id 
having sum(p.amount) > 120;
```

### 4. Scenario: Average Price
Analyze rental prices based on rating category.
---
Task:
Calculate the average rental_rate for each rating category.
```sql
select 
	f1.rating,
	avg(f1.rental_rate) avg_rentalrate
from film f1
group by  f1.rating
order by avg(f1.rental_rate) desc;
```

###5. Scenario: Staff KPI
Measure employee productivity.
---
Task:
Count how many times each staff_id processed rentals.
```sql
select  
	r.staff_id,
	count(r.rental_id ) jumlah_rental
from rental r  
group by r.staff_id ;
```

## EXERCISE - JOIN(SINGLE & MULTI TABLE)

### 1. Scenario: International Catalog (2 Tables)
---
Task:
Display film title and its language name.
```sql
select 
	f.film_id, 
	f.title,
	l."name" as language_film
from film f 
join "language" l on f.language_id = l.language_id ;
```

### 2. Scenario: Film Category (3 Tables)
Task:
Display film title (film) and category name (category).
---
Hint:
Use the bridge table.
```sql
select 
	f.film_id,
	c.category_id,
	c."name",
	f.title
from film f
inner join film_category fc on f.film_id  = fc.film_id 
inner join category c on  fc.category_id  = c.category_id ;
```


### 3. Scenario: Rental Report (4 Tables)
Task:
Display customer name, film title, and rental date.
---
Path:
Customer → Rental → Inventory → Film
```sql
select 
	concat(c.first_name , ' ', c.last_name ) as customer,
	f.title as film_titler,
	r.rental_date 
from customer c
join rental r on c.customer_id = r.customer_id 
join inventory i on i.inventory_id = r.inventory_id 
join film f  on i.film_id = f.film_id 
;
```


### 4. Scenario: Staff Location Audit (4 Tables)
Task:
Display staff name, store ID, and country name where the store is located.
---
Path:
Staff → Store → Address → City → Country
```sql
select 
	concat(s2.first_name ,' ', s2.last_name ) as staf_name,
	s.store_id ,
	c2.country 
from store s 
join staff s2  on s.store_id = s2.store_id 
join address a  on s2.address_id = a.address_id 
join city c on a.city_id = c.city_id 
join country c2 on c.country_id = c2.country_id 
;
```


5. Scenario: Actor Portfolio (5 Tables)
---
Task:
Display actor name, film title, and film category.
```sql
select 
	concat(a.first_name ,' ', a.last_name ) as actor_name,
	f.title as film_title,
	c."name" as film_category
from actor a 
join film_actor fa on a.actor_id = fa.actor_id 
join film f on fa.film_id = f.film_id 
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id ;
```

Excercide - Subquery

### 1. Scenario: Price Benchmarking
Find films priced above the market average.
---
Task:
Display films with rental_rate higher than the average rental rate of all films.
```sql
select 
	f.film_id,
	f.title,
	f.rental_rate as 
from film f  
where f.rental_rate > 
	(select avg(f2.rental_rate) as avg_rental_rate
	from film f2 ) 
; 
```

### 2. Scenario: Extreme Duration
Find the longest film.
---
Task:
Display films whose length equals the maximum length in the database.
```sql
select 
	f.film_id,
	f.title,
	f.length 
from film f 
where f.length = (select max(f2.length ) as max_length from film f2  ) ;
```

### 3. Scenario: Productive Actors
Filter experienced actors.
---
Task:
Display actor IDs who have starred in more than 3 films
(Use subquery in HAVING/filter logic).
```sql
select 
	a.actor_id,
	a.first_name ,
	a.last_name 
from actor a 
join film_actor fa  on a.actor_id  = fa.actor_id 
join  film f on fa.film_id  = f.film_id 
group by a.actor_id 
having count(a.actor_id ) > 3;
```

4. Scenario: Active Rental Customers
Cross-check customer data.
---
Task:
Display customer details whose IDs appear in the rental table
(Use IN).
```sql
select 
	*
from customer c 
where c.customer_id in (select r.customer_id  from rental r  )
; 
```

### 5. Scenario: Above-Average Spending
Analyze spending performance.
---
Task:
Display customers whose total payment is greater than the average total payment of all customers
(Use Nested Subquery).
```sql
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    (SELECT SUM(p.amount) 
     FROM payment p 
     WHERE p.customer_id = c.customer_id) as total_pembayaran
FROM 
    customer c
WHERE 
    (SELECT SUM(p.amount) 
     FROM payment p 
     WHERE p.customer_id = c.customer_id) > 
    (SELECT AVG(p2.amount) 
     FROM payment p2);
```

EXERCISE - WINDOW FUNCTIONS
### 1. Scenario: Film Ranking
---
Task:
Rank films (RANK) based on rental_rate from highest to lowest.
```sql
select
	f.film_id,
	f.title ,
	f.rental_rate ,
	rank() over (order by f.rental_rate desc ) as rental_rank
from film f ;
```

### 2. Scenario: Customer Leaderboard
---
Task:
Rank customers (DENSE_RANK) based on their total payments.
```sql
SELECT 
    f.film_id,
    f.title,
    f.rental_rate,
    DENSE_RANK() OVER (ORDER BY sum(f.rental_rate) DESC) AS rental_rank
FROM 
    film f
group by f.film_id , f.title ;
```

### 3. Scenario: Release Order
---
Task:
Create a row number (ROW_NUMBER) for each film ordered by release year.
```sql
select 
	f.film_id,
	f.title ,
	f.release_year ,
	row_number() over (order by f.release_year desc)
from film f ;
```

### 4. Scenario: Payment Comparison
---
Task:
Display transaction-level payment data, alongside the customer’s total payment
(Use SUM with PARTITION BY).
```sql
select 
	p.payment_id ,
	p.customer_id,
	p.amount,
	sum(p.amount) over (partition by customer_id) as total_pembayaran,
	rank() over(partition by p.customer_id  order by p.amount desc  ) as rank_per_customer
from payment p 
;
```

### 5. Scenario: Largest Transaction
---
Task:
Use subquery and window function to retrieve only the largest payment transaction (Rank 1) for each customer.
```
WITH ranked_payments AS (
    SELECT 
        p.payment_id,
        p.customer_id,
        p.amount,
        DENSE_RANK() OVER (PARTITION BY p.customer_id ORDER BY p.amount DESC) AS rank_transaksi
    FROM 
        payment p
)
SELECT 
    payment_id,
    customer_id,
    amount,
    rank_transaksi
FROM 
    ranked_payments
WHERE 
    rank_transaksi = 1;
```

## EXERCISE -  CTE & CLASSIFICATION
CTE(COMMN table EXPRESSIONS)

### 1. Scenario: Loyal Customers
--- 
Task:
Use a CTE to filter customers with more than 15 rental transactions.
```sql
 with customer_transaktion as(
	select 
		r.customer_id ,
		count(r.rental_id ) total_rental
	from rental r
	group by r.customer_id 
 ) 
select * 
from customer_transaktion 
where total_rental > 15;
```

### 2. Scenario: Popular Films
---
Task:
Use a CTE to count how many times each film is rented, then join it with the film table to display the title.
```sql
with RENTAL_COUNT AS(
	select 
		f.film_id, 
		f.title, 
		count(r.rental_id ) as rental_film 
	from film f  
	join inventory i on f.film_id = i.film_id  
	join rental r on i.inventory_id = r.inventory_id 
	group by f.film_id , f.title 
)
select film_id , title  ,rental_film  from RENTAL_COUNT 
```

##**CASE WHEN**

### 3. Scenario: Price Labeling
---
Task:
Create a new column “Price Category”

4 → Premium
2 – 4 → Regular
Others → Budget
```sql
select 
	p.payment_id ,
	p.amount ,
	case 
		when p.amount  > 4 then 'Premium'
		when p.amount  between 2 and 4 then ' Regular'
		else 'budget'
	end as kategori_harga
from payment p ;
```

### 4. Scenario: Customer Segmentation
---
Task:
Categorize customers based on total spending

200 → High Value
100 – 200 → Medium
Others → Low
```sql
select 
	p.customer_id ,
	sum(p.amount) total_spend,
	case
		when sum(p.amount) > 200  then 'High Vaue'
		when sum(p.amount) between 100 and 200 then 'Mediium'
		else 'Low'
	end as Segmnet
from payment p 
group by p.customer_id
order by total_spend desc;
```

----------------------------------------------------------------
