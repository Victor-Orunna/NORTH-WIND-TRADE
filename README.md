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
























