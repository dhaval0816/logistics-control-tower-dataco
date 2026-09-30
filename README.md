# Logistics Control Tower (DataCo supply chain)

A Power BI and Excel project I built on the DataCo Smart Supply Chain dataset from Kaggle.

The question I started with: customers complain about late deliveries, and the COO thinks premium shipping isn't giving customers what they pay for. Where do the delays come from, and what do they cost?

## What I found

All numbers below leave out cancelled orders (see "Decisions I made").

1. **Premium shipping is the problem, not the fix.** First Class was on time 0% of the time. It promises 1 day and always takes 2. Second Class was on time 20% of the time, Same Day 51.6% and Standard 60.2%. So the cheapest option is the most reliable one.
2. **It's not a geography problem.** Late % is between 56.5% and 57.7% in every market. When every region looks the same, the cause is inside the business. Here it's the delivery promise set for each shipping mode.
3. **A lot of money is tied to late orders.** Overall on-time delivery is 42.7% (36,048 late orders out of 62,897). $18.1M of $31.6M in revenue came from late orders. The margin is 12.0% and 21.1% of orders lose money.

My recommendation is to reset the promised days for First and Second Class to what the network really delivers, or stop charging a premium for them until they hit a target.

## What's in this repo

| File | What it is |
|---|---|
| `Logistics_Control_Tower_PBIP.zip` | The Power BI report as a PBIP project (unzip it and open `Logistics_Control_Tower.pbip`) |
| `DAX_measures.dax` | Every measure in the model, so you can read them without opening Power BI |
| `logistics_theme.json` | My Power BI theme |
| `country_names_en.csv` | Small lookup I made to translate the Spanish country names |
| Excel workbook | In the Releases section (the file is too big for the main page) |

## The dashboard

Five pages:

1. Control Tower Overview: KPI cards, monthly trend against a 90% target, on-time % by shipping mode
2. Delivery by Shipping Mode: promised vs actual days, delay mix, mode by market matrix
3. Geographic Performance: map by country, market comparison
4. Profitability and Loss Orders: margin by category, loss orders, revenue at risk
5. Root-Cause Explorer: decomposition tree and key influencers

There are also bookmarks (premium modes only, late orders only, last 12 months, reset), a drill-through page for order detail and a tooltip page.

Screenshots coming soon.

## The data model

Star schema with one fact table (`Fact_OrderLines`, one row per order line) and five dimensions: Customer, Product, Shipping Mode, Geography and Date.

Date connects to Order Date (active) and Ship Date (inactive, used with USERELATIONSHIP).

I did all the cleaning in Power Query by clicking through the UI, not by writing code. I added Delay Days, Delay Bucket and Is Cancelled as columns there.

## Decisions I made

- **On-Time Delivery %, not OTIF.** The data has no "in full" field, so calling it OTIF would be wrong.
- **Cancelled orders are left out of delivery KPIs.** Their late flag is always 0 even when they were delayed, which made on-time look better than it was. I show them as their own count (2,855 orders).
- **Revenue means Order Item Total**, which is after discount. The Sales column is before discount, so I only use it as "Gross Sales".
- **Loss orders are counted per order, not per line.** It's 21.1% per order and 18.7% per line, so mixing them up changes the story.
- I dropped personal fields (names, email, password, street) and columns that were empty or exact copies of other columns.

## Problems I hit

- The CSV has to be opened with encoding 1252, or customer and city names come out broken.
- Order Country is in Spanish, so the map put some countries in the wrong place. I fixed it with a small lookup table.
- On-Time % showed 100% for months with no orders (1 minus blank). I added a blank check to fix it.
- Order lines drop by about 57% from October 2017. Orders didn't really drop though. Almost every order became a single line. I added a note on the trend chart so nobody reads it as a crash in sales.
- At first I had First Class at about 95% late. That was before I took out cancelled orders. With them out, it's 100%.

## How to open it

1. Download the dataset from Kaggle: [DataCo Smart Supply Chain for Big Data Analysis](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis). I didn't upload it here because it's about 90 MB.
2. Put `DataCoSupplyChainDataset.csv` and `country_names_en.csv` in one folder.
3. Unzip `Logistics_Control_Tower_PBIP.zip` and open `Logistics_Control_Tower.pbip` in Power BI Desktop. It opens with the data already loaded.
4. If you want to refresh it, go to Transform data > Edit parameters and set `DataFolder` to your folder (keep the `\` at the end).

## Tools

Excel (Microsoft 365), Power BI Desktop, Power Query, DAX.
