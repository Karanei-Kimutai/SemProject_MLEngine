The EDA is giving us a useful picture of how demand behaves in the M5 dataset, and, importantly, it helps us decide what information the forecasting model should pay attention to.

The biggest finding is that retail demand is not consistent across products, stores or days. Many product-store combinations have long periods with no recorded sales, overall sales show a clear weekly pattern, and the relationships between sales and factors such as events, SNAP indicators and prices are not all equally clear.

Below, we’ve broken down each part of the EDA in simple language: what we wanted to investigate, what we found and what the findings mean for the project.

## 1. Understanding the dataset and checking its quality

What we wanted to find out

Before exploring demand patterns, we wanted to understand what information was available and check whether there were any obvious problems that could affect our analysis. For example, we needed to know whether there were missing values, duplicate records, invalid sales values or inconsistencies between the sales, calendar and price files.

What we found

The sales data contains 30,490 product-store combinations, covering 1,913 days from January 29, 2011, to April 24, 2016. Across these observations, the dataset records approximately 65.7 million units sold.

The main data-quality checks were satisfactory:

- There were no duplicate product-store combinations or sales IDs.
- All 1,913 sales-day identifiers had matching calendar entries, and the day sequence was continuous.
- No negative sales values or product-store combinations with zero sales throughout the entire observed period were found.
- The price file had no missing, zero or negative selling-price values, and its store-item-week keys were unique.

We also found that many event-related calendar fields were blank. For example, the first event-name field was blank on about 91.8% of calendar days, while the second was blank on about 99.7%. This is expected because events are recorded only on relevant dates; it does not mean that the sales data itself is missing.

One detail needs attention later: although the price table contains no missing price values within its records, not every product-store-week sales observation could be matched to a price record.

What this tells us

The dataset is suitable for continuing with the EDA. We do not need to remove valid zero-sales observations or invent values to fill the blank event fields. However, we must handle unmatched prices carefully when building the model.

## 2. Understanding how sales change over time

What we wanted to find out

We wanted to understand the overall movement of sales throughout the five-year period. Specifically, we were looking for long-term growth or decline, sudden changes and recurring patterns that might make future demand easier or harder to predict.

We examined daily sales alongside seven-day and 28-day rolling averages, as well as weekly and monthly sales totals. The rolling averages help us see the broader direction of sales without being distracted by every daily fluctuation.

What we found

The overall sales trend generally moved upwards over the period, although it was not a smooth increase. The rolling-average charts show periods of growth, periods of decline and recurring fluctuations.

Daily sales ranged from 11 units to 57,218 units across all products and stores. The mean daily total was approximately 34,342 units, while the median was 33,732 units.

We also noticed strong short-term fluctuations and very sharp drops around the end of the year. These are consistent with the extremely low sales recorded on certain dates, including Christmas.

The weekly sales chart shows the broader upward trend more clearly, alongside recurring fluctuations. The first and last weekly totals should be interpreted carefully because the observed period does not begin and end on complete weeks.

What this tells us

Past sales contain useful information about future demand, but a simple overall trend will not explain everything. The model should be able to learn from recent sales history while accounting for recurring patterns and unusual dates.

It also reminds us that looking only at total sales across all products can hide the behaviour of individual items. We therefore investigated differences between stores and product categories next.

## 3. Comparing stores, states and product categories

What we wanted to find out

We wanted to know whether demand is distributed evenly across the dataset or whether certain states, stores and product categories account for considerably more sales than others.

This matters because a forecasting approach that works well for frequently purchased products might not perform equally well for less frequently purchased items.

What we found

At state level, the total recorded sales were:

| State           | Total units sold |
| --------------- | ---------------- |
| California (CA) | 28,675,547       |
| Texas (TX)      | 18,899,006       |
| Wisconsin (WI)  | 18,120,856       |

California recorded the highest total. However, the dataset contains four California stores and three stores each in Texas and Wisconsin, so these totals should not be interpreted as a direct comparison of demand per store.

There were also differences among individual stores. For example, CA_3 recorded approximately 11.19 million units, making it the highest-selling store by total volume in the observed period. CA_4 recorded approximately 4.10 million units.

The product-category totals showed an even clearer difference:

| Category  | Total units sold |
| --------- | ---------------- |
| Foods     | 45,089,939       |
| Household | 14,480,670       |
| Hobbies   | 6,124,800        |

Food products accounted for approximately 68.6% of all recorded units. However, Foods also contains the largest number of product-store series, so total volume alone does not tell us how an individual product behaves.

When we compared typical product-store series, the median daily sales were highest for Foods, followed by Household and Hobbies. The average share of zero-sales days was also lower for Foods.

What this tells us

Demand differs substantially across stores and product categories. We should not assume that every product follows the same pattern.

For modelling, this supports investigating product, store and category identifiers as potential features. It also gives us a reason to examine forecasting performance separately across categories instead of relying entirely on one overall performance score.

## 4. Investigating how common zero-sales days are

What we wanted to find out

We wanted to understand how often each product-store combination recorded no sales on a particular day.

This is important because forecasting demand is different when a product sells almost every day compared with when it sells only occasionally. Frequent zero-sales observations can make patterns harder to learn and predictions more difficult to evaluate.

What we found

Out of approximately 58.33 million product-store-day observations, 39.78 million contained zero sales. This means that 68.2% of all daily observations recorded zero units sold.

The distribution across individual product-store combinations was also revealing:

| Condition                                      | Number of series | Percentage of series |
| ---------------------------------------------- | ---------------- | -------------------- |
| At least 25% of days had zero sales            | 28,930           | 94.9%                |
| At least 50% of days had zero sales            | 23,916           | 78.4%                |
| At least 75% of days had zero sales            | 14,534           | 47.7%                |
| At least 90% of days had zero sales            | 5,284            | 17.3%                |
| No recorded sales throughout the entire period | 0                | 0.0%                 |

The differences between categories were also substantial. Hobbies had the highest median zero-sales share at 83.2%, followed by Household at 77.6% and Foods at 65.2%.

What this tells us

The dataset contains a large amount of intermittent demand. Some products sell regularly, while others record sales on only a small portion of the observed days.

We should preserve zero-sales observations because they are part of the behaviour we are trying to forecast. We should also consider whether our model performs differently for frequently selling products and products with many zero-sales days.

One important limitation is that a zero in the dataset means that no sale was recorded. It does not tell us whether the item was unavailable, out of stock or simply not purchased.

## 5. Investigating how variable demand is

What we wanted to find out

We wanted to measure how much sales fluctuate for individual product-store combinations.

We used the coefficient of variation (CV), which compares the standard deviation of sales with their average. We calculated it across all days and separately for days with positive sales. We also calculated the average demand interval, which gives us a rough measure of how often positive sales occur.

We examined both versions of CV because frequent zeros can make a product appear much more variable than it is on the days when it actually sells.

What we found

Across all product-store series:

- The median CV across all days was 2.049.
- The median CV on positive-sales days was 0.590.
- The median average demand interval was approximately 3.78 days.

The difference between the two CV values is particularly important. Once we exclude zero-sales days, the typical measured variability is considerably lower.

The category breakdown reinforces this pattern:

| Category  | Median CV across all days | Median CV on positive-sales days | Median demand interval |
| --------- | ------------------------- | -------------------------------- | ---------------------- |
| Foods     | 1.792                     | 0.644                            | 2.88 days              |
| Hobbies   | 2.621                     | 0.506                            | 5.97 days              |
| Household | 2.165                     | 0.530                            | 4.46 days              |

Hobbies had the highest median variability when zero-sales days were included. However, its median CV on positive-sales days was lower than that of Foods. Its median demand interval was also longer: approximately six days compared with about three days for Foods.

What this tells us

Much of the apparent variability, especially in Hobbies, comes from how infrequently products sell rather than from enormous fluctuations in sales every time they sell.

This is useful for our research because it gives us a way to distinguish between products with different demand characteristics. We can investigate whether the model performs differently for low-volatility, high-volatility and intermittent-demand series.

We should not rely on CV alone, however. When a product's average sales are very low, CV can become large or unstable. That is why we interpret it alongside the zero-sales share and average demand interval.

## 6. Investigating calendar patterns and seasonality

What we wanted to find out

We wanted to determine whether demand follows recurring patterns according to the day of the week or the month of the year.

If these patterns are consistent, the calendar could provide useful information for forecasting future demand, even before we consider the more complicated factors affecting sales.

What we found

The day-of-week comparison showed a clear pattern:

| Day       | Mean daily units sold | Compared with the overall daily mean |
| --------- | --------------------- | ------------------------------------ |
| Saturday  | 41,547                | 21.0% higher                         |
| Sunday    | 41,130                | 19.8% higher                         |
| Monday    | 32,853                | 4.3% lower                           |
| Tuesday   | 30,369                | 11.6% lower                          |
| Wednesday | 30,010                | 12.6% lower                          |
| Thursday  | 30,205                | 12.0% lower                          |
| Friday    | 34,226                | Approximately average                |

Sales were considerably higher on Saturdays and Sundays, while Tuesday through Thursday generally recorded lower daily totals.

We also found differences across months. August had the highest average daily sales at approximately 35,947 units, while May had the lowest at approximately 32,504 units. September, July and June also had relatively high average sales.

The monthly-by-year chart suggests that overall sales levels changed over time, with many of the fuller years showing higher sales during parts of the middle of the year. However, the first and last years are incomplete, so comparisons involving those years require caution.

What this tells us

The weekly pattern is one of the clearest findings in the EDA. Knowing that a prediction is for a Saturday rather than a Wednesday could provide useful information to a forecasting model.

This supports testing calendar features such as weekday, month and other date-related information. It also gives us a reason to compare LightGBM against a simple weekly seasonal baseline, which uses recent sales from the same day of the week.

The monthly patterns may also be useful, although we need to test how much they improve the forecasts rather than assuming they will.

## 7. Investigating holidays and other recorded events

What we wanted to find out

We wanted to know whether sales behave differently on dates associated with events and holidays in the M5 calendar.

The idea was to see whether knowing about a recorded event could help explain unusual sales levels that would otherwise be difficult to predict using recent sales alone.

What we found

The dataset contained 154 days with a recorded event and 1,759 days without a recorded event during the observed sales period.

The average daily sales were approximately:

| Day type          | Mean daily units sold | Median daily units sold |
| ----------------- | --------------------- | ----------------------- |
| No recorded event | 34,489                | 33,714                  |
| Recorded event    | 32,655                | 33,780                  |

At first glance, the average was lower on event days. However, the median was slightly higher on event days, and the standard deviation was also higher: approximately 9,301 units on event days compared with 7,134 on days without a recorded event.

This tells us that event days had a different distribution of sales, rather than simply producing consistently higher or lower demand.

The individual event summaries also showed differences. Some events, such as Labor Day and the Super Bowl, had relatively high average sales. Others, including Christmas, had very low recorded sales. However, individual events occurred on only a small number of dates in this dataset, generally five or six occurrences in the displayed results.

What this tells us

Recorded events may help explain some unusual sales patterns, but the results do not show that events consistently increase or decrease sales.

Other factors, such as the weekday, time of year and the broader sales trend, may also explain the differences. The event analysis is descriptive, so we cannot conclude that an event caused a particular sales outcome.

Event information is therefore worth testing as a potential feature. We should judge its value using validation results rather than assuming that adding more event features will automatically improve the model.

## 8. Investigating SNAP indicators

What we wanted to find out

The M5 calendar includes SNAP indicators for California, Texas and Wisconsin. These identify dates on which the relevant state's SNAP indicator was active.

We wanted to see whether total sales in each state differed between dates when its SNAP indicator was active and dates when it was inactive.

What we found

In all three states, the mean daily sales total was higher on dates when the corresponding SNAP indicator was active.

| State      | SNAP inactive | SNAP active | Approximate difference |
| ---------- | ------------- | ----------- | ---------------------- |
| California | 14,606        | 15,772      | 8.0% higher            |
| Texas      | 9,519         | 10,613      | 11.5% higher           |
| Wisconsin  | 8,839         | 10,764      | 21.8% higher           |

The difference was largest in Wisconsin, where the average daily total was approximately 21.8% higher on dates with an active indicator.

What this tells us

SNAP indicators may contain useful information for forecasting demand. The fact that the same general pattern appears in all three states makes the relationship worth investigating further.

However, these figures are comparisons of overall state-level daily sales. We have not isolated the effect of SNAP from other differences between dates. Therefore, we should not say that an active SNAP indicator caused the increase in sales.

A reasonable next step is to include the appropriate state-specific indicator in the model and test whether it improves forecasting accuracy.

## 9. Investigating selling prices and their relationship with sales

What we wanted to find out

We wanted to understand the range of selling prices in the dataset and see whether changes in price appear to coincide with changes in sales.

This could help us decide whether price information should be included among our forecasting features.

Because prices are recorded weekly while sales are recorded daily, we first aggregated sales to weekly totals for a sample of product-store combinations and matched them to the corresponding weekly prices.

What we found

The selling-price distribution was uneven, with many products having relatively low prices and a smaller number having much higher prices.

| Price statistic       | Value  |
| --------------------- | ------ |
| Mean selling price    | 4.41   |
| Median selling price  | 3.47   |
| 75th percentile       | 5.84   |
| 90th percentile       | 8.64   |
| 99th percentile       | 17.92  |
| Maximum selling price | 107.32 |

The weekly sales-price comparison covered a sample of 60 product-store series across the categories. Approximately 83.2% of the sampled weekly sales observations matched a price record.

For the within-series correlation analysis, 45 series produced results. The mean correlation was −0.043, the median was −0.012, and individual correlations ranged from −0.519 to 0.806.

What this tells us

There was no strong overall relationship between price and sales in this sample. The median correlation was close to zero, while the individual series showed mixed results: some had negative relationships and others positive ones.

This does not mean price is useless. The relationship may differ between products, and a simple correlation does not capture every way that price information could help a model.

We should therefore test price as a feature rather than automatically include it or discard it. We must also ensure that the price used for each forecast was available at the prediction time, and that unmatched prices are handled correctly.

Importantly, the observed correlations do not show that price changes caused changes in sales.

## 10. What the EDA tells us about the forecasting model

Taken together, these investigations give us a clearer picture of what the model needs to learn.

The main observations are:

1\. Demand has a clear weekly pattern. Weekend sales are considerably higher than midweek sales in the aggregate data. Weekday information and recent weekly sales are strong candidates for forecasting features.

2\. Demand differs between products and stores. Food products account for the largest total sales volume, and individual stores have different sales totals. Product, store and category information may help LightGBM learn these differences.

3\. Zero sales are extremely common. Since 68.2% of observed product-store-day records contain zero sales, we need to preserve these observations and examine model performance across products with different levels of intermittent demand.

4\. Demand variability is not the same for every category. Hobbies products tend to have more zero-sales days and longer intervals between positive sales. We should investigate whether forecasting performance changes across demand patterns rather than evaluating the model only on all products combined.

5\. Calendar events and SNAP indicators are potential sources of information. Their observed relationships with sales justify testing them, but the current comparisons do not establish causation or prove that these features will improve predictions.

6\. Price information has mixed results. The sampled correlations do not show a strong overall relationship, so the value of price features needs to be established through model evaluation.

### How this informs our next steps

For the LightGBM model, the EDA supports investigating lagged sales, rolling sales summaries, calendar features, product-store identifiers, category information, and relevant price and SNAP features. It also suggests that our evaluation should look beyond a single overall score to see how the model performs across different demand patterns.

We should compare the model against a simple baseline, such as forecasting using sales from the corresponding day of the previous week. This will help us determine whether LightGBM actually learns useful patterns beyond those already visible in the sales history.

The important distinction is that EDA tells us what patterns exist and what is worth testing. It does not yet tell us which features or model will forecast best. That conclusion must come from our validation and test results, using a chronological split and making sure that information from the forecast period does not leak into the model's inputs.

Scope note: The conclusions above come from the executed EDA outputs in the uploaded notebook. The final preprocessing cell has no recorded output in the notebook, so this summary does not make claims about the results of that preprocessing step or the LightGBM model's predictive performance.