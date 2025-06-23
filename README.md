# NORTH-WIND TRADE SALES ANALYSIS
![NORTHWIND](https://github.com/user-attachments/assets/e00644d9-1cae-44d7-9c51-b7adae9078ca)

### PROJECT OVERVIEW

The Objectives of this analysis is to show the company's performances in key areas including;  
- Sales Trend  
- Product Performance  
- Key Customers  
- Shipping Cost

### DATA SOURCE

The Dataset was downloaded from Maven Data Playground. It comprises of SEVEN different tables which include;
- Customers Table
- Order Table
- Shipping Table
- Category Table
- Order Details Table
- Product Table
- Employees Table

### TOOLS

- SQL Server - Data Cleaning and Analysis
- Microsoft PowerBI - Visualization and Report

### DATA CLEANING AND PREPARATION

In the initial phase of data preparation, the following tasks were performed;

- DATA LOADING AND INSPECTION;
  - A Preview of the Tables
 
 ```sql
select * from categories;

select * from orders;

select * from customers;

select * from employees;

select * from order_details;

select * from products;

select * from shippers;
```

  - DATA DESCRIPTION

```sql
exec sp_columns categories;
exec sp_columns order_details;
exec sp_columns products;
exec sp_columns shippers;
exec sp_columns employees;
exec sp_columns customers;
exec sp_columns orders;
```

 - NUMBER OF ROWS IN EACH TABLE

```sql
select count(*) Nos_of_Rows
from categories;

select count(*) Nos_of_Rows
from products;

select count(*) Nos_of_Rows
from employees; 

select count(*) Nos_of_Rows 
from shippers; 

select count(*) Nos_of_Rows 
from customers;

select count(*) Nos_of_Rows 
from order_details;

select count(*) Nos_of_Rows 
from orders;
```

 - NUMBER OF COLUMNS IN EACH TABLE

```sql
select count(*)columns
from information_schema.columns
where table_name = 'categories';

select count(*)columns
from information_schema.columns
where table_name = 'customers';

select count(*)columns
from information_schema.columns
where table_name = 'order_details';

select count(*)columns
from information_schema.columns
where table_name = 'shippers';

select count(*)columns
from information_schema.columns
where table_name = 'employees';

select count(*)columns
from information_schema.columns
where table_name = 'products';

select count(*)columns
from information_schema.columns
where table_name = 'orders';
```
- CHECKING FOR DUPLICATES NAMES IN CUSTOMERS TABLE

```sql
select Company_Name, count(distinct Company_Name) Count
from customers
group by Company_Name
having count(distinct Company_Name) > 1;
```

- CHECKING FOR DUPLICATES IN EMPLOYEES TABLE

```sql
select Employee_Name, count(distinct Employee_Name) Count
from employees
group by Employee_Name
having count(distinct Employee_Name) > 1;
```

- CHECKING FOR DUPLICATES IN PRODUCTS TABLE

```sql
select Product_Name, count(distinct Product_Name) Count
from products
group by Product_Name 
having count(distinct Product_Name) > 1;
```
    
- DATA CLEANING
  - RENAMING COLUMN TITLES

 ```sql
--Renaming the tables columns names--
exec sp_rename 'categories.categoryID','Category_ID','Column';
exec sp_rename 'categories.categoryName','Category_Name','Column';
exec sp_rename 'categories.description','Description','Column';
exec sp_rename 'orders.orderID','Order_ID','Column';
exec sp_rename 'orders.customerID','Customer_ID','Column';
exec sp_rename 'orders.employeeID','Employee_ID','Column';
exec sp_rename 'orders.orderDate','Order_Date','Column';
exec sp_rename 'orders.requiredDate','Required_Date','Column';
exec sp_rename 'orders.shippedDate','Shipped_Date','Column';
exec sp_rename 'orders.shipperID','Shipper_ID','Column';
exec sp_rename 'orders.freight','Freight','Column';
exec sp_rename 'customers.customerID','Customer_ID','Column';
exec sp_rename 'customers.companyName','Company_Name','Column';
exec sp_rename 'customers.contactName','Contact_Name','Column';
exec sp_rename 'customers.contactTitle','Contact_Title','Column';
exec sp_rename 'customers.city','City','Column';
exec sp_rename 'customers.country','Country','Column';
exec sp_rename 'employees.employeeID','Employee_ID','Column';
exec sp_rename 'employees.employeeName','Employee_Name','Column';
exec sp_rename 'employees.title','Title','Column';
exec sp_rename 'employees.city','City','Column';
exec sp_rename 'employees.country','Country','Column';
exec sp_rename 'employees.reportsTo','Reports_To','Column';
exec sp_rename 'order_details.orderID','Order_ID','Column';
exec sp_rename 'order_details.productID','Product_ID','Column';
exec sp_rename 'order_details.unitPrice','Unit_price','Column';
exec sp_rename 'order_details.quantity','Quantity','Column';
exec sp_rename 'order_details.discount','Discount','Column';
exec sp_rename 'products.productID','Product_ID','Column';
exec sp_rename 'products.productName','Product_Name','Column';
exec sp_rename 'products.quantityPerUnit','Quantity_Per_Unit','Column';
exec sp_rename 'products.unitPrice','Unit_Price','Column';
exec sp_rename 'products.discontinued','Discount','Column';
exec sp_rename 'products.categoryID','Category_ID','Column';
exec sp_rename 'shippers.shipperID','Shipper_ID','Column';
exec sp_rename 'shippers.companyName','Company_Name','Column';
```

## SALES INSIGHTS
- TOTAL SALES OR REVENUE GENERATED, QUANTITY SOLD, ORDER PLACED, TOTAL DISCOUNT

```sql
select sum(Revenue) as Total_Revenue, sum(Total_Quantity) as Quantity_Sold,
		sum(Total_Order) as Order_Placed, sum(Total_Discount) as Total_Discount
from(select Year(Order_Date) as Year,sum(Amount) Revenue, sum(Quantity) as Total_Quantity, 
		sum(Discount) as Total_Discount, count(Order_ID) Total_Order
	from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
		from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
			from orders O
			join order_details OD
			on O.Order_ID = OD.Order_ID) A 
		join (select * from Products) P
		on A.Product_ID = P.Product_ID) B
	group by Year(Order_Date)) C;
```
![TOTAL SALES](https://github.com/user-attachments/assets/b76a6208-fb8f-4f45-b5e6-a813f14acaff)

![TOTAL SALES_1](https://github.com/user-attachments/assets/95d138c2-4dfa-4f48-8634-c8c57edcd647)

- SALES TREND
```sql
select Year(Order_Date) as Year,sum(Amount) Revenue, sum(Quantity) as Total_Quantity, 
		sum(Discount) as Total_Discount, count(Order_ID) Total_Order
from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID) B
group by Year(Order_Date) 
order by Year asc;
```
![SALES TREND](https://github.com/user-attachments/assets/bfed429a-1820-4034-9a76-e7022ab8e756)

![SALES TREND_1](https://github.com/user-attachments/assets/8ae85028-8bf6-4740-acd0-6598e008495d)

```sql
select DateName(Month,Order_Date) as Month,sum(Amount) Revenue, sum(Quantity) as Total_Quantity, 
		sum(Discount) as Total_Discount, count(Order_ID) Total_Order
from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID) B
group by DateName(Month,Order_Date)
order by Revenue asc;
```

![BEST SELLING MONTH](https://github.com/user-attachments/assets/ef53f526-1f31-46ad-81af-525257b1d15a)


- EFFECT OF DISCOUNT ON ORDERS
```sql
select round(discount,2) as Discount, AVG(Quantity) AS average_quantity_ordered
from (select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID)B
group by discount
order by discount desc;
```
![DISCOUNT-REVENUE CORRELATION](https://github.com/user-attachments/assets/d91e3205-124e-4623-a922-72f52a7316d5)


## CUSTOMERS' SEGMENTATION
- CUSTOMERS TREND OVER TIME
```sql
select Year(Order_Date) as Year, count(distinct Customer_ID) as Customers
from(select C.Customer_ID, Company_Name, Contact_Name, Order_Date,
		Contact_Title, City, Country, Order_ID from customers c
	join orders o
	on o.Customer_ID = c.Customer_ID)A
group by Year(Order_Date);
```

- EXPANSION GROWTH OVER TIME
```sql
select Year(Order_Date) as Year, count(distinct Country) as Countries
from(select C.Customer_ID, Company_Name, Contact_Name, Order_Date,
		Contact_Title, City, Country, Order_ID from customers c
	join orders o
	on o.Customer_ID = c.Customer_ID)A
group by Year(Order_Date);
```

- TOP 10 CUSTOMERS BY REVENUE
```sql
select top 10 Company_Name, Country,sum(Quantity) as Total_Quantity,count(Order_ID) as Total_Order,
		sum(Amount) as Revenue, sum(Discount) as Discount
from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID)B
join (select * from customers) C
on B.Customer_ID = C.Customer_ID
group by Company_Name, Country
order by Revenue desc;
```

![TOP 10 CUSTOMERS BY REVENUE](https://github.com/user-attachments/assets/007eadbb-1a70-4b33-b0b2-8fc554c72988)

![CUSTOMER SEGMENTATION](https://github.com/user-attachments/assets/cf52bc63-0963-4802-8f89-697e43cf6009)

- BUSINESS COUNTRY HUBS
```sql
select top 10  Country,sum(Quantity) as Total_Quantity,count(Order_ID) as Total_Order,
		sum(Amount) as Revenue, sum(Discount) as Discount
from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID)B
join (select * from customers) C
on B.Customer_ID = C.Customer_ID
group by  Country
order by Revenue desc;
```
![BUSINESS COUNTRY HUB](https://github.com/user-attachments/assets/c598659b-5321-4c7f-b0bc-013d3382e8fa)

![REVENUE BY COUNTRY](https://github.com/user-attachments/assets/e595a700-9953-4a95-bded-7cba8cb1eaec)

![ORDER BY COUNTRY](https://github.com/user-attachments/assets/462fc1f0-3aa2-4ab8-bf34-00dfc4dc4ac7)

## PRODUCT AND CATEGORY SEGMENTATION
- BEST SELLING PRODUCTS BY REVENUE AND AMOUNT OF DISCOUNT
```sql
select Product_Name, sum(Quantity) as Quantity_Sold, count(Product_Name) as Total_Order,
		sum(Amount) as Amount, sum(Discount) as Discount
from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Quantity, Freight
	from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
on A.Product_ID = P.Product_ID)B
group by Product_Name
order by  Amount desc;
```

![BEST SELLING PRODUCT](https://github.com/user-attachments/assets/6a261368-2453-4905-8844-4a3797acd36f)

![PRODUCT SEGEMENTATION](https://github.com/user-attachments/assets/83544e4f-6112-4cf1-a613-56ed575432ef)

- TOP SELLING CATEGORY
```sql
select Category_Name, count(Category_Name) as Total_Order, sum(Quantity_Sold) as Total_Quantity, 
		round(sum(Amount),2) as Amount, round(sum(Discount),2) as Discount
from(select Product_Name, sum(Amount) as Amount, sum(Quantity) as Quantity_Sold, count(Product_Name) as Total_Order,
		sum(Discount) as Discount
	from(select Order_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Quantity, Freight
		from(select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
			from orders O
			join order_details OD
			on O.Order_ID = OD.Order_ID) A 
		join (select * from Products) P
	on A.Product_ID = P.Product_ID)B
	group by Product_Name) C
JOIN(
	select B.*, Category_Name, Description
	from(select P.Product_ID, Product_Name, Quantity_Per_Unit, 
				P.Unit_Price, Category_ID from products P
	join  order_details OD
	on OD.Product_ID = P.Product_ID)B
	join categories C 
	on B.Category_ID = C.Category_ID) D
on C.Product_Name = D.Product_Name
group by Category_Name
order by Amount desc;
```
![TOP SELLING CATEGORY](https://github.com/user-attachments/assets/ca0cd2a6-0a8f-4437-b205-06595ce57f49)

![REVENUE BY CATEGORY](https://github.com/user-attachments/assets/876789f2-d9a5-41d7-95ab-bbc198a89b4c)

![ORDER BY CATEGORY](https://github.com/user-attachments/assets/0a0cc3f5-2b4b-49fe-ab79-4276f6bd5e0d)

![CATEGORY DISTRIBUTION BY COUNTRY](https://github.com/user-attachments/assets/66d17584-31e8-43ec-a578-db807e1bd749)

## SHIPPING MODE

- JOINING SHIPPMENT AND ORDERS TABLES 
```sql
select * from orders o
join shippers s 
on o.Shipper_ID = s.Shipper_ID;
```

- AVERAGE DAYS IT TAKES TO DELIVER AN ORDER
```sql
select abs(avg(datediff(day,Required_Date,Order_Date))) as Expected_Avg_Delivery_Day
from orders O
join(
select abs(avg(datediff(day,Shipped_Date,Required_Date))) as Expected_Avg_Delivery_Day
from orders) A
on O.Required_Date = A.Required_Date;
```

- EFFECT OF EARLY DELIVERY ON REVENUE
```sql
select 
  case 
    when abs(datediff(Day, Shipped_Date, order_date)) <=  1  then 'On Time'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  3  then '1-3 Days'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  7  then '4-7 Days'
    else 'More than 7 Days'
  end as Early_Delivery_Range,
  avg(revenue) as average_revenue
from (select top 10 Company_Name, Country,sum(Quantity) as Total_Quantity,count(Order_ID) as Total_Order, Order_Date, Shipped_Date,
		sum(Amount) as Revenue, sum(Discount) as Discount
from(select Order_Date, Shipped_Date, Order_ID,Customer_ID,A.Product_ID,Product_Name,Selling_Price = A.Unit_Price, 
		Actual_Unit_Price = P.Unit_Price, Quantity, Amount, round(P.Unit_Price - A.Unit_price,2) as Discount, Freight
	from(select Order_Date, Shipped_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
		from orders O
		join order_details OD
		on O.Order_ID = OD.Order_ID) A 
	join (select * from Products) P
	on A.Product_ID = P.Product_ID)B
join (select * from customers) C
on B.Customer_ID = C.Customer_ID
group by Company_Name, Country, Order_Date,Shipped_Date)F
--order by Revenue desc;
group by
  case 
    when abs(datediff(Day, Shipped_Date, order_date)) <=  1  then 'On Time'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  3  then '1-3 Days'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  7  then '4-7 Days'
    else 'More than 7 Days'
  end
order by
  case 
    when abs(datediff(Day, Shipped_Date, order_date)) <=  1  then 'On Time'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  3  then '1-3 Days'
    when abs(datediff(Day, Shipped_Date, order_date)) <=  7  then '4-7 Days'
    else 'More than 7 Days'
  end;
```

![EARLY DELIVERY EFFECT](https://github.com/user-attachments/assets/5b43449f-3766-4656-b58d-d4e14b08ce2a)


- WHAT FACTOR DETERMINES PREFFERED SHIPPING MODE
```sql
select s.Shipper_ID, Company_Name, round(avg(Freight),2) as Avg_Freight, 
	round(sum(Freight),2) as Freight_Revenue,
	avg(datediff(day,Shipped_Date, Required_Date)) as Avg_Delivery_Day, 
	count(Company_Name) as Count
from orders o 
join shippers s 
on o.Shipper_ID = s.Shipper_ID
group by Company_Name, s.Shipper_ID
order by Avg_Delivery_Day asc;
```

![SHIPPING MODE DETERMINANT](https://github.com/user-attachments/assets/d05d1c6a-102a-4c3a-b429-89eaa07dd6dc)


- DOES FREIGHT DETERMINE PREFERRED SHIPPING MODE
```sql
select Company_Name, round(avg(Freight),2) as Avg_Freight, count(Company_Name) as Count
from(select s.Company_Name,Freight, o.Shipper_ID from orders o 
join shippers s 
on s.Shipper_ID = o.Shipper_ID) A
group by Company_Name
order by Avg_Freight desc;
```

![FREIGHT ON SHIPPING MODE](https://github.com/user-attachments/assets/1333e1fe-c078-49c2-bdb3-03f43e8cc3ff)

![SHIPPMENT](https://github.com/user-attachments/assets/5aefb187-3205-468c-b4e9-8b573b77cb1f)


## SALES REPRESENTATIVE ANALYSIS

- BEST SALES REPRESENTATIVES BY REVENUE GENERATED
```sql
select Employee_Name, Title,round(sum(Amount),2) as Revenue
from (select O.Employee_ID, Employee_Name, Title
	from employees E 
	join orders O
	on O.Employee_ID = E.Employee_ID)A
join (select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
	from orders O
	join order_details OD
	on O.Order_ID = OD.Order_ID)B
on A.Employee_ID = B.Employee_ID
group by Employee_Name, Title
order by Revenue desc; 

select Employee_Name, Title,Amount
from (select O.Employee_ID, Employee_Name, Title
	from employees E 
	join orders O
	on O.Employee_ID = E.Employee_ID)A
join (select Order_Date, O.Order_ID, Customer_ID,Product_ID, round(Unit_Price,2) as Unit_price, Quantity,
		Employee_ID, round(Quantity * Unit_Price,2) as Amount, Discount, round(Freight,2)Freight
	from orders O
	join order_details OD
	on O.Order_ID = OD.Order_ID)B
on A.Employee_ID = B.Employee_ID;
```

![SALES REP AND REVENUE](https://github.com/user-attachments/assets/d2c62ac2-25d8-45f5-943d-a6ac19e2f7c6)


![TOP SALES REP](https://github.com/user-attachments/assets/2716450e-838c-4626-9210-2383fb2d063f)


## OBSERVATIONS:
- At 190,329.95, April had the highest Revenue and was 386.93% higher than June, which had the lowest Revenue at 39088.
- Revenue and total Orders are positively correlated with each other.
- Revenue also correlated positively with Discount but with a 0.2 correlation coefficient, it shows that discount is not the primary driver of Revenue.
- April accounted for 14.05% of Revenue.
- Revenue and Orders diverged the most when the Month was April, when Revenue were 190,068.95 higher than Orders.
- Among the Eight Categories Diary Products generated more than 25% of the Revenue.
- More than 50% of the Total Revenue comes from United States of America and Germany. United States is the Hub for most of the Products.


## RECOMMENDATIONS:
- Instead of offering blanket discounts, design targeted promotions that provide value to specific customer segments. This can maintain price integrity and drive sales and revenue growth.
- Segmenting customers based on their needs, preferences and willingness to pay.  Develop tailored pricing strategies for each segment.
- Evaluate pricing strategy to determine if it aligns with the business goals and market targets
















