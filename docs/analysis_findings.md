# Olist E-Commerce — Analysis Findings

**Source:** Brazilian E-Commerce Public Dataset by Olist (historical records, 2016–2018)  
**Tools:** Excel, Power Query (M), Power Pivot (DAX), PivotTables and PivotCharts  
**Reporting note:** Findings below use full-dataset figures unless explicitly marked. The dashboard's Timeline and state slicer change the displayed KPIs. The monthly trend visual intentionally focuses on Jan 2017–Aug 2018 because boundary months are sparse.

## F-001 — Delivery performance and customer satisfaction

**Status:** Validated observational association; not causal  
**Finding:** On-time deliveries averaged **4.29/5** in customer reviews; late deliveries averaged **2.27/5**, a difference of approximately **2.02 points**.  
**Validation:** The Reviews source has **99,224 review records** covering **98,673 distinct reviewed orders**. Review-level and order-level averages were compared; the on-time/late pattern persisted to two decimal places when each reviewed order was weighted equally.  
**Business implication:** Late deliveries are associated with lower customer satisfaction.  
**Recommended action:** Investigate logistics failures in high-volume or high-delay regions; track review outcomes after interventions.  
**Limits:** Reviews are missing for some orders; correlation does not prove causation. Different delivery-status groups can have other differences.

## F-002 — Geographic variation in late deliveries

**Status:** Descriptive finding  
**Finding:** Maranhão (MA) recorded a **17.43% late-delivery rate** across **717 evaluated deliveries**, compared with **4.50%** across **40,480 evaluated deliveries** in São Paulo (SP). Rio de Janeiro (RJ) had **1,495 late deliveries** across **12,349 evaluated deliveries** (**12.11%**).  
**Business implication:** The state with the highest rate is not necessarily the state with the highest number of affected orders.  
**Recommended action:** Examine MA for relative reliability and RJ for high absolute delayed-order volume. Segment further by period and shipping method where data allow.  
**Limits:** Regional chart uses a 500+ evaluated-delivery threshold; filter context can change eligibility. The data do not identify the cause of each late delivery.

## F-003 — Sales concentration by product category

**Status:** Descriptive finding  
**Finding:** Health & Beauty generated approximately **R$1.26 million** in item sales, followed by Watches & Gifts at about **R$1.21 million**. Total recorded item sales across all categories were **R$13,591,643.70**.  
**Business implication:** Category contribution is driven by both item volumes and average item prices.  
**Recommended action:** Compare Item Sales, Items Sold, and Average Item Price side-by-side before prioritizing categories.  
**Limits:** Item Sales is the sum of Order Items `price`, excluding freight. It is not proven to equal Olist company revenue or profit.

## F-004 — Historical monthly sales coverage

**Status:** Data coverage limitation  
**Finding:** Very low observed item sales in sparse boundary months can make a full-period line chart look like a collapse.  
**Recommended presentation:** Show **Jan 2017–Aug 2018** on the monthly trend and identify the restricted period in the chart caption. Preserve all observations in the original model and show the filtered context for KPI cards separately.  
**Limits:** Excluding sparse months is a visualization decision, not evidence of the platform's real-world activity outside the sample.

## Data quality findings / handling

| ID | Issue | Count | Handling |
|---|---|---:|---|
| DQ-001 | Customer delivery timestamp precedes carrier handoff | 23 | Flag for investigation; preserve original timestamps |
| DQ-002 | Orders marked delivered lack a customer delivery timestamp | 8 | Keep missing; exclude from calculations requiring valid timestamp |
| DQ-003 | Zero-valued payment records | 9 | Preserve zero; inspect payment type and order status |
| DQ-004 | Missing approval timestamps | 160 | Preserve null; review by order status |
| DQ-005 | Review records in excess of distinct reviewed orders | 551 | Not presumed invalid duplicates; compare per-record and per-order average |

See `Olist_Data_Quality_and_Methodology.xlsx` for detailed issue definitions, checks and KPI methodology.

## Model and validation notes

- `Orders.order_id` is an order-level key. `Order Items` is item-line grain; payment records and reviews can repeat by order.
- `Customers.customer_id` is an order-specific record; `customer_unique_id` identifies a real-world customer across repeat orders.
- Left Anti Join checks are appropriate for detecting missing foreign-key matches; duplicate-key checks are separate.
- Referential integrity and uniqueness should be rechecked after source refresh; documented previously observed pass results should not be treated as live refresh validation.
- A state or month slicer changes the subset being analyzed. Do not present a filtered KPI as if it is the entire data period.
