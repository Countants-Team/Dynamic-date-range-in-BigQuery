# Dynamic date range in BigQuery

We come across various scenarios in database system where we want to compare two dynamic date ranges. 

Hardcoding of dates directly into the main query may cause the troubles for future as we will have to alter the duration every now and then in multiple queries which is not feasible and it may cause the manual errors as well.
    
This can be solved by creating the source view separately which cab be then joined with the main code. So that, any changes made in the source code will be reflected in the main code and can be resued in multiple programs.

#### Example :
Consider the below example where we want to find the Last Year Fiscal Date (LY) correspond to the This Year Fiscal Date (TY) in multiple queries. But, the duration between the “Last Year” and “This year” dates is not constant i.e it can be either 364 days or 371 days.

( Note : Please [refer](https://docs.google.com/spreadsheets/d/1X1OqY_XK_q3AFaSwB9y_XDSphMEB2IXa1wQE7jiRfx0/edit?usp=sharing) the fiscal calendar 454 for more details )

| This Year week | Last Year week |Time difference |
| ---------------| -------------- |----------------|
| 2018-02-18     | 2017-02-19     |  371 DAY       |
| 2018-01-31     | 2017-01-01     |  364 DAY       |


##### Single view is reused in multiple programs as shown below:

![alt text](https://drive.google.com/uc?id=1JN8GF8sAeOMBe3snMXRbNLnhoSqIiWn4)
        
#### How to Implement?

#### Step 1: source code (separate view)
Write separate view in BigQuery which includes the dynamic date range.
View to find TY and LY date
```
select a.time, TY, LY from
(
(select distinct fiscal_week_start_date as TY, 'Week' as time from
dataset.project.dim_date_454`
where date(fiscal_week_start_date) = DATE_ADD(current_date(), INTERVAL -EXTRACT(DAYOFWEEK FROM current_date())-6 DAY)) as a
JOIN
(select distinct fiscal_week_start_date as LY, 'Week' as time from
dataset.project.dim_date_454`
where date(fiscal_week_start_date) = DATE_ADD(current_date(), INTERVAL -EXTRACT(DAYOFWEEK FROM current_date())-6 - 364 DAY)) as b
on a.time = b.time)
```
#### Step 2: How we can add this to main code
- Simply, join the source code with the main code.
- So that the changes in the source code will be reflected in all the main codes where we have implemented this.
