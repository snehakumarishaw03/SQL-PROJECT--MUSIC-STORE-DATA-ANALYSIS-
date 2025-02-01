# SQL-PROJECT--MUSIC-STORE-DATA-ANALYSIS-
SQL PROJECT- MUSIC STORE DATA ANALYSIS 
/* Who is the senior most employee based on job title? */
SELECT first_name, last_name, title 
FROM employee 
ORDER BY title DESC 
LIMIT 1;

/* Which countries have the most Invoices? */
SELECT billing_country, COUNT(*) AS NumberOfInvoices 
FROM invoice 
GROUP BY billing_country 
ORDER BY NumberOfInvoices DESC;
/* What are the top 3 values of total invoices? */
SELECT total 
FROM invoice 
ORDER BY total DESC 
LIMIT 3;
/* Which city has the best customers? */
SELECT billing_city, SUM(total) AS TotalRevenue 
FROM invoice 
GROUP BY billing_city 
ORDER BY TotalRevenue DESC 
LIMIT 1;
/* Who is the best customer? */
SELECT customer.first_name, customer.last_name, SUM(invoice.total) AS TotalSpent 
FROM customer 
JOIN invoice ON customer.customer_id = invoice.customer_id 
GROUP BY customer.first_name, customer.last_name 
ORDER BY TotalSpent DESC 
LIMIT 1;
/*Return the email, first name, last name, & Genre of all Rock Music listeners. */
SELECT customer.email, customer.first_name, customer.last_name, genre.name AS Genre
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id
JOIN genre ON track.genre_id = genre.genre_id
WHERE genre.name = 'Rock'
ORDER BY customer.email;

/*Invite the artists who have written the most rock music.*/
SELECT artist.name, COUNT(track.track_id) AS TrackCount
FROM artist
JOIN album2 ON artist.artist_id = album2.artist_id
JOIN track ON album2.album_id = track.album_id
JOIN genre ON track.genre_id = genre.genre_id
WHERE genre.name = 'Rock'
GROUP BY artist.name
ORDER BY TrackCount DESC
LIMIT 10;

/* Return all track names that have a song length longer than the average song length. */
SELECT name, milliseconds 
FROM track 
WHERE milliseconds > (SELECT AVG(milliseconds) FROM track) 
ORDER BY milliseconds DESC;

/* Question Set 3 – Advance
Find how much each customer spent on artists */
SELECT customer.first_name, customer.last_name, artist.name, SUM(invoice_line.unit_price * invoice_line.quantity) AS TotalSpent
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id
JOIN album2 ON track.album_id = album2.album_id
JOIN artist ON album2.artist_id = artist.artist_id
GROUP BY customer.first_name, customer.last_name, artist.name
ORDER BY TotalSpent DESC;

/* Find the most popular music Genre for each country. */
SELECT billing_country, genre.name AS Genre, COUNT(invoice_line.track_id) AS PurchaseCount
FROM invoice
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id
JOIN genre ON track.genre_id = genre.genre_id
GROUP BY billing_country, genre.name
HAVING COUNT(invoice_line.track_id) = (SELECT MAX(PurchaseCount) FROM 
    (SELECT billing_country, COUNT(invoice_line.track_id) AS PurchaseCount
    FROM invoice
    JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
    JOIN track ON invoice_line.track_id = track.track_id
    JOIN genre ON track.genre_id = genre.genre_id
    GROUP BY billing_country, genre.name) AS CountryPurchaseCounts); 
    
 /* Determine the customer that has spent the most on music for each country.*/
 
SELECT billing_country, customer.first_name, customer.last_name, SUM(invoice.total) AS TotalSpent
FROM invoice
JOIN customer ON invoice.customer_id = customer.customer_id
GROUP BY billing_country, customer.first_name, customer.last_name
HAVING SUM(invoice.total) = (SELECT MAX(TotalSpent) FROM 
    (SELECT billing_country, customer.customer_id, SUM(invoice.total) AS TotalSpent
    FROM invoice
    JOIN customer ON invoice.customer_id = customer.customer_id
    GROUP BY billing_country, customer.customer_id) AS CountryCustomerSpending);




