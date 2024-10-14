# AtliQo Telecom Analysis - PowerBI

### Dashboard Link : https://app.powerbi.com/groups/me/reports/6bdce94a-54c2-4edc-8c46-c91fc8030bf3/26ccf74cd25dbb77778b?experience=power-bi

## Problem Statement
Atliq Grands owns multiple five-star hotels across India. They have been in the hospitality industry for the past 20 years. Due to strategic moves from other competitors and ineffective decision-making in management, Atliq Grands are losing its market share and revenue in the luxury/business hotels category. As a strategic move, the managing director of Atliq Grands wanted to incorporate “Business and Data Intelligence” in order to regain their market share and revenue. However, they do not have an in-house data analytics team to provide them with these insights.

Their revenue management team had decided to hire a 3rd party service provider to provide them insights from their historical data.

## Task List
You are a data analyst who has been provided with sample data and a mock-up dashboard to work on the following task. You can download all relevant documents from the download section.

Create the metrics according to the metric list.
Create a dashboard according to the mock-up provided by stakeholders.
Create relevant insights that are not provided in the metric list/mock-up dashboard

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

## Learnt things from this Project
	-Learnt to build a new visual (Calendar visual) using matrix table, which can be utilized for different purpose of analyze.

	-By referring different cancellation polices followed by different hotels, understood that most of the hotels charge zero fee, only if the booking is cancelled before three months of booking date. If the booking is cancelled after that, the charge range from 60 to 90% of the booking cost.

	-Learnt, how to use bookmarks and selection for different purposes. (Page navigation and clear filter button in the dashboard was achieved using bookmarks and selection.

## Some Important insights from the Dashboard
	-Mumbai generates the highest revenue (669 M) followed by Bangalore, Hyderabad and Delhi

	-AtliQ Exotica performs better compared to all 7 type of properties with 320 Million revenue, rating 3.62, occupancy percentage 57 and cancellation rate as 24.4%.
	
	-AtliQ Bay has the highest occupancy of 66%
	
	-Week 24 recorded the highest revenue among all, which is 139.6 Million
	
	-Delhi tops both in occupancy and rating followed by Hyderabad, Mumbai, Bangalore

	-AtliQ lost around 298 Million in cancellation

	-Elite type rooms has the most booking and as well higher cancellation rate
