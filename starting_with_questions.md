Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
/* Cities */
select country, city, sum(totaltransactionrevenue)
from all_sessions
group by country, city
order by sum(totaltransactionrevenue) desc nulls last

/* Countries */
select country, SUM(totaltransactionrevenue)
from all_sessions
group by country
order by sum(totaltransactionrevenue) desc nulls last


Answer:
    Cities:
        'not available in demo dataset'
        San Francisco
        Sunnyvale
        Atlanta
        Palo Alto
        Tel Aviv-Yafo
        New York
        Mountain View
        Los Angeles
        Chicago
    
    Countries:
        united states
        israel
        australia
        canada
        switzerland



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
/* Cities */
select country, city, sum(productquantity)
from all_sessions
group by country, city
order by sum(productquantity) desc nulls last

/* Countries */
select country, sum(productquantity)
from all_sessions
group by country
order by sum(productquantity) desc nulls last


Answer:
    Cities:
        'not available in demo dataset' - USA
        Madrid
        Salem
        New York
        Mountain View
        'not available in demo dataset' - Canada
        Sunnyvale
        San Francisco
        Houston
        
    Countries:
        United States
        Spain
        Canada
        Ireland
        Argentina
        Mexico
        Colombia
        Finland
        India
        France



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
select country, v2productcategory, count(transactions)
from all_sessions
group by country, v2productcategory
having count(transactions) > 0
order by country, count(transactions) desc nulls last


Answer:
    None immediately visible – consider further investigation if time allows
        Limited number of purchases seem available using this method




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
/* initial check - rendered obsolete following further cleaning which removed all_sessions.v2productname */
with orders as (
    select * 
    from all_sessions 
    where transactions is not null)
select country, city, v2productname, count(v2productname)
from orders
group by country, city, v2productname
order by count(v2productname) desc

/* altered query to join products table and reference products.name, sum quantity from transactions by product rather than counting instances of product */
with orders as (
    select * 
    from all_sessions 
    where transactions is not null)
select o.country, o.city, p.name, sum(o.productquantity)
from orders o
join products p on o.productsku = p.sku
group by o.country, o.city, p.name
order by sum(o.productquantity) desc nulls last

Answer:
    Still returning questionable data 




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

/* transaction revenue from all_sessions grouped by country */

select country, sum(totaltransactionrevenue)
from all_sessions
group by country
order by sum(totaltransactionrevenue) desc nulls last

/* transaction revenue from all_sessions grouped by city */

select city, sum(totaltransactionrevenue)
from all_sessions
group by city
order by sum(totaltransactionrevenue) desc nulls last

Answer:

Countries:
    United States responsible for vast majority of sales
    Israel
    Australia
    Canada
    Switzerland
    
Cities:
    Large proportion have unset city, most listed revenue originates in
        San Francisco
        Sunnyvale
        Atlanta
        Palo Alto





