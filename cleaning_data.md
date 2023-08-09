What issues will you address by cleaning the data?

1. Assess Table-level Database Structure
    Setting appropriate primary and foreign keys
    Removing columns unnecessarily duplicated across tables
    Re-mapping columns where they would be more effectively stored in a different table

2. Assess Columns and Data
    Appropriate data types for all columns
    Missing and duplicate listings for site visits
    Missing and duplicate listings for products
    Missing and duplicate listings for countries / cities
    Transformation of numeric data where required, especially pricing and time
    
    

Queries:
Below, provide the SQL queries you used to clean your data.


/* Is any fullvisitorid associated with multiple visitids?

    select fullvisitorid, count(visitid) 
    from all_sessions a
    group by fullvisitorid 
    order by count(visitid) desc nulls last
*/
    /* Yes */

/* Is any visitid associated with multiple rows?
    
    select visitid, count(visitid) 
    from all_sessions a
    group by visitid
    order by count(visitid) desc nulls last
*/    
    /* Yes */

/* Is any fullvisitorid and visitid associated with multiple rows? 

    select fullvisitorid || visitid, count(fullvisitorid || visitid)
    from all_sessions a
    group by fullvisitorid || visitid
    order by count(fullvisitorid || visitid) desc
*/
    /* Yes */

/* Is any fullvisitorid and visitid and productsku associated with multiple rows?

    select fullvisitorid || visitid || productsku, count(fullvisitorid || visitid || productsku)
    from all_sessions a
    group by fullvisitorid || visitid || productsku
    order by count(fullvisitorid || visitid || productsku) desc
*/
    /* Yes */

/* Investigate all column data for results of previous table with more than 1 listing.

    select *
    from all_sessions a
    where fullvisitorid || visitid || productsku
        in ('33908455805952548601489797382GGOEAOCH014199',
            '53557005918059340831495005901GGOEGCBB074199',
            '8686967448169354801493605322GGOEGHPJ080110',
            '99212501752989454061493909433GGOEGAAX0355',
            '17045320138500099441481233332GGOENEBJ079499')
    order by fullvisitorid || visitid || productsku
*/
    /* Each duplicate listing has two distinct times. 
    This can be concatenated to form a unique identifier. */

/* Ensuring this is correct by concatenating the identifier and counting occurrences.

    select     
        fullvisitorid || visitid || productsku || time, 
        count(fullvisitorid || visitid || productsku || time)
    from all_sessions a
    group by fullvisitorid || visitid || productsku || time
    order by fullvisitorid || visitid || productsku || time desc
*/
    /* Confirmed – this can be used as a unique identifier */

/* Add table and data

    alter table all_sessions
    add column uniqueid varchar
    


    update all_sessions
    set uniqueid = fullvisitorid || visitid || productsku || time
    
    */ 
    
    TABLE
    POTENTIAL TABLE-LEVEL ISSUES
    
        COLUMN
            POTENTIAL COLUMN-LEVEL ISSUE
                QUERY
                    EXPLANATION
                        RESULT

all_sessions
    fullvisitorId
        duplicate values
            SELECT count(*), count(distinct(fullvisitorid))
            FROM all_sessions
                Does the count of all rows match the number of unique visitor ids?
                    No - row count 15134, id count 14223
                    
            SELECT fullvisitorId, COUNT(fullvisitorId)
            FROM all_sessions
            GROUP BY fullvisitorId
            ORDER BY COUNT(fullvisitorID) DESC
                Are any fullvisitorid's listed more than once?
                    Yes – 794
                    
            SELECT fullvisitorId, COUNT(fullvisitorId)
            FROM all_sessions
            GROUP BY fullvisitorId
            HAVING COUNT(fullvisitorId) > 1 OR COUNT(fullvisitorId) < 0
            ORDER BY COUNT(fullvisitorId) DESC
                How many fullvisitorid's are listed more than once (or returning value less than 0)?
                794 – matches previous query so addressing duplicate values should solve issue
            
        inconsistent length
        
        missing or null values
            SELECT fullvisitorid
            FROM all_sessions
            WHERE fullvisitorid IS NULL
                Are any fullvisitorid values null?
                    No – clean
        
    channelgroup
        missing or null values
            SELECT channelgroup
            FROM all_sessions
            WHERE channelgroup IS NULL
                Are any channelgroup values null?
                    No - clean
        
        incorrect categories (count of each category)
            SELECT channelgroup, COUNT(channelgroup)
            FROM all_sessions
            GROUP BY channelgroup
            ORDER BY COUNT(channelgroup) DESC
                How many listings are there for each channel group?
                    Appears normal – investigate "(Other)" category
    time
        what information is being communicated? 
            SELECT time/60
            FROM all_sessions
                if the data is recorded as seconds (v2: or minutes), how many minutes does it represent (v2: or hours)?
                        results too high
            SELECT time/60/60
            FROM all_sessions
                if the data is recorded as seconds, how many hours does it represent?
                    results too high
            
        what do "0" values represent?
        is there a more useful way to describe?
        should this be a time value rather than an integer?
        
        select cast(max(time) as integer), min(time)
        from all_sessions
        
    country
        missing or null values
            SELECT *
            FROM all_sessions
            WHERE country IS NULL
                Are any values NULL?
                    No - clean

        incorrect categories (count of each category)
            SELECT country, COUNT(country)
            FROM all_sessions
            GROUP BY country
            ORDER BY COUNT(COUNTRY) DESC
                How many sessions originated in each country? Are all of them real countries? Are there duplicate entries for any country?
        inconsistent reference to categories (US vs. USA etc)
            not set - 24
            Macedonia (FYROM) - 4
            
        
        
    city
        missing or null values
            not available in dataset - 8302
            not set - 354
                
        incorrect categories (count of each category)
            SELECT city, COUNT(city)
            FROM all_sessions
            GROUP BY city
            ORDER BY COUNT(city) DESC
        inconsistent reference to categories (US vs. USA etc)
        
    totaltransactionrevenue
        many null values – is information being tracked, can it be parsed from data set
            SELECT totaltransactionrevenue, count(totaltransactionrevenue)
            FROM all_sessions
            GROUP BY totaltransactionrevenue
            HAVING totaltransactionrevenue > 0
            ORDER BY count(totaltransactionrevenue) DESC
               /* Unclear result, consider alternative approach */
                
    transactions
        many null values – is information being tracked, can it be parsed from data set
            /* created materialized view including only sessions with purchases */
            
            create materialized view transactions as
            select * 
            from all_sessions
            where transactions is not null
            
    timeonsite
        what information is being communicated?
        is there a more useful way to describe?
        should this be a time value rather than an integer?
           
            select timeonsite
            from all_sessions
            
            updated table to format timeonsite
            
    pageviews
        outliers
        
    sessionquality
        many null values – is information being tracked, can it be parsed from data set
        how is data defined
        
    date
        missing / null values?
        consistent data type?
        outliers?
        unlikely values?
        
    visitid
        missing / null values?
        duplicate values? reason?
        
    type
        missing / null values?
        incorrect categories (count of each category)
        
    productrefundamount
        format consistent with other pricing data?
        
    productquantity
        many null values – is information being tracked, can it be parsed from data set
        
    productprice
        need to transform to correct pricing format
           
            UPDATE all_sessions
            SET productprice = productprice/1000000
                Divided product price values by 1000000

    productrevenue
        many null values – is information being tracked, can it be parsed from data set
            
            
    productsku
        inconsistent length / format – can ids be adjusted to consistent unique values
        
    v2productname
        missing values?
        duplicate values?
        should likely be excluded – product identifiable by id via products table
            
            eliminated table
        
    v2productcategory
        ineffective way to note categories – should be own table
        
    productvariant
        what info conveyed?
            select productvariant, count(productvariant)
            from all_sessions
            where productvariant is not null
            group by productvariant
            
                column is being used, though sparsely. appropriate formatsele
        
    currencycode
        do all listings contain currency code?
        do all listings contain valid currency code?
            
            update all_sessions
                set currencycode =
                    case
                    when currencycode = 'USD' then currencycode
                    else 'USD'
                    END
                        
                        select *
                        from all_sessions
                        where currencycode is null
                        
    itemquantity
        mostly null, parseable?
        likely duplicate (productquantity)
        
    itemrevenue
        mostly null, parseable?
        likely duplicate (productrevenue)
        
    transactionrevenue
        mostly null, parseable?
            select totaltransactionrevenue, transactionrevenue, productprice, productquantity, productrefundamount
            from all_sessions
            where transactionrevenue is not null
        
    transactionid
        mostly null, parseable?
        
    pagetitle
        ineffective way to store page data – should be own table
        
    searchkeyword
        mostly null, parseable?
        
    pagepathlevel1
        likely ineffective data storage
        
    ecommerceaction_type
        what is being communicated?
        
    ecommerceaction_step
        what is being communicated?
        
    ecommerceaction_option
        what is being communicated?
        mostly null, parseable?


analytics

    visitnumber
        is duplicate data appropriate?
        missing/null values?
        
    visitid
        is duplicate data appropriate?
        missing/null values?
        
    visitstarttime
        is existing data format consistent / can it be coerced to a date
        
    date
        missing/null values?
        formatting?
        outliers / unlikely values?
        
    fullvisitorid
        incosistent length – appropriate? likely to cause problems?
        
    userid
        mostly null – parseable from data?
        
    channelgrouping
        missing / incorrect categories (count of each category)?
        
    socialengagementtype
        possible this value is coercable to boolean, would save storage & make more navigable
        
    units_sold
        mostly null, parseable?
        
    pageviews
        mostly 1 – if each listing denotes a pageview, this column is redundant
        
    timeonsite
        mostly null – parseable? 
        
    bounces
        coercable to boolean? possibly helpful re storage (if current column type is storing additional unused spaces)
        
    revenue
        mostly null – parseable? 
        
    unit_price
        need to transform to correct pricing format
        likely duplicate – should be in 'products' table

products

    sku
        inconsistent format
        likely this should be central sku listing
        duplicates?
        
    name
        missing / null / duplicate?
        likely this should be central product name listing
        
    orderedquantity
        is this appropriate place for this data?
        how is this being tracked / updated?
        can this be validated?
        
    stocklevel
        how is this being tracked / updated?
        can this be validated?
        
    restockingleadtime
        how is this being tracked / updated?
        can this be validated?
        
    sentimentscore
        what info being communicated?
        
    sentimentmagnitude
        what info being communicated?
        
sales_by_sku
this table can likely be included in products or sales report

    productsku
    
    total_ordered

sales_report
this table can likely be included in products if appropriate on further investigation
this may more appropriately exist as a view on products once data is assessed,
duplicates columns available in other tables
    
    productsku
        SELECT *, count(productsku)
        FROM sales_by_sku
        GROUP BY productsku
        ORDER BY productsku DESC
            Are any products listed more than once?
                No – clean
                
    total_ordered
    
    name
    
    stocklevel
    
    restockingleadtime
    
    sentimentscore
    
    sentimentmagnitude
    
    ratio


