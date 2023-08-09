What are your risk areas? Identify and describe them.

Several tables and columns still contain data which is either 
    a) duplicated elsewhere
    b) difficult to distinguish from another column (e.g. all_sessions.productquantity vs all_sessions.itemquantity, etc)
    
Duplicate values still present in analytics

Require further checks to properly format time, price data


QA Process:
Describe your QA process and include the SQL queries used to execute it.

/* Do the summed totaltransactionrevenue, transactionrevenue, and price*quantity-discount*quantity,
grouped by country, align with one another? */

select country, sum(totaltransactionrevenue) as ttr, sum(transactionrevenue) as tr, sum((productprice * productquantity - productrefundamount * productquantity)) as product_calc
from all_sessions
group by country
order by sum(totaltransactionrevenue) desc nulls last


select totaltransactionrevenue, transactionrevenue, productprice, productquantity, productrefundamount
from all_sessions

select totaltransactionrevenue, transactionrevenue, productprice, productquantity, productrefundamount
from all_sessions
order by productrefundamount desc nulls last

select totaltransactionrevenue, transactionrevenue, productprice, productquantity, productrefundamount
from all_sessions
where productrefundamount is not null

/* all results null, dropping column */
alter table all_sessions
drop column productrefundamount


