## Music Store Data Analysis Project

Welcome to the **Music Store Data Analysis** project! This project explores a fictional music store database using SQL to extract insights related to customers, invoices, songs, and sales performance. It is designed as a portfolio project suitable for beginners and intermediate SQL learners, especially freshers preparing for data analyst or business intelligence roles.

 **🌐Project Overview**

This analysis uses SQL to answer real-world business questions using a music store database. The goal is to demonstrate proficiency in SQL by writing optimized, insightful queries and translating data into meaningful information.

**Database Tables Used:**

- customer
- invoice
- invoice_line
- track
- album2
- artist
- genre
- employee


#SQL PROJECT- MUSIC STORE DATA ANALYSIS

## Question Set 1 - Easy

**1. Who is the senior most employee based on job title?**

select * from employee

order by title

limit 1;

**2. Which countries have the most Invoices?**

select billing_country, count(invoice_id) as invoices

from invoice

group by billing_country

order by invoices desc;

**3. What are top 3 values of total invoice?**

select total

from invoice

order by total desc

limit 3;

**4. Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. 
Write a query that returns one city that has the highest sum of invoice totals.Return both the city name & sum of all invoice totals**

SELECT billing_city, CONCAT('$', FORMAT(SUM(total), 2)) AS invoice_total

FROM invoice

GROUP BY billing_city

ORDER BY SUM(total) DESC

LIMIT 1;

**5. Who is the best customer? The customer who has spent the most money will be declared the best customer. Write a query that returns the person who has spent the most money**

select i.customer_id, c.first_name, c.last_name, round(sum(i.total),2) as spent

from invoice i

join customer c on i.customer_id= c.customer_id

group by 1,2,3

order by 4 desc

limit 1;

## Question Set 2 – Moderate

**1. Write query to return the email, first name, last name, & Genre of all Rock Music listeners. Return your list ordered alphabetically by email starting with A**

select c.email, c.first_name, c.last_name, g.name

from customer c

join invoice i on c.customer_id= i.customer_id

join invoice_line il on i.invoice_id= il.invoice_id

join track t on il.track_id= t.track_id

join genre g on t.genre_id= g.genre_id

where g.name= "Rock"

order by 1 asc;

**2. Let's invite the artists who have written the most rock music in our dataset. Write a query that returns the Artist name and total track count of the top 10 rock bands**

select a.name, count(t.track_id) as total

from artist a

join album2 ab on a.artist_id= ab.artist_id

join track t on ab.album_id= t.album_id

join genre g on t.genre_id= g.genre_id

where g.name= "Rock"

group by 1

order by 2 desc

limit 10;

**3. Return all the track names that have a song length longer than the average song length. Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first**

select t.name, t.milliseconds

from track t

where t.milliseconds >= (select round(avg(milliseconds),2) from track)

order by 2 desc;

## Question Set 3 – Advance

**1. Find how much amount spent by each customer on artists? Write a query to return customer name, artist name and total spent**

select c.customer_id, concat(c.first_name," ", c.last_name) as Cx_name, a.name as artist ,round(sum(il.unit_price* quantity),2) as spent

from customer c

join invoice i on c.customer_id= i.customer_id

join invoice_line il on i.invoice_id= il.invoice_id

join track t on il.track_id= t.track_id

join album2 ab on t.album_id= ab.album_id

join artist a on ab.artist_id= a.artist_id

group by 1,2,3

order by 1, 4 desc;


**2. We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre with the highest amount of purchases.Write a query that returns each country along with the top Genre. For countries where the maximum number of purchases is shared return all Genres**

with popular_music as(

select i.billing_country as country, g.name as genre, count(il.invoice_line_id) as count , rank() over(partition by i.billing_country order by count(il.invoice_line_id)desc) as rnk

from invoice i 

join invoice_line il on i.invoice_id= il.invoice_id

join track t on il.track_id= t.track_id

join genre g on t.genre_id= g.genre_id

group by 1,2)

select country, genre, count

from popular_music

where rnk=1

order by 1;

**3. Write a query that determines the customer that has spent the most on music for each country. Write a query that returns the country along with the top customer and how much they spent. For countries where the top amount spent is shared, provide all customers who spent this amount**

with best_customer as( select c.country as country, concat(c.first_name," ", c.last_name) as name, round(sum(i.total),2) as spent,

rank() over(partition by c.country order by sum(i.total) desc) as rnk

from customer c

join invoice i on c.customer_id= i.customer_id

group by 1,2)

select country, name, spent

from best_customer

where rnk =1

order by country;
