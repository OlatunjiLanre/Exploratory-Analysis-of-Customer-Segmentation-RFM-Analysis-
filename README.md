# Exploratory-Analysis-of-Customer-Segmentation-RFM-Analysis-


### Business Request?

The organization is looking to lunch a new campaign on all products. We need you to segment our customers by finding the best and the worse group of customers for the campaign.

### Stakeholders Requirements?

1. Segment customers based on transaction history.
2. Identify top customers for targeted marketing.
3. Gain insights into customer segmentation to inform strategic decisions

### Technique Used: RFM Analysis

#### What is **RFM** Analysis?
RFM Analysis stands for Recency, Frequency and Monetary.
It is an indexing technique that uses past purchase behaviour to segment customers. 

An RFM report is a way of segmenting customers using three metrics;

-Recency (How long ago was the customer’s last purchase).

-Frequency (How often the customers purchase from us).

-Monetary Value (How much a customer spends).


**The Query**

```sql

--Customer Segmentation(RFM Analysis)

-- Data Filtering

SELECT 
	order_id,
	order_date,
	customers_name,
	sales
FROM [dbo].[sample]


--Calculating my Recency,Frequency and Monetary Values

WITH RFM_Value AS (
	SELECT 
		customers_name,
		COUNT(order_id) AS Frequency,
		SUM(sales) AS Monetary,
		MAX(order_date) AS Last_order_date,
		(SELECT MAX(order_date) FROM [dbo].[sample]) AS Max_ordr_date,
		DATEDIFF(DD, MAX(order_date), (SELECT MAX(order_date) FROM [dbo].[sample])) AS Recency
		
	FROM [dbo].[sample]
	GROUP BY customers_name
),

--Customers Ratings based on Recency,Frequency and Monetary
	RFM_Score as (
SELECT 
	customers_name,
	Recency,
	Frequency,
	Monetary,

	NTILE(5) OVER (ORDER BY Recency DESC) AS R,
	NTILE(5) OVER (ORDER BY Frequency) AS F,
	NTILE(5) OVER (ORDER BY Monetary) AS M
		

FROM RFM_Value

--RFM Score
),
RFM_Segment AS (

	SELECT 
	customers_name,
	Recency,
	Frequency,
	Monetary,
	R,F,M,
				
		Cast (R as varchar)+ Cast(F as varchar)+ Cast(M as varchar)as rfm_score
	

	FROM RFM_Score
)

--Customers RFM segmentation 
	SELECT
	customers_name,
	Recency,
	Frequency,
	Monetary, 
	R,F,M,
    rfm_score,
		case
		when rfm_score in (213, 221, 231, 241, 251, 312, 321, 331) then 'About to Sleep'
		when rfm_score in (124,125,133,134,135,142,143,145,152,153,224,225,234,235,242,243,244,245,252,253,254,255	) then 'At Risk'
		when rfm_score in (113,114,115,144,154,155,214,215) then 'Can Not Lose Them'
		when rfm_score in (445,454,455,544,545,554,555) then 'Champions'
		when rfm_score in (122,123,132,211,212,222,223,231,232,233,241,251,322,332) then 'Hibernating Customers'
		when rfm_score in (111,112,121,131,141,151) then 'Lost Customers'
		when rfm_score in (335,344,345,354,355,435,444,543) then 'Loyal Customers'
		when rfm_score in (324,325,334,343,434,443,534,535) then 'Need Attention'
		when rfm_score in (311,411,412,421,422,511,512) then 'New Customers'
		when rfm_score in (323,333,341,342,351,352,353,423,431,432,433,441,442,451,452,453,531,532,533,541,542,551,552,553) then 'Potential Loyalist'
		when rfm_score in (313,314,315,413,414,415,424,425,513,514,515,521,522,523,524,525) then 'Promising '
																		
	end as rfm_segment 
	FROM RFM_Segment

```




