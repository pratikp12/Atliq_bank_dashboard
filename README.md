# Mitron_bank_dashboard
This Power BI dashboard provides an in-depth analysis of bank customers' data, focusing on demographics, expenditures, and income utilization. It offers valuable insights into customer behavior, spending patterns, and financial habits, helping banks make data-driven decisions.
Overview
This Power BI dashboard provides an in-depth analysis of bank customers' data, focusing on demographics, expenditures, and income utilization. It offers valuable insights into customer behavior, spending patterns, and financial habits, helping banks make data-driven decisions.<br>

<a href='https://app.powerbi.com/view?r=eyJrIjoiN2Q3OTFlODQtNGJkOC00OTJmLWEwMDQtM2RkOWU5MzkwMzE2IiwidCI6ImM2ZTU0OWIzLTVmNDUtNDAzMi1hYWU5LWQ0MjQ0ZGM1YjJjNCJ9'>live dashboard</a>


Key Features<br>
✅ Demographic Analysis – Visualizes customer distribution by age group, gender, city, and other key factors.<br>
✅ Expenditure Insights – Tracks spending habits across different categories and time periods. <br>
✅ Income Utilization – Analyzes how customers allocate their income between savings, investments, and expenses.<br>
✅ Interactive Visuals – Dynamic filters and drill-down capabilities for a deeper understanding.<br>

### Dataset discription
This file contains all the meta information regarding the columns described in the CSV files. We have provided 2 CSV files:
1. dim_customers
2. fact_spends


Column Description for dim_customers:
- customer_id: This column represents the Unique ID assigned to each customer.
- gender: This column represents the gender of the customer. (Male, Female)
- age_group: This column categorizes the customer into different age groups. (21-24, 25-34, 35-45, 45+)
- marital_status: This column indicates the marital status of the customer (single, married).
- city: This column represents the city of residence for the customer. (Mumbai, Delhi-NCR, Chennai, Hyderabad, Bengaluru)
- occupation: This column denotes the occupation or profession of the customer. (Salaried IT Employees, Salaried Other Employees, Business Owners, Freelancers, Government Employees)
- average_income: This column indicates the monthly average income of the customer, in INR currency.


*******************************************

Column Description for fact_spends:
- customer_id: This column represents the Unique ID of each customer, linking to the dim_customer table.
- month: This column indicates the month in which the spending was recorded. (May, June, July, August, September, October)
- category: This column describes the category of spending (Entertainment, Apparel, Electronics, etc).
- payment_type: This column specifies the type of payment used by the customer (Debit Card, Credit Card, UPI, Net Banking).
- spends: This column shows the total amount spent by the customer in the specified month, category and payment_type.




Demograpics
![image](https://github.com/user-attachments/assets/a35a8440-d591-44ca-9a86-92e4044fec4a)

Spends
![image](https://github.com/user-attachments/assets/92ca077c-902d-4588-aed0-6273497c34cd)

Income Utilization
![image](https://github.com/user-attachments/assets/cff69b28-d0ac-4835-ad4e-d35d69c40af7)


### measure 
<ol>
  <li >
    <pre>
    Total Customer - Not affected by any filter <br>
Total_Customers = 
CALCULATE(
    DISTINCTCOUNT(dim_customers[customer_id]), 
    ALL(dim_customers)
) </pre>
  </li>
  <li>
    <pre>
      Total_Male_Customers - affected by filters as no ALL() used. <br>
    Total_Male_Customers = 
CALCULATE(
    DISTINCTCOUNT(dim_customers[customer_id]), 
    dim_customers[gender] = "Male"
)
    </pre>
    
  </li>
  <li>
    <pre>
    Total_Female_Customers = 
CALCULATE(
    DISTINCTCOUNT(dim_customers[customer_id]), 
    dim_customers[gender] = "Female"
)
    </pre>
  </li>
  <li>
    Total spends = sum(fact_spends[spend])
  </li>
  <li>
    <pre>
      Avg_Spends = 
VAR SelectedMonths = DISTINCTCOUNT(fact_spends[month])
RETURN 
SUM(fact_spends[spend]) / IF(SelectedMonths = 0, 6, SelectedMonths)

    </pre>
    
  </li>
  <li>
    
    total avg income = sum(dim_customers[avg_income])
  </li>
  <li>
    AvgIncomeUtilization% = DIVIDE([Avg_Spends], [total avg income], 0) 

  </li>
  <li>
    <pre> % Spends by Payment Method = 
VAR TotalSpendsPerCategory = 
    CALCULATE(
        SUM(fact_spends[spend]), 
        ALLEXCEPT(fact_spends, fact_spends[category])  -- Keeps only the Spending Category filter
    )
RETURN 
DIVIDE(SUM(fact_spends[spend]), TotalSpendsPerCategory, 0)
</pre>
   
  </li>
  <li>
    Rank Category = RANKX(ALL(fact_spends[category]),[Total spends],,DESC,Dense)
  </li>
  <li>
    <pre>
       Top Categories = 
var selected_top =SELECTEDVALUE('Cat'[tops])
var top_categores = SWITCH(selected_top, 
"Top 2", if([Rank Category] <= 2,[Total spends]), 
"Top 3", if([Rank Category] <= 3,[Total spends]), 
"Top 4", if([Rank Category] <= 4,[Total spends]), 
"Top 5", if([Rank Category] <= 5,[Total spends]), 
"Top 6", if([Rank Category] <= 6,[Total spends]), 
[Total spends])

return top_categores
    </pre>
   
  </li>
</ol>
