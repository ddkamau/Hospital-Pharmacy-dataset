# Hospital-Pharmacy data analysis
##Data Cleaning

- Set all the columns with the correct data types eg: ID columns set to text, dates set to date, numbers set to whole number, texts set to text. Unit price and cost set to fixed decimal number. 

- **Trimmed** and **cleaned** all the columns

- There were no missing values and errors to correct

- Removed duplicates if any for the _patient ID, Visit ID & Transaction ID_ in the _Patients, Visits & Pharmacy Transactions_ respectively 

- **Capitalised** each word in cells

- Parsed through the data to check for negative values like age, quantity & cost. None found

- Added a new column, _Calculated total cost_=_(Unit price) * (Quantity)_. Parsed through and saw that there were no differences with the given _total cost_, so deleted the new column

##Data Modelling

- For this dataset, my **fact tables** are: _Visits and Pharmacy transactions_ as they contain quantitative data while my **dimension table** is _patients_ as it contains descriptive data.

- The **unique identifiers** in _patients_ table is _patientID_ for _visits_ is _visitID_ for _pharmacy transactions_ is _transactionID_

- To create the **relationships** link patientID in _patients_ and _visits_ tables. This relationship is many-to-one from _visits _to _patients_ table or vice versa. Reveals that one patient can have many visits in the hospital. For _pharmacy transactions_ to _visits_, the connecting column is _visitID_ and it's many-to-one relationship or vice versa. This reveals that one visit can have many transactions but not vice versa.

- Created another table called _DimDrug_ that contains _Drug Name, Drug Category & Drug ID_. Referenced the _pharmacy transactions_ table and went ahead to delete the other columns and remained with _Drug Name & Drug Category_. Added an indexed column and formatted a prefix "DRG" to come up with the column _Drug ID_ eg "DRG 1". Removed duplicates hence remained with six drug names and drug categories. 

- Created another table _DimDate_ from refernced table _Visits_. Deleted all columns and remained with the _visit date_ column. I added columns from **date & time** to return columns _month, month name, quatre & year_ 

- Created 2 tables from the _visits_ table namely _FacVisits & DimVisits_. _FacVisits_ is a fact table containing _patientID, VisitID, Visit date & Length of stay days_ these are measurable data hence fact table. The _DimVisits_ table contains _VisitID, Diagnosis & Department_, these are descriptive data hence dimension table

- In the _Pharmacy Transactions_ table, added _Drug ID_ by adding a **conditional column** and using an **IF & Else** statement that returns the correct drug ID paired to the drug name from the _DimDrug_ table. 

- Merged queries using _visitID_ both in _Pharmacy Transactions & Visits_ table using **Left outer merge**. Added _Patient_ID_ from _Visits_ table into _Pharmacy Transactions_. From _Ptaient_ to _Pharmacy transactions_, _Patient ID_ used as one to many query.

- For the relationships:
_(Visit_ID)Visit table_ - _(Visit_ID)Pharmacy transactions_ = one-many

- _(Visit_ID)FacVisits_ - _(Visit_ID)Pharmacy transactions_ = one-many

- _(Visit_ID)DimVisits_ - _(Visit_ID)Pharmacy transactions_ = one-many

- _(Visit_ID)DimDate_ - _(Visit_ID)Pharmacy transactions_ = one-many

- _(Drug_ID)DimDrug_ - _(Drug_ID)Pharmacy transactions_ = one-many

- _(Visit_ID)Visit table_ - _(Visit_ID)Pharmacy transactions_ = one-many

- _(Patient_ID)Patient_ - _(Patient_ID)Pharmacy transactions_ = one-many

- (Visit_ID)Visit table_ - _(Visit_ID)FacVisists_ = one-one

- (Visit_ID)Visit table_ - _(Visit_ID)DimDate_ = one-one

- (Visit_ID)Visit table_ - _(Visit_ID)DimVisits_ = one-one

##Data Analysis
**To visualise the diseases most common across counties**, used a **matrix table**. The rows had the _county_ from the _patients_ table, and the columns were the _diseases_ from the _visits_ table. The value field was the count of _visitID_. Formatted the cells to show red colour for the max values, white colour for middle and blue colour for the minimum values. From the chart, **Typhoid & Diabetes are common in Kiambu, Hypertension is most common in Kisumu, Diabetes is most common in Mombasa, Pneumonia is most common in the capital city and the flu is most common in Nakuru and Uasin Gishu.** 

- **To visualise which departments generate a higher pharmacy revenue** - used a pie chart with the value being the **total cost** & the legend being the **department**. From the charts, **Inpatient>>>Emergeny>>>Outpatient**

- **To visualise which age groups consume the most drugs** - used a stacked bar chart. Used a **Switch** function to come up with a column that will group ages into:
<1yr - Infant
1-14yrs - Child
15-44yrs - Young adult
45-59yrs - Middle Age
60-74yrs - Elderly
>75yrs - Senior
ie 

```
Age_Group = SWITCH(
    TRUE(),
    [Age] < 1, "Infant",
    [Age] >= 1 && [Age] <= 14, "Child",
    [Age] >= 15 && [Age] <= 44, "Young Adult",
    [Age] >= 45 && [Age] <= 59, "Middle-age",
    [Age] >= 60 && [Age] <= 74, "Elderly",
    [Age] >= 75, "Senior",
    "Unknown")
```
 Used a stacked bar chart of age group vs the drug count. 
From the chart, **young adults use more drugs compared to other age groups **

- **To visualise if a high number of patients always leads to higher pharmacy revenue,** I used a combo chart. Came up with a new measure `Total Visits=Total Visits = COUNTROWS(Visits)`. This is the sum of visits made to the facility. The columns were the total cost and the line was the total visits and the x-axis was the months. For statistical correctness, plotted a scatter plot where x-axis was total visits, y-axis was total cost and value was department. From the charts, **we can conclude that high numbers of patients leads to a higher pharmacy revenue, evidenced by the peak months of July & August. The scatter plot has a positive gradient, confirming the inference.**  

- **To visualise if some diagnosis are associated with longer hospital stay but less pharmacy spending**, plotted a combo chart for this data. The x-axis was diagnosis, the column was the total cost, and the line was the total length of stay. From the chart, **typhoid is associated with longer hospital stay and higher pharmacy spending, while the flu is associated with a longer hospital stay but little pharmacy spending**.   

## Dashboard
KPI cards were:

- Total visits - _Total visits_ measure ie `Total Visits = COUNTROWS(Visits)`

- Total Pharmacy revenue - Total revenue measure ie `Total Revenue = sum(Pharmacy_Transactions[Total_Cost])`

- Average days spent - Average days stayed ie `Average days stayed = AVERAGE(Visits[Length_of_Stay_Days])`
Plotted chars showing: 

- Diseases trend over time - A line chart where x-axis is quarter y axis is count of diagnosis and legend is diagnosis

- Pharmacy cost breakdown by category - A stacked bar chart where x axis is diagnosis and y axis is total revenue

- County and Department comparisons - A stacked bar chart where x axis is county and y axis is count of visits and the legend is departments. 
Slicers for county, department and visit date 
