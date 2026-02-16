# 📚 Bookstore Sales Data Analysis (SQL Project)

## 📌 Project Overview

This project analyzes a bookstore's sales data using SQL.  
The objective is to extract meaningful business insights such as revenue performance, customer behavior, sales trends, and inventory tracking.

The project demonstrates strong SQL fundamentals along with business-oriented analytical thinking.

---

## 🗂 Database Schema

The database consists of three relational tables:

### 1️⃣ Books
- Book_ID (Primary Key)
- Title
- Author
- Genre
- Published_Year
- Price
- Stock

### 2️⃣ Customers
- Customer_ID (Primary Key)
- Name
- Email
- Phone
- City
- Country

### 3️⃣ Orders
- Order_ID (Primary Key)
- Customer_ID (Foreign Key)
- Book_ID (Foreign Key)
- Order_Date
- Quantity
- Total_Amount

Relational integrity is maintained using foreign key constraints.

---

## 🛠 Tools & Technologies Used

- SQL (PostgreSQL)
- Relational Database Design
- Aggregate Functions
- JOIN Operations
- Data Import using COPY
- Data Filtering & Grouping

---

## 📊 Business Questions Solved



--Drop Table

DROP TABLE IF EXISTS Books;

--Create Table Books

CREATE TABLE IF NOT EXISTS Books 
(
   Book_ID	SERIAL PRIMARY KEY, 
   Title VARCHAR(100),
   Author VARCHAR(100),
   Genre VARCHAR(50),
   Published_Year INTEGER,	
   Price NUMERIC(10,2),
   Stock INTEGER
);

--Create Table Customers

CREATE TABLE IF NOT EXISTS Customers
(
	   Customer_ID	SERIAL PRIMARY KEY,
	   Name	VARCHAR(100),
	   Email VARCHAR(100),
	   Phone VARCHAR(15),	
	   City	VARCHAR(100),
	   Country VARCHAR(100)
);

--Create table Orders 

CREATE TABLE IF NOT EXISTS Orders
(
    Order_ID SERIAL PRIMARY KEY,	
	Customer_ID	INT REFERENCES Customers(Customer_ID),
	Book_ID	 INT REFERENCES Books(Book_ID),
	Order_Date	DATE,
	Quantity INT,
	Total_Amount NUMERIC(10,2)
);


--import data from Books table 

COPY Books(Book_ID,Title,Author,Genre,Published_Year,Price,Stock)
FROM 'C:\Users\User\OneDrive\Documents\SQL\Postgres SQL\Dataset\SQL Project\Books.csv'
DELIMITER ','
CSV HEADER;


--import data from Customers table

COPY Customers (Customer_ID	,Name,Email,Phone,City,Country)
FROM 'C:\Users\User\OneDrive\Documents\SQL\Postgres SQL\Dataset\SQL Project\Customers.csv'
DELIMITER ','
CSV HEADER;


--import data from Orders table 

COPY Orders(Order_ID,Customer_ID,Book_ID,Order_Date,Quantity,Total_Amount)
FROM 'C:\Users\User\OneDrive\Documents\SQL\Postgres SQL\Dataset\SQL Project\Orders.csv'
DELIMITER ','
CSV HEADER;


--retrieve data from tables

SELECT * FROM Books;
SELECT * FROM Customers;
SELECT * FROM Orders;


--1) Retrieve all books in the "Fiction" genre

SELECT * 
FROM Books
WHERE Genre = 'Fiction';


--2) Find books published after the year 1950

SELECT * 
FROM Books
WHERE Published_year >1950;


--3) List all customers from the Canada

SELECT * 
FROM Customers
WHERE Country = 'Canada';


--4) Show orders placed in November 2023

SELECT * 
FROM Orders
WHERE Order_Date BETWEEN '2023-11-01' AND '2023-11-30';


--5) Retrieve the total stock of books available

SELECT SUM(stock) AS Total_stock
FROM Books; 


--6) Find the details of the most expensive book

SELECT *
FROM Books
ORDER BY Price DESC
LIMIT 5;


--7) Show all customers who ordered more than 1 quantity of a book

SELECT *
FROM Orders
WHERE Quantity>1;


--8) Retrieve all orders where the total amount exceeds $20

SELECT *
FROM Orders 
WHERE Total_amount >20;


--9) List all genres available in the Books table

SELECT Genre
FROM Books 
GROUP By Genre;


--10) Find the book with the lowest stock

SELECT *
FROM Books
ORDER BY stock ASC
LIMIT 1;


--11) Calculate the total revenue generated from all orders

SELECT SUM(Total_Amount) AS Total_Revenue 
FROM Orders;


-- Advance Questions : 
-- 1) Retrieve the total number of books sold for each genre

SELECT b.Genre, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders o
JOIN Books b ON o.Book_ID = b.Book_ID
GROUP BY b.Genre;


-- 2) Find the average price of books in the "Fantasy" genre

SELECT AVG(Price) AS Average_Price
FROM Books
WHERE Genre = 'Fantasy';


-- 3) List customers who have placed at least 2 orders

SELECT Customer_ID, COUNT(Order_ID) AS Order_Count
FROM Orders
GROUP BY Customer_ID
HAVING COUNT(Order_ID) >= 2;


-- 4) Find the most frequently ordered book

SELECT Book_ID, COUNT(Order_ID) AS Order_Count
FROM Orders
GROUP BY Book_ID
ORDER BY Order_Count DESC LIMIT 1;


-- 5) Show the top 3 most expensive books of 'Fantasy' Genre 

SELECT * FROM Books 
WHERE Genre = 'Fantasy'
ORDER BY Price DESC LIMIT 3;



-- 6) Retrieve the total quantity of books sold by each author

SELECT b.Author,SUM(o.Quantity) AS Total_Books_Sold
FROM Orders AS o
JOIN Books AS b
ON o.Book_ID = b.Book_ID
GROUP BY b.Author;

-- 7) List the cities where customers who spent over $30 are located

SELECT DISTINCT c.City
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
WHERE o.Total_Amount > 30;

-- 8) Find the customer who spent the most on orders
SELECT c.Customer_ID, c.Name, SUM(o.Total_Amount) AS Total_Spent
FROM Orders o
JOIN Customers c ON o.Customer_ID = c.Customer_ID
GROUP BY c.Customer_ID, c.Name
ORDER BY Total_Spent DESC LIMIT 1;


--9) Calculate the stock remaining after fulfilling all orders

SELECT b.Book_ID, b.Title, b.Stock - COALESCE(SUM(o.Quantity), 0) AS Remaining_Stock
FROM Books b
LEFT JOIN Orders o ON b.Book_ID = o.Book_ID
GROUP BY b.Book_ID;

-- OR 

SELECT b.Book_ID, b.Title, b.Stock, COALESCE(SUM(o.Quantity), 0) AS order_quantity, 
		b.Stock - COALESCE(SUM(o.Quantity), 0) AS Remaining_stock
FROM Books b
LEFT JOIN Orders o ON b.Book_ID = o.Book_ID
GROUP BY b.Book_ID;
