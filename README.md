# OOPS-in-BigQuery

#### Implementing the OOPS in Bigquery to easily modify and maintain the code over time.

#### Prerequisite : Hands-on experience in BigQuery

#### What is OOPS?

- OOP stands for Object Oriented Programming.
- It is not just a programming language, but a paradigm (An example or model used to explain a concept or theory).
- OOP does not tell you how to program, rather it tells you how to go about designing your software. 
- When you are developing Object-oriented programs/software the emphasis is more on how you think about and design the software   rather that on actually implementing(coding) it.

#### What is object?
In the real world, an object is anything that is well-defined; say a car, a bat, a ball, a door, etc. An object has well-    defined characteristics. These characteristics make one object (class of objects) easily differentiable from others.

#### Where we can use:
- Code reuse and recycling - objects created for OOP can be easily reused in other programs.
- Maintenance of project becomes easy, as minor change in definitions require little modification rather than complete change  in whole project.

![alt text](https://drive.google.com/uc?id=1JN8GF8sAeOMBe3snMXRbNLnhoSqIiWn4)
         (Single object is reused in multiple programs)
         
#### How to Implement?
#### Example :
Consider the below example where we want to find the Last Year Fiscal Date (LY) correspond to the This Year Fiscal Date (TY) in multiple queries. But, the duration between the “Last Year” and “This year” dates is not constant i.e it can be either 364 days or 371 days.

( Note : Please refer the fiscal calendar 454 for more details )

| This Year week | Last Year week |Time difference |
| ---------------| -------------- |----------------|
| 2018-02-18     | 2017-02-19     |  371 DAY       |
| 2018-01-31     | 2017-01-01     |  364 DAY       |

Hardcoding of this data directly into the main query may cause the troubles for future as we will have to alter the duration every now and then in multiple queries which is not feasible and it may cause the manual errors as well.

#### Step 1: Creating object ( separate view)
Write the object (source code) as separate view in BigQuery which we may need to modify in future.
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
#### step 2: How we can add this to main code:
- Simply, join the source code with the main code.
- So that the changes in the source code will be reflected in all the main codes where we have implemented this.
