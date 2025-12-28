# Porfolio 

## Project 1

### Business Performnace 

Process: 
Define the Problem and Objectives - Planned the desired outcome by asking sufficient questions and narrowed the description to specific objectives to work on.<br/>
Data Collection - Reviewed the database and, based on the objectives, confirmed whether the data collected was sufficient for the desired outcome.<br/>
Data Cleaning and Processing - After reviewing, I used DBeaver to run queries in SQL to clean and extract the critical information needed.<br/>
Exploratory Analysis - Created initial visualizations to identify overall patterns, detect anomalies in the data (to request their correction), and developed initial hypotheses.<br/>
Statistical Analysis - I reviewed the current patterns to confirm data integrity and to interpret the existing data.<br/>
Visualization and Communication - Once I had all the desired information and confirmed its integrity, I created the appropriate visualizations to share the results and insights with stakeholders

Tools and resources: 
  Database - PostgreSQL database.<br/>
  DBeaver - SQL coding to clean the database and extract critical information.<br/>
  Looker Studio - For data visualization.<br/>

Data: 
  Postgres Database - Internal data collection and management system within the company.<br/>
    - To ensure data integrity, I ran 3 different querys for different dashboards within one report

Business Problem: 
  Optimize resources, gain clarity about their own transactions, gain efficiency in their operations and resources

Objectives: 
  Identify which revenue sources are most profitable and consistent to optimize business development and resource allocation.<br/>
  Categorize, track, and optimize business expenses to improve operational efficiency and reduce unnecessary costs.<br/>
  Understand personnel costs and allocation efficiency to optimize team productivity and project profitability.<br/>

Purpose: 
  Provide actionable insights on financial performance, risk exposure, and resource optimization to support strategic decision-making.

Key Questions Answered 
  Where is the revenue concentrated, and what are the associated risks?.<br/>
  Which expense categories offer the greatest optimization opportunities?.<br/>
  How efficiently are we allocating team resources to maximize profitability?.<br/>
  Are we accurately estimating project costs, and which projects deviate most from budget?.<br/>
  How dependent are we on our top customers, and what is our revenue diversification risk?.<br/>
  Which revenue streams are most profitable, and where should we invest resources?.<br/>
  What are our revenue patterns, and how can we improve cash flow predictability?.<br/>
  Where are we spending the most, and which categories offer cost reduction opportunities?.<br/>
  Are we optimizing our vendor relationships, and can we consolidate for better rates?.<br/>
  What are our fixed monthly commitments, and are we getting value from all subscriptions/services?.<br/>
  Are we assigning the right number of team members to projects, and what's the ROI per resource?.<br/>

Key Results and indicators:

  KR 1.1: Project Profitability Variance.<br/>

  KR 1.2: Customer Revenue Concentration.<br/>

  KR 1.3: Stream Profitability Ratio.<br/>

  KR 1.4: Revenue Growth & Timing.<br/>

  KR 2.1: Expense Category Optimization.<br/>

  KR 2.3: Recurring Expense Management.<br/>

  KR 3.1: Project Resource Allocation Efficiency.<br/>

  KR 3.2: Transaction Activity & Spending Patterns.<br/>


Visuzalization: 
<img width="1079" height="812" alt="image" src="https://github.com/user-attachments/assets/9cb3a059-17ec-41b7-bb35-377b39e2e3ef" />

<img width="1084" height="814" alt="image" src="https://github.com/user-attachments/assets/cd8f0122-6488-49cd-a200-3d6b7b73e3e1" />

<img width="1083" height="816" alt="image" src="https://github.com/user-attachments/assets/db5d476e-7f8d-4b4e-b598-cbb598427436" />

<img width="1086" height="820" alt="image" src="https://github.com/user-attachments/assets/73bf550c-2d33-4f02-a45e-4326bfd77a97" />

<img width="1084" height="815" alt="image" src="https://github.com/user-attachments/assets/bce0a978-0a12-4731-8d2c-e3aa39060357" />

<img width="1085" height="814" alt="image" src="https://github.com/user-attachments/assets/7d177d17-fe34-4fc0-824e-dc2b608ef2f6" />

SQL Code: 
  (For ethical and security purposes, table's name has been changed to generic names) 

SELECT
    a.id,
    a.title as item_name,<br/>
    b.category as category_name,<br/>
    d.company_name as client,<br/>
    MIN(e.source_name) as vendors,<br/>
    SUM(a.allocated_amount / 100) as budget,<br/>
    e.currency_code as currency,<br/>
    SUM(a.invoiced_amount / 100) as total_billed,<br/>
    SUM(CASE<br/>
        WHEN e.flow_type = 1 AND e.record_type = 'main_item'<br/>
        THEN e.converted_amount<br/>
        ELSE 0<br/>
    END) / 100 AS income,<br/>
    SUM(CASE<br/>
        WHEN e.flow_type = 2<br/>
        THEN e.converted_amount<br/>
        ELSE 0<br/>
    END) / 100 AS outcome,<br/>
    MIN(e.created_at) as first_record_date,<br/>
    MAX(e.created_at) as last_record_date<br/>
FROM<br/>
    main_items a<br/>
LEFT JOIN
    financial_records e ON a.id = e.item_id<br/>
LEFT JOIN<br/>
    categories b ON a.category_id = b.id<br/>
LEFT JOIN<br/>
    clients d ON a.client_id = d.id<br/>
GROUP BY<br/>
    a.id,<br/>
    a.title,<br/>
    b.category,<br/>
    d.company_name,<br/>
    e.currency_code<br/>
ORDER BY<br/>
    income DESC;<br/>

------------------ Correct filters<br/>

SELECT<br/>
    a.id,<br/>
    a.title as item_name,<br/>
    b.category as category_name,<br/>
    d.company_name as client,<br/>
    fin.vendors,<br/>
    a.allocated_amount / 100 as budget,<br/>
    fin.currency,<br/>
    a.invoiced_amount / 100 as total_billed,<br/>
    fin.income,<br/>
    fin.outcome,<br/>
    fin.first_record_date,<br/>
    fin.last_record_date<br/>
FROM<br/>
    main_items a<br/>
LEFT JOIN (<br/>
    SELECT<br/>
        e.item_id,<br/>
        e.currency_code as currency,<br/>
        MIN(e.source_name) as vendors,<br/>
        SUM(CASE<br/>
            WHEN e.flow_type = 1 AND e.record_type = 'main_item'<br/>
            THEN e.converted_amount<br/>
            ELSE 0<br/>
        END) / 100 AS income,<br/>
        SUM(CASE<br/>
            WHEN e.flow_type = 2<br/>
            THEN e.converted_amount<br/>
            ELSE 0<br/>
        END) / 100 AS outcome,<br/>
        MIN(e.created_at) as first_record_date,<br/>
        MAX(e.created_at) as last_record_date<br/>
    FROM<br/>
        financial_records e<br/>
    GROUP BY<br/>
        e.item_id,<br/>
        e.currency_code<br/>
) fin ON a.id = fin.item_id<br/>
LEFT JOIN<br/>
    categories b ON a.category_id = b.id<br/>
LEFT JOIN<br/>
    clients d ON a.client_id = d.id<br/>
ORDER BY<br/>
    fin.income DESC;<br/>


-------- 6th page

SELECT<br/>
    e.id,<br/>
    MIN(e.source_name) as vendor,<br/>
    MIN(f.description) as account_names,<br/>
    e.currency_code,<br/>
    e.converted_amount / 100 as total_outcome,<br/>
    e.created_at,<br/>
    e.classification<br/>
FROM financial_records e<br/>
LEFT JOIN ledger_entries g ON e.id = g.record_id<br/>
INNER JOIN account_master f ON g.account_id = f.id<br/>
WHERE e.flow_type = 2<br/>
GROUP BY<br/>
    e.id,<br/>
    e.source_name,<br/>
    e.currency_code,<br/>
    e.converted_amount,<br/>
    e.created_at,<br/>
    e.classification<br/>
ORDER BY total_outcome DESC;<br/>


**Insights** <br/>
Exectuvie Summary: <br/>
Provide actionable insights on financial performance, risk exposure, and resource optimization to support strategic decision-making.<br/>
  Revenue concentration risk.<br/>
  Profit margin by stream.<br/>
  Expense reduction opportunity.<br/>
  Resource utilization efficiency.<br/>
  Identification of the top priorities for financial optimization.<br/>
  Risk mitigation recommendations for revenue concentration.<br/>
  Resource reallocation strategy to improve project profitability.<br/>

Core Analysis:<br/>
Identify which revenue sources are most profitable and consistent to optimize business development and resource allocation.<br/>
  Detailed variance report by project.<br/>
  Variance trends over time.<br/>
  Complete customer revenue ranking with contribution percentages.<br/>
  Historical trend analysis of customer concentration.<br/>
  Detailed P&L by stream.<br/>
  Expense breakdown by stream category.<br/>
  Detailed expense ledger by category with transaction-level detail.<br/>
  Statistical analysis of expense patterns.<br/>
  Complete vendor transaction history.<br/>
  Price variance analysis by vendor over time.<br/>
  Pattern recognition results.<br/>
  Historical spending trends by recurring service.<br/>
  Resource allocation.<br/>
  Regression analysis: team size vs project profitability.<br/>
  Historical utilization trends.<br/>

**Recommendations**<br/>
Based on the analysis conducted on the business, it is critical to either increase revenue per project or expand the number of projects to reduce direct dependency on the top two clients. It is recommended to plan on a quarterly basis instead of annually, as this will provide greater stability to the business and create improved revenue margins in later quarters.<br/>
Given the company's profitability profile, it is recommended to track expenses directly related to projects, as this will create better visibility into expenditures and provide superior control over the income versus outcome balance within the company and its resources.<br/>
In the following pages, it is recommended to focus on the most profitable stream and establish a centralized operational stream, as this provides better control over project operations. Additionally, it is recommended to standardize categories across merchants and transaction types, as this provides clear visibility into the nature of transactions being made and their destinations.<br/>
As a final recommendation, it is critical to separate operational expenses from workforce-related expenses, as this distinction creates better information for budgeting purposes, enables accurate calculation of annual operating costs, and facilitates more precise project budgeting and man-hour allocation.




  
