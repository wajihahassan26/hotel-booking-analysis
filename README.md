# hotel-booking-analysis
Power BI case study analyzing drivers of hotel booking cancellations

## Overview
This project analyzes hotel booking data to identify key factors driving booking cancellations using a dimensional data model and Power BI.

## Business Question
What factors drive booking cancellations?

## Data Model
- Fact table: fact_bookings
- Dimensions: dim_date, dim_market_segment, dim_customer
- Star schema with single-direction relationships

## Key Metrics
- Total Bookings
- Cancellation Rate
- Revenue at Risk
- Average Lead Time

## Key Insights
- Long lead-time bookings have significantly higher cancellation rates
- Online travel agents contribute the most cancellations
- Certain room types and peak seasons are more cancellation-prone

## Tools Used
- Power BI (Power Query, DAX)
- GitHub

## Notes
Revenue is estimated using ADR multiplied by total nights.
