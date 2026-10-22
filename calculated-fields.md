# Calculated fields

Every calculated field and parameter in `KingCountyHousing.twbx`, grouped by what it's used for.

## Data source filters

Applied to the whole data source, so every chart uses the same 21,044 sales.

| Field | Keep |
| --- | --- |
| Bedrooms | 1 or more |
| Bathrooms | 0.5 or more |

## Basics

```
// The raw date is text like 20141013T000000
Sale Date = DATEPARSE("yyyyMMdd", LEFT([Date], 8))

Price per Sqft = [Price] / [Sqft Living]

Homes Sold = COUNT([Id])

Median Price = MEDIAN([Price])

Median Price per Sqft = MEDIAN([Price per Sqft])
```

## Groupings

```
// The numbers at the front keep the bands in order
Grade Band =
IF [Grade] <= 6 THEN "1. Below average (1-6)"
ELSEIF [Grade] = 7 THEN "2. Average (7)"
ELSEIF [Grade] <= 9 THEN "3. Good (8-9)"
ELSE "4. Luxury (10-12)"
END

Decade Built = STR(INT([Yr Built] / 10) * 10) + "s"
```

## Metric switch

```
// Parameter: Select Metric (string list: "Median Price", "Median $/sq ft")

Selected Metric =
CASE [Select Metric]
    WHEN "Median Price" THEN MEDIAN([Price])
    ELSE MEDIAN([Price per Sqft])
END
```

## Repeat sales

```
// How many times each house appears, ignoring whatever is on the chart
Times Sold = { FIXED [Id] : COUNT([Id]) }

Resold = IF [Times Sold] > 1 THEN "Resold" ELSE "Sold once" END

First Sale Date = { FIXED [Id] : MIN([Sale Date]) }

Last Sale Date = { FIXED [Id] : MAX([Sale Date]) }

First Sale Price = { FIXED [Id] : MIN(IF [Sale Date] = [First Sale Date] THEN [Price] END) }

Last Sale Price = { FIXED [Id] : MAX(IF [Sale Date] = [Last Sale Date] THEN [Price] END) }

Resale Gain % = ([Last Sale Price] - [First Sale Price]) / [First Sale Price]

Resold Homes = COUNTD(IF [Resold] = "Resold" THEN [Id] END)

Median Resale Gain = MEDIAN(IF [Resold] = "Resold" THEN [Resale Gain %] END)
```

## Headline tiles (page 2)

```
// The "vs $445K" comparison on each tile
Overall Median Price = { FIXED : MEDIAN([Price]) }

Waterfront Median = MEDIAN(IF [Waterfront] = "Yes" THEN [Price] END)

Excellent View Median = MEDIAN(IF [View] = "Excellent" THEN [Price] END)

Luxury Grade Median = MEDIAN(IF [Grade] >= 10 THEN [Price] END)
```

## Helper field

```
// A copy of Zipcode used only for the Top 10 filter on the Top Zips chart.
// The real Zipcode field is a shared filter across both pages, and a sheet
// can only have one filter per field.
Zipcode (copy) = [Zipcode]
```
