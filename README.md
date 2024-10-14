# AtliQo Telecom Analysis - PowerBI

### Dashboard Link : https://app.powerbi.com/groups/me/reports/6bdce94a-54c2-4edc-8c46-c91fc8030bf3/26ccf74cd25dbb77778b?experience=power-bi

## Problem Statement

AtliQo is one of the leading telecom providers in India and launched it’s 5G plans in May 2022 along with other telecom providers.

However, the management noticed a decline in their active users and revenue growth post 5G launch in May 2022. Atliqo’s business director requested their analytics team to provide a comparison report of KPIs between pre and post-periods of the 5G launch. The management is keen to compare the performance between these periods and get insights which would enable them to make informed decisions to recover their active user rate and other key metrics. They also wonder if they can optimize their internet plans to get more active users. Peter Pandey, a junior data analyst is assigned to this task.

##  Task:
Imagine yourself as Peter Pandey and perform the following task

Create the comparison report based on the mock-up provided. Please note the mock-up is created by a business user who has a minimal idea about dashboarding. Hence you need to represent the insights in a much better way
The target audience of this dashboard is top-level management - hence the dashboard should be self-explanatory and easy to understand
Create relevant insights not provided in the metric list/mock-up dashboard to support the cause.

## Data Model
 
 ![Data Model](https://github.com/user-attachments/assets/a8dcfbf3-16d0-4ba8-a8cf-a0bed91a6949)


## Dashboard Design

 ![Dashboard Design](https://github.com/user-attachments/assets/43eb8c68-c00c-432d-864d-a12d920a1344)



## Steps followed 

## Data Loading and Power Query Documentation

1. Create a folder in Desktop and store all the csv files related to hospitality challenge.
2. Open a Power BI file, and In "Get Data", select the option as folder and browse through the folder containing csv files.
3. Then go to Transform data to expand each and every dataset.
4. Now, duplicate the data source 4 times and in each one, expand one dataset by clicking on "Binary" option. also, rename the tables accordingly.

## Power Query steps for tables
1. dim_date:
	- remove the column 'day_type'
	- we are deleting this because, we got a feedback from the mock dashboard review that Friday and Saturday are considered as weekends in the industry and not sunday. But In our datasets, saturday and sunday are considered as weekends. So we delete this column and re-create day_type using calculated columns.
2. dim_rooms
	- The column names are not captured here. We need to select "Use First Row as Headers" option .

## New Metrics Added
 
## Calculated column was created as Follows:

1.Week Number (wn):
	wn = WEEKNUM(dim_date[date])
	(from the dim_date table)

2.Day Type (Weekday or Weekend):
	day type = 
	VAR wkd = WEEKDAY(dim_date[date], 1)
	RETURN
	IF(wkd > 5, "Weekend", "Weekday")
	(from the dim_date table)

## For creating new measures following DAX expression was written;
 1.Revenue = SUM(fact_bookings[revenue_realized])
(from the fact_bookings table)

2.Total Bookings = COUNT(fact_bookings[booking_id])
(from the fact_bookings table)

3.Total Capacity = SUM(fact_aggregated_bookings[capacity])
(from the fact_aggregated_bookings table)

4.Total Successful Bookings = SUM(fact_aggregated_bookings[successful_bookings])
(from the fact_aggregated_bookings table)

5.Occupancy % = DIVIDE([Total Successful Bookings], [Total Capacity], 0)
(from the fact_aggregated_bookings table)

6.Average Rating = AVERAGE(fact_bookings[ratings_given])
(from the fact_bookings table)

7.No. of Days = DATEDIFF(MIN(dim_date[date]), MAX(dim_date[date]), DAY) + 1
(from the dim_date table)

8.Total Cancelled Bookings = CALCULATE([Total Bookings], fact_bookings[booking_status] = "Cancelled")
(from the fact_bookings table)

9.Cancellation % = DIVIDE([Total Cancelled Bookings], [Total Bookings])
(from the fact_bookings table)

10.Total Checked Out = CALCULATE([Total Bookings], fact_bookings[booking_status] = "Checked Out")
(from the fact_bookings table)

11.Total No Show Bookings = CALCULATE([Total Bookings], fact_bookings[booking_status] = "No Show")
(from the fact_bookings table)

12.No Show Rate % = DIVIDE([Total No Show Bookings], [Total Bookings])
(from the fact_bookings table)

13,Booking % by Platform = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(fact_bookings[booking_platform]))) * 100
(from the fact_bookings table)

14.Booking % by Room Class = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(dim_rooms[room_class]))) * 100
(from the fact_bookings and dim_rooms tables)

15.ADR = DIVIDE([Revenue], [Total Bookings], 0)
(from the fact_bookings table)

16.Realization % = 1 - ([Cancellation %] + [No Show Rate %])
(from the fact_bookings table)

17.RevPAR = DIVIDE([Revenue], [Total Capacity])
(from the fact_bookings and fact_aggregated_bookings tables)

18.DBRN = DIVIDE([Total Bookings], [No. of Days])
(from the fact_bookings and dim_date tables)

19.DSRN = DIVIDE([Total Capacity], [No. of Days])
(from the fact_aggregated_bookings and dim_date tables)

20.DURN = DIVIDE([Total Checked Out], [No. of Days])
(from the fact_bookings and dim_date tables)

21.Revenue WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([Revenue], dim_date[wn] = selv)
VAR revpw = CALCULATE([Revenue], FILTER(ALL(dim_date), dim_date[wn] = selv-1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_bookings and dim_date tables)

22.Occupancy WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([Occupancy %], dim_date[wn] = selv)
VAR revpw = CALCULATE([Occupancy %], FILTER(ALL(dim_date), dim_date[wn] = selv-1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_aggregated_bookings and dim_date tables)

23.ADR WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([ADR], dim_date[wn] = selv)
VAR revpw = CALCULATE([ADR], FILTER(ALL(dim_date), dim_date[wn] = selv-1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_bookings and dim_date tables)

24.RevPAR WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([RevPAR], dim_date[wn] = selv)
VAR revpw = CALCULATE([RevPAR], FILTER(ALL(dim_date), dim_date[wn] = selv-1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_aggregated_bookings and dim_date tables)

25.Realization WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([Realization %], dim_date[wn] = selv)
VAR revpw = CALCULATE([Realization %], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_bookings and dim_date tables)

26.DSRN WoW Change % = 
VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
VAR revcw = CALCULATE([DSRN], dim_date[wn] = selv)
VAR revpw = CALCULATE([DSRN], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
RETURN
DIVIDE(revcw, revpw, 0) - 1
(from the fact_aggregated_bookings and dim_date tables)

## Insights

Revenue is declined by 0.5 % compared to the previous network, which is mainly caused by Delhi, Chennai, Ahmedabad cities.
Even though there is a decline in the revenue, ARPU (Average revenue per user) has increased from 190.2 to 211.3 rupees.
AtliQo maintains third position in the market always without any change.
Plan 1,2 and 11 are the highly performing plans among all the plans
Pune has 18% growth in active users despite having 55% increase in the unsubscribed users
