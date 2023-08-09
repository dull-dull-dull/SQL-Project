Question 1: Which pages have received the most pageviews?

SQL Queries:

select p.name, sum(a.pageviews) as total_pageviews
from products p
join all_sessions a
on p.sku = a.productsku
group by p.name
order by sum(a.pageviews) desc

Answer: 
"Google Men's 100% Cotton Short Sleeve Hero Tee White"    1268
"YouTube 22 oz Infuser Bottle"    747
"Google Men's 100% Cotton Short Sleeve Hero Tee Black"    742
"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"    733
"YouTube Men's Short Sleeve Hero Tee Black"    724


Question 2: Which channel group generates the most pageviews?

SQL Queries:

select channelgroup, sum(pageviews) as total_pageviews
FROM all_sessions a
group by channelgroup
order by sum(pageviews) desc

Answer:
"Organic Search"    36249
"Referral"    14214
"Direct"    13226
"Paid Search"    2984
"Affiliates"    1192
"Display"    600
"(Other)"    14


Question 3: Which products have been ordered most?

SQL Queries:

select name, count(name)
from products
group by name
order by count(name) desc

select distinct(name), sum(orderedquantity)
from products
group by name
order by sum(orderedquantity) desc

select distinct(sku), name, sum(orderedquantity)
from products
group by sku
order by sum(orderedquantity) desc

Answer:

"GGOEGFSR022099"    "Google Kick Ball"    15170
"GGOEGDHC018299"    "Google 22 oz Water Bottle"    10075
"GGOEGAAX0074"    "Google 22 oz Water Bottle"    8942
"GGOEGAAX0037"    "Google Sunglasses"    4204
"GGOEGOCC077999"    "Google Spiral Journal with Pen"    3896
"GGOEYFKQ020699"    "YouTube Custom Decals"    3786
"GGOEGCBQ016499"    "SPF-15 Slim & Slender Lip Balm"    3682
"GGOEGOLC013299"    "Spiral Notebook and Pen Set"    3582
"GGOEGOCB017499"    "Leatherette Journal"    3071
"GGOENEBQ078999"    "Nest® Cam Outdoor Security Camera - USA"    2719


Question 4: Which customers spent the most money?

SQL Queries:

select distinct(fullvisitorid), sum(totaltransactionrevenue) as total_purchases
from all_sessions
group by fullvisitorid
order by sum(totaltransactionrevenue) desc nulls last

Answer:

"1480311877127336013"    1015.48
"3444642706013409818"    1005.5
"513403330506317388"    849.7
"751536527071005965"    747
"8444725132150789814"    742.48
"368133363767522278"    649.24
"2318731926469069951"    602
"9426961848593138180"    396
"2809719389620100374"    391
"9144248942571287239"    363


Question 5: 

SQL Queries:

Answer:
