# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
Create database in PostgreSQL using provided CSV files outlining product, order, and site details.

## Process
Analyze database
Re-map data into appropriate tables
Assign proper primary and foreign keys
Transform data into appropriate formats
Answer existing questions
Create and answer additional questions
Complete QA process
Complete README

## Results
README.md contains a description of the enclosed files along with general information about the project.

cleaning_data.md contains notes about data cleaning process, including many of the queries used to investigate and transform the data.

starting_with_questions.md contains answers to provided questions regarding geographic distribution of sales and the queries used to determine that data.

starting_with_data.md contains further questions which the database can be used to answer, along with their answers and the queries used to determine that data. 

QA.md contains an outline of the quality assurance process used to validate the answers provided.

## Challenges 
Where seemingly identical columns exist in multiple tables, difficult to tell which should be treated as reliable.

Difficult to tell specific purpose of all_sessions vs analytics

Unsure if appropriate to remove sales_by_sku & sales_report tables – these should likely be views or saved queries drawing from other tables but I was unable to correlate their data with the others.

## Future Goals
More effectively map database prior to building tables
More data cleaning required
    Data formatting needs more attention
    Duplicate records need to be addressed
Incomplete QA process
***Primary and foreign keys not yet mapped


