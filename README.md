# Bookstore_sql_analysis

## Create Database
``` sql
CREATE DATABASE OnlineBookstore;
```

## Create Tables
``` sql
DROP TABLE IF EXISTS Books;
CREATE TABLE Books (
    Book_ID SERIAL PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    Published_Year INT,
    Price NUMERIC(10, 2),
    Stock INT
);
DROP TABLE IF EXISTS customers;
CREATE TABLE Customers (
    Customer_ID SERIAL PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100),
    Phone VARCHAR(15),
    City VARCHAR(50),
    Country VARCHAR(150)
);
DROP TABLE IF EXISTS orders;
CREATE TABLE Orders (
    Order_ID SERIAL PRIMARY KEY,
    Customer_ID INT REFERENCES Customers(Customer_ID),
    Book_ID INT REFERENCES Books(Book_ID),
    Order_Date DATE,
    Quantity INT,
    Total_Amount NUMERIC(10, 2)
);
``` 
## 1) Retrieve all books in the "Fiction" genre:
```sql
 1) Retrieve all books in the "Fiction" genre:

select *
from books
where genre = "Fiction"
;
```

## 2) Find books published after the year 1950:
```sql 
select *
from books
where Published_Year > 1950
order by Published_Year
; 
```
## 3) List all customers from the Canada:
```sql
select * 
from customers
where Country = 'Canada'
;
```

## 4) Show orders placed in November 2023:
```sql
select * 
from orders
where month(Order_Date) = 11
order by Order_Date
;
```
## 5) Retrieve the total stock of books available:
```sql
select
 title, Book_ID ,stock,
 sum(Stock) over  
 ( order by Book_ID
rows between unbounded preceding and current row
) as Running_stock
from books
;
```

## 6) Find the details of the most expensive book:
```sql
select *
from books
where Price = (select max(Price) from books)
;
```

## 7) Show all customers who ordered more than 1 quantity of a book:
```sql
select *
from orders
where Quantity > 1
order by Quantity
;
```

## 8) Retrieve all orders where the total amount exceeds $20:
```sql
select *
from orders
where Total_Amount > 20
order by Total_Amount
;
```


## 9) List all genres available in the Books table:
```sql
select distinct Genre
from books
;
```
## 10) Find the book with the lowest stock:
```sql
select * 
from books
where Stock = (select min(Stock) from books)
;
```

## 11) Calculate the total revenue generated from all orders:
```sql
select *,
sum(Total_Amount)
over
( order by Order_ID
rows between unbounded preceding and current row
)  as total
from orders
;
select *
from books
where genre = "Fiction"
;
```

## 12) Retrieve the total number of books sold for each genre:
```sql
with book_sales as 
(select 
books.Genre,
orders.Quantity
from orders
join books on books.Book_ID = orders.Book_ID
) 
select genre,
sum(Quantity) as total_sale
from book_sales
group by genre 
order by total_sale desc
;
```

## 13) Find the average price of books in the "Fantasy" genre:
```sql
select avg(Price) as avg_price
from books
where genre = "Fantasy"
;
```

## 14) List customers who have placed at least 2 orders:
```sql
with custom as (
select c.Customer_ID,c.Name
from orders o
join customers c on c.Customer_ID = o.Customer_ID
)
select 
Name,Customer_ID
from custom
group by Customer_ID,Name
having count(*) >= 2
;
```

## 15) Find the most frequently ordered book:
```sql
select b.Book_ID,b.Title, o.Quantity
from orders o
join books as b on b.Book_ID = o.Book_ID
order by Quantity desc
limit 1
;
```

## 16) Show the top 3 most expensive books of 'Fantasy' Genre :
```sql
select * from books
where Genre = "Fantasy"
order by Price desc
limit 3
;
```

## 17) Retrieve the total quantity of books sold by each author:
```sql
select Author,Title,sum(orders.Quantity) as total_Sold
 from books
 join orders on orders.Book_ID = books.Book_ID
 group by Author,Title
 order by total_sold desc
 ;
```
 ## 18) List the cities where customers who spent over $30 are located:
```sql
select distinct City, avg(Total_Amount) as averag
from customers
join orders on orders.Customer_ID = customers.Customer_ID
where Total_Amount >= 30
group by City
order by averag asc
;
```

## 19) Find the customer who spent the most on orders:
```sql
select Name,sum(orders.Total_Amount) as sum
from customers
join orders on orders.Customer_ID = customers.Customer_ID
group by Name
order by sum desc
limit 1
;
```

## 20) Calculate the stock remaining after fulfilling all orders:
```sql
select * from orders;

select * from books
;
select sum(Quantity)
from orders
;
select sum(stock)
from books
;
select b.Book_ID, b.Title,sum(Stock) as Stock, sum(Quantity) as Quantity ,(sum(Stock)-sum(Quantity)) left_stock
from orders o
join books as b on b.Book_ID = o.Book_ID
group by b.Book_ID,b.Title
order by b.Book_ID
;
```


## Conclusion
This analysis provides a comprehensive view of stock,orders and customers of the book store and can help inform content strategy and decision-making.
