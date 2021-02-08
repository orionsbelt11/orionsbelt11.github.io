---
layout: post
title: Digital Divide
---

For those of us lucky enough to be working from home over the past year,
the workday has become synonymous with one word: Zoom. From daily
check-ins, to all-hands meetings, to not-so-happy happy hours, our
working lives have gone fully digital. Like all industries the rapid
shift to remote work has presented the field of international
development with challenges and opportunities.

The Data
--------

To assess the role of data pricing in the digital divide, I analyzed
data from UK company Cable on the price of mobile data plans in 228
countries from 2018 and 2020. I used the data on mobile data as this is
the most common form of internet access in lower income countries. Data
on broadband pricing is also available. The Cable researchers collected
pricing data on the major mobile internet providers by country.

The dataset includes the number of plans assessed by country, the median
cost of 1GB of mobile data per country, the cheapest 1GB of data, and
the most expensive 1GB of data, as well as the date on which the pricing
data was accessed.

``` r
head(cost19)
```

    ## # A tibble: 6 x 14
    ##    rank country_code name  region plans `1gb_avg_price_~ currency
    ##   <dbl> <chr>        <chr> <chr>  <dbl>            <dbl> <chr>   
    ## 1     1 IN           India ASIA ~    57             18.5 INR     
    ## 2     2 KG           Kyrg~ CIS (~    12             18.8 KGS     
    ## 3     3 KZ           Kaza~ CIS (~    26            186.  KZT     
    ## 4     4 UA           Ukra~ CIS (~    12             14.0 UAH     
    ## 5     5 RW           Rwan~ SUB-S~    36            504.  RWF     
    ## 6     6 SD           Sudan SUB-S~    35             32.3 SDG     
    ## # ... with 7 more variables: conversion_rate <dbl>, `1gb_avg_price_usd` <dbl>,
    ## #   cheapest_usd <dbl>, cheapest_30d_usd <dbl>, expensive_local <dbl>,
    ## #   expensive_usd <dbl>, date <chr>

Data Prep
---------

We first merge the pricing data from 2018 and 2020. We then join this
with data on GDP and population from the World Bank. These data will
allow us to analyze the cost of data based on income per person within
each country.

``` r
fullcost <- full_join(cost19, cost20, by = "country_code") %>%
  select(country_code, name = name.x, region = region.x, plans18 = plans.x, `1gb_avg_price_usd18` = `1gb_avg_price_usd.x`, cheapest_usd18 = cheapest_usd.x, expensive_usd18 = expensive_usd.x, plans20 = plans.y, `1gb_avg_price_usd20` = `1gb_avg_price_usd.y`, cheapest_usd20 = cheapest_usd.y, expensive_usd20 = expensive_usd.y)
```

``` r
gdp_pop <- left_join(gdp, pop, by = "country") %>%
  select(country, code = country_code.x, gdp_per_cap, gdp_year = year.x, population, pop_year = year.y)
```

``` r
gb_gdp_pop <- left_join(fullcost, gdp_pop, by = c("name" = "country")) %>%
  filter(!is.na(code)) %>%
  mutate(percent_monthly20 = (`1gb_avg_price_usd20`/(gdp_per_cap/12)),
         percent_weekly20 = (`1gb_avg_price_usd20`/(gdp_per_cap/52)),
         percent_monthly18 = (`1gb_avg_price_usd18`/(gdp_per_cap/12)),
         zoom_call = percent_monthly20 *.54,
         cheapest_weekly20 = (cheapest_usd20/(gdp_per_cap/52)),
         expensive_weekly20 = (expensive_usd20/(gdp_per_cap/52)),
         within_country_diff = expensive_weekly20 - cheapest_weekly20,
         difference_18_20 = `1gb_avg_price_usd20` - `1gb_avg_price_usd18`) 
```

``` r
gdp_broadband <- left_join(gdp_pop, broad, by = c("country" = "name")) %>% 
  filter(!is.na(ranke)) %>%
  mutate(percent_monthly = avg_monthly_usd/(gdp_per_cap/12))
```

Analysis
--------

You can also embed plots, for example:

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](digital_divide_files/figure-markdown_github/pressure-1.png)

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.
