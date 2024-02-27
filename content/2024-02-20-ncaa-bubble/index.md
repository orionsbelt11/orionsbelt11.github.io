---
title: "NCAA bubble resumes"
subtitle: 
excerpt: 
date: 2024-02-20
author: 
draft: true
images:
series:
tags:
categories:
layout: single
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>
<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

``` r
library(rvest)
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter()         masks stats::filter()
    ## ✖ readr::guess_encoding() masks rvest::guess_encoding()
    ## ✖ dplyr::lag()            masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(magrittr)
```

    ## 
    ## Attaching package: 'magrittr'
    ## 
    ## The following object is masked from 'package:purrr':
    ## 
    ##     set_names
    ## 
    ## The following object is masked from 'package:tidyr':
    ## 
    ##     extract

``` r
library(ncaahoopR)
library(sqldf)
```

    ## Loading required package: gsubfn
    ## Loading required package: proto
    ## Loading required package: RSQLite

``` r
proj = "C:/Users/Orion Wilcox/Documents/Personal/Projects/NCAA Schedules"
```

\#Pull data

``` r
#helper data from ncaahoopsR
dict = dict
teams = ids

#rankings from NCAA website (updated daily)
ncaa = read_html("https://www.ncaa.com/rankings/basketball-men/d1/ncaa-mens-basketball-net-rankings")

net_rank = ncaa %>%
  html_element("table") %>%
  html_table() %>%
  select(Rank, School) %>%
  janitor::clean_names()

#grab vector of top 25 teams based on net ranking
top_25 = filter(net_rank, rank <=25)

top_25 = left_join(top_25, dict, by = c("school" = "NCAA")) %>%
  select(rank, school = ESPN)

top_25_list = top_25$school

#grab vector of bubble teams

bubble = filter(net_rank, rank >= 63 & rank <= 72)

bubble$school = recode(bubble$school, 
                     "Ohio St." = "Ohio State",
                     "St. Bonaventure" = "St Bonaventure")

bubble_list = bubble$school
```

pull schedules for top 25 teams

``` r
df = data.frame()

for (i in bubble_list) {
  sched = get_schedule(i)
  sched$team = i
  df = bind_rows(df, sched)
  
}


net_rank_dict = left_join(net_rank, dict, by = c("school" = "NCAA"))

df_rank = sqldf("SELECT * FROM df 
       LEFT JOIN net_rank_dict
       ON df.opponent = net_rank_dict.school
       OR df.opponent = net_rank_dict.ESPN
       OR df.opponent = net_rank_dict.ESPN_PBP
       OR df.opponent = net_rank_dict.Warren_Nolan")

#manually review for missing D1 schools

df$opponent = recode(df$opponent, 
                     "Fairleigh Dickinson" = "FDU",
                     "Miami OH" = "Miami (OH)",
                     "Tarleton State" = "Tarleton St.")

df_rank = sqldf("SELECT * FROM df 
       LEFT JOIN net_rank_dict
       ON df.opponent = net_rank_dict.school
       OR df.opponent = net_rank_dict.ESPN
       OR df.opponent = net_rank_dict.ESPN_PBP
       OR df.opponent = net_rank_dict.Warren_Nolan") %>%
  select(1:10) 
```

``` r
df_rank %<>%
  mutate(result = ifelse(team_score > opp_score, "Win", "Loss"),
         quad = case_when(location == "H" & rank <= 30 ~ "Quad 1",
                          location == "N" & rank <= 50 ~ "Quad 1",
                          location == "A" & rank <= 75 ~ "Quad 1",
                          location == "H" & rank <= 75 ~ "Quad 2",
                          location == "N" & (rank >= 50 & rank <=100) ~ "Quad 2",
                          location == "A" & (rank >= 75 & rank <=135) ~ "Quad 2",
                          location == "H" & (rank >= 75 & rank <= 160) ~ "Quad 3",
                          location == "N" & (rank >= 101 & rank <= 200) ~ "Quad 3",
                          location == "A" & (rank >= 135 & rank <= 240) ~ "Quad 3",
                          TRUE ~ "Quad 4"))

df_rank %<>%
  select(team, date, opponent, team_score, opp_score, result, quad, rank, location, team_score, opp_score) %>%
  group_by(team) %>%
  mutate(game_order = dense_rank(date))
```

\#chart

each team’s worst loss

``` r
#replace NA with unranked
df_rank %<>%
  mutate(rank = ifelse(is.na(rank), 999, rank))


df_losses = df_rank %>%
  filter(result == "Loss") %>%
  group_by(team) %>%
  mutate(label = case_when(rank == max(rank) & result == "Loss" ~ "Worst loss",
                           TRUE ~ "Other loss"))

df_wins = df_rank %>%
  filter(result == "Win") %>%
  group_by(team) %>%
  mutate(label = case_when(rank == min(rank) & result == "Win" ~ "Best win", 
                           TRUE ~ "Other win"))

df_tbd = df_rank %>%
  filter(is.na(result)) %>%
  mutate(label = "Not yet played")

df_rank = rbind(df_losses, df_wins)
df_rank = rbind(df_rank, df_tbd)

#set team rank as factor
df_rank$team = factor(df_rank$team, levels = bubble_list)

df_rank %<>%
  mutate(quad_result = ifelse(is.na(result), "TBD", paste0(quad, " ", result))) %>%
  mutate(heat = case_when(quad_result == "Quad 1 Win" ~ 1,
                          quad_result == "Quad 2 Win" ~ 2,
                          quad_result == "Quad 3 Win" ~ 3,
                          quad_result == "Quad 4 Win" ~ 4,
                          quad_result == "Quad 1 Loss" ~ 5,
                          quad_result == "Quad 2 Loss" ~ 6,
                          quad_result == "Quad 3 Loss" ~ 7,
                          quad_result == "Quad 4 Loss" ~ 8))

df_rank$quad_result = factor(df_rank$quad_result, levels = c("Quad 1 Win", "Quad 2 Win", "Quad 3 Win", "Quad 4 Win", "Quad 1 Loss", "Quad 2 Loss", "Quad 3 Loss", "Quad 4 Loss", "TBD"))

ggplot(df_rank) +
  geom_tile(aes(x=game_order, y=team, fill=quad_result))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

``` r
colors = c('#0045ff', '#5e74fc', '#84a1f7', '#a6cdf2', '#ffc4b5', '#ff9b80', '#ff6a49', '#fe0000', "grey")

df_rank %<>%
  mutate(location = case_when(location == "H" ~ "vs",
                              location == "A" ~ "at",
                            location == "N" ~ "vs"),
         tooltip = ifelse(is.na(result), "Not yet played", paste0(date, ": ", result, " ", location, " ", opponent, " (", rank, ") ", " ", team_score, " to ", opp_score, sep = " ")))

df_rank$Outcome = df_rank$quad_result
bubble = ggplot(df_rank, aes(x = game_order, y = team, color = Outcome, text = tooltip), size = 5) +
  geom_point(size = 5, shape = 15) +
  scale_y_discrete(limits=rev) +
  xlab("Game") +
  ylab("") +
  labs(title = "Tournament Resumes: Bubble Teams",
       subtitle = "Records classified by NCAA quadrant system",
       caption = "Quadrants based on location of game and net ranking of opponent as of February 19, 2024") +
   scale_color_manual(values = (colors), na.value = "grey") +
   theme(plot.title.position = "plot",
        plot.title = element_text(family = "Space Grotesk",
                                  face = "bold",
                                  size = 16, margin = margin(t = 10, b = 15)),
        plot.subtitle = element_text(family = "Source Sans 3",
                                     size = 12,
                                     margin = margin(b = 5)),
        plot.caption = element_text(family = "Source Sans 3", 
                                    size = 12),
        legend.position = "bottom",
        legend.title = element_blank(),
        legend.text = element_text(family = "Source Sans 3",
                                   size = 12),
        plot.background = element_rect(fill = "#FAF8F6",
                                       color = NA),
        panel.background = element_rect(fill = "#FAF8F6"),
        axis.ticks = element_blank(),
        axis.text.y = element_text(size = 12, 
                                   hjust = 0),
        panel.grid = element_blank(),
        legend.background =element_blank(),
        legend.key = element_blank())

bubble
```

    ## Warning in grid.Call(C_stringMetric, as.graphicsAnnot(x$label)): font family
    ## not found in Windows font database

    ## Warning in grid.Call(C_stringMetric, as.graphicsAnnot(x$label)): font family
    ## not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

    ## Warning in grid.Call(C_textBounds, as.graphicsAnnot(x$label), x$x, x$y, : font
    ## family not found in Windows font database

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-2.png" width="672" />

``` r
#interactive

plotly::ggplotly(bubble, tooltip = "tooltip")
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[14,15,5,16,2,5,26,28,17,20,14,16,9,20],"y":[10,10,9,9,8,7,7,7,6,6,4,4,3,1],"text":["2024-01-04: Win at Washington (70)  76 to 74 ","2024-01-06: Win at Washington State (35)  89 to 84 ","2023-11-19: Win vs Saint Mary's (16)  66 to 49 ","2024-01-13: Win at Providence (54)  85 to 65 ","2023-11-11: Win vs Utah State (29)  72 to 66 ","2023-11-24: Win vs Alabama (6)  92 to 81 ","2024-02-18: Win vs Purdue (2)  73 to 69 ","2024-02-25: Win at Michigan State (24)  60 to 57 ","2024-01-14: Win at Illinois (17)  76 to 67 ","2024-01-24: Win at Iowa (60)  69 to 67 ","2024-01-10: Win vs Kansas (15)  65 to 60 ","2024-01-17: Win at Texas (40)  77 to 71 ","2023-12-09: Win vs Gonzaga (21)  78 to 73 ","2024-01-27: Win vs Dayton (20)  69 to 64 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,69,255,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(0,69,255,1)"}},"hoveron":"points","name":"Quad 1 Win","legendgroup":"Quad 1 Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[1,21,23,26,10,13,17,21,23,1,18,24,12,21,25,28,18,23,24,21,26,4,10,18,21,27,12,13,27,5,14,15,17,18],"y":[10,10,10,10,9,9,9,9,9,8,8,7,6,6,6,6,5,5,5,4,4,3,3,3,3,3,2,2,2,1,1,1,1,1],"text":["2023-11-06: Win vs Georgia (97)  82 to 71 ","2024-02-01: Win at USC (101)  78 to 69 ","2024-02-08: Win vs Washington (70)  85 to 80 ","2024-02-22: Win at Stanford (116)  78 to 65 ","2023-12-09: Win vs Cincinnati (45)  84 to 79 ","2023-12-23: Win vs Seton Hall (61)  74 to 54 ","2024-01-16: Win vs Butler (62)  85 to 71 ","2024-01-31: Win vs St. John's (44)  88 to 77 ","2024-02-07: Win vs Villanova (38)  56 to 53 ","2023-11-06: Win at UAB (118)  73 to 71 ","2024-01-17: Win at Southern Illinois (102)  70 to 69 ","2024-02-10: Win vs Maryland (67)  79 to 75 ","2023-12-22: Win at UCLA (112)  69 to 60 ","2024-01-27: Win vs Nebraska (41)  73 to 51 ","2024-02-14: Win vs Iowa (60)  78 to 66 ","2024-02-25: Win at Rutgers (98)  63 to 46 ","2024-01-16: Win at Western Carolina (123)  75 to 71 ","2024-02-03: Win at Chattanooga (134)  78 to 56 ","2024-02-08: Win at UNC Greensboro (133)  78 to 69 ","2024-02-03: Win vs Oklahoma (39)  74 to 63 ","2024-02-24: Win vs Texas Tech (34)  75 to 61 ","2023-11-17: Win vs Xavier (64)  74 to 71 ","2023-12-17: Win at Seattle U (114)  100 to 99 ","2024-01-18: Win at California (115)  77 to 75 ","2024-01-27: Win vs Utah (53)  98 to 73 ","2024-02-22: Win at Arizona State (127)  84 to 82 ","2023-12-30: Win vs Akron (95)  62 to 61 ","2024-01-03: Win at VCU (76)  89 to 78 ","2024-02-24: Win at Massachusetts (87)  75 to 67 ","2023-11-21: Win vs UNLV (81)  82 to 65 ","2024-01-06: Win vs St. Bonaventure (71)  65 to 54 ","2024-01-09: Win at Loyola Chicago (92)  58 to 56 ","2024-01-16: Win at Duquesne (105)  63 to 61 ","2024-01-20: Win at Davidson (108)  69 to 64 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(94,116,252,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(94,116,252,1)"}},"hoveron":"points","name":"Quad 2 Win","legendgroup":"Quad 2 Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[2,7,12,13,16,19,25,3,4,6,15,17,19,20,22,23,28,1,6,8,11,13,14,18,9,16,8,11,14,16,21,26,28,4,5,7,18,6,11,16,24,25,3,8,19,20,22,24,25,11,16,21,25,26,27],"y":[10,10,10,10,10,10,10,8,8,8,8,8,8,8,8,8,8,7,7,7,7,7,7,7,6,6,5,5,5,5,5,5,5,4,4,4,4,3,3,3,3,3,2,2,2,2,2,2,2,1,1,1,1,1,1],"text":["2023-11-10: Win vs Montana (148)  75 to 61 ","2023-12-02: Win vs Michigan (120)  86 to 83 ","2023-12-28: Win vs USC (101)  82 to 74 ","2023-12-30: Win vs UCLA (112)  64 to 59 ","2024-01-13: Win vs California (115)  80 to 73 ","2024-01-25: Win vs Arizona State (127)  80 to 61 ","2024-02-17: Win at Oregon State (167)  60 to 58 ","2023-11-14: Win vs Tarleton St. (142)  86 to 63 ","2023-11-20: Win vs Tulane (137)  80 to 77 ","2023-11-25: Win vs Vermont (100)  79 to 70 ","2024-01-06: Win vs Missouri State (152)  86 to 60 ","2024-01-13: Win at UIC (180)  77 to 59 ","2024-01-20: Win vs Belmont (131)  95 to 72 ","2024-01-24: Win vs Murray State (146)  71 to 63 ","2024-01-31: Win vs Northern Iowa (119)  85 to 69 ","2024-02-03: Win at Illinois State (170)  73 to 60 ","2024-02-21: Win at Missouri State (152)  86 to 62 ","2023-11-06: Win vs Oakland (130)  79 to 73 ","2023-11-25: Win vs Santa Clara (106)  86 to 56 ","2023-12-03: Win vs Minnesota (78)  84 to 74 ","2023-12-16: Win vs UCLA (112)  67 to 60 ","2023-12-30: Win vs West Virginia (147)  78 to 75 ","2024-01-03: Win vs Rutgers (98)  76 to 72 ","2024-01-20: Win vs Penn State (96)  79 to 67 ","2023-12-06: Win vs Penn State (96)  81 to 75 ","2024-01-11: Win vs Michigan (120)  64 to 57 ","2023-11-30: Win vs Louisiana (128)  88 to 65 ","2023-12-16: Win vs Belmont (131)  99 to 93 ","2024-01-03: Win vs Chattanooga (134)  89 to 74 ","2024-01-11: Win vs UNC Greensboro (133)  79 to 70 ","2024-01-27: Win at East Tennessee State (211)  75 to 72 ","2024-02-14: Win vs Western Carolina (123)  88 to 62 ","2024-02-21: Win vs Furman (136)  74 to 72 ","2023-11-19: Win vs South Dakota State (162)  83 to 80 ","2023-11-20: Win vs Charlotte (104)  74 to 71 ","2023-12-02: Win vs Lipscomb (159)  72 to 57 ","2024-01-23: Win vs West Virginia (147)  72 to 59 ","2023-11-28: Win vs UC San Diego (110)  83 to 56 ","2023-12-21: Win vs Eastern Washington (129)  73 to 66 ","2024-01-11: Win vs Arizona State (127)  82 to 67 ","2024-02-10: Win at Oregon State (167)  67 to 55 ","2024-02-15: Win vs Stanford (116)  85 to 65 ","2023-11-16: Win vs Oklahoma State (113)  66 to 64 ","2023-12-06: Win at Niagara (224)  94 to 60 ","2024-01-26: Win vs Saint Joseph's (99)  91 to 72 ","2024-01-30: Win vs VCU (76)  67 to 62 ","2024-02-07: Win vs Massachusetts (87)  79 to 73 ","2024-02-14: Win at Fordham (182)  85 to 67 ","2024-02-17: Win vs Davidson (108)  81 to 80 ","2023-12-16: Win vs Charlotte (104)  64 to 56 ","2024-01-13: Win vs George Mason (89)  77 to 70 ","2024-01-31: Win at Fordham (182)  83 to 69 ","2024-02-17: Win at George Washington (203)  90 to 74 ","2024-02-21: Win at Rhode Island (201)  85 to 77 ","2024-02-24: Win vs Davidson (108)  66 to 63 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(132,161,247,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(132,161,247,1)"}},"hoveron":"points","name":"Quad 3 Win","legendgroup":"Quad 3 Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[3,4,8,9,11,1,2,6,11,18,22,5,12,13,14,16,26,29,3,4,7,9,12,1,5,6,7,10,11,13,3,4,5,6,7,9,10,12,13,15,17,19,22,25,29,1,3,8,10,11,12,1,2,8,15,1,5,6,7,9,11,16,1,2,6,8,12,13,19,23],"y":[10,10,10,10,10,9,9,9,9,9,9,8,8,8,8,8,8,8,7,7,7,7,7,6,6,6,6,6,6,6,5,5,5,5,5,5,5,5,5,5,5,5,5,5,5,4,4,4,4,4,4,3,3,3,3,2,2,2,2,2,2,2,1,1,1,1,1,1,1,1],"text":["2023-11-17: Win vs Tennessee State (275)  92 to 67 ","2023-11-20: Win at Florida A&M (340)  67 to 54 ","2023-12-09: Win vs UTEP (226)  71 to 49 ","2023-12-12: Win vs California Baptist (197)  76 to 55 ","2023-12-21: Win vs Kent State (171)  84 to 70 ","2023-11-06: Win vs Robert Morris (294)  77 to 63 ","2023-11-10: Win vs Jacksonville (276)  79 to 56 ","2023-11-24: Win vs Bryant (163)  100 to 75 ","2023-12-16: Win vs Winthrop (172)  75 to 59 ","2024-01-19: Win vs Georgetown (198)  92 to 91 ","2024-02-03: Win at DePaul (320)  93 to 68 ","2023-11-22: Win vs UTEP (226)  63 to 59 ","2023-12-21: Win vs SIU Edwardsville (274)  75 to 64 ","2023-12-28: Win vs Truman State (999)  69 to 47 ","2024-01-03: Win at Valparaiso (301)  86 to 61 ","2024-01-10: Win vs Evansville (199)  86 to 50 ","2024-02-14: Win vs UIC (180)  85 to 73 ","2024-02-24: Win vs Illinois State (170)  48 to 45 ","2023-11-15: Win vs Merrimack (200)  76 to 52 ","2023-11-19: Win vs Western Michigan (291)  73 to 56 ","2023-11-29: Win vs Central Michigan (254)  88 to 61 ","2023-12-06: Win vs Miami (OH) (234)  84 to 64 ","2023-12-21: Win vs New Orleans (347)  78 to 36 ","2023-11-07: Win vs Mount St. Mary's (243)  68 to 53 ","2023-11-21: Win vs UMBC (287)  92 to 68 ","2023-11-25: Win vs South Alabama (244)  68 to 55 ","2023-11-28: Win vs Rider (241)  103 to 76 ","2023-12-12: Win vs Alcorn State (292)  105 to 65 ","2023-12-19: Win vs Nicholls (264)  73 to 67 ","2023-12-28: Win vs Coppin State (360)  75 to 53 ","2023-11-14: Win vs Mississippi College (999)  96 to 52 ","2023-11-17: Win vs South Carolina State (293)  89 to 72 ","2023-11-22: Win vs Alabama State (308)  99 to 67 ","2023-11-24: Win vs Merrimack (200)  79 to 71 ","2023-11-25: Win vs North Carolina A&T (337)  101 to 83 ","2023-12-03: Win vs LaGrange (999)  128 to 82 ","2023-12-11: Win vs Alabama A&M (335)  118 to 91 ","2023-12-19: Win at Valparaiso (301)  79 to 61 ","2023-12-21: Win at Texas Southern (281)  87 to 65 ","2024-01-06: Win at The Citadel (250)  80 to 64 ","2024-01-13: Win vs VMI (353)  134 to 96 ","2024-01-20: Win vs Mercer (233)  87 to 80 ","2024-01-31: Win vs Wofford (190)  81 to 79 ","2024-02-10: Win at VMI (353)  102 to 63 ","2024-02-24: Win vs East Tennessee State (211)  87 to 71 ","2023-11-06: Win vs Florida International (299)  85 to 62 ","2023-11-16: Win vs Cal State Fullerton (251)  72 to 44 ","2023-12-06: Win vs Jacksonville (276)  94 to 52 ","2023-12-18: Win vs Maine (223)  74 to 51 ","2023-12-21: Win vs Florida A&M (340)  69 to 56 ","2023-12-29: Win vs Bethune-Cookman (314)  98 to 54 ","2023-11-06: Win vs Bellarmine (316)  91 to 57 ","2023-11-09: Win vs Northern Kentucky (187)  75 to 67 ","2023-12-05: Win vs Montana State (259)  85 to 61 ","2024-01-06: Win vs Oregon State (167)  79 to 72 ","2023-11-06: Win vs Longwood (177)  73 to 69 ","2023-11-22: Win vs Bucknell (302)  67 to 61 ","2023-11-25: Win vs Miami (OH) (234)  90 to 60 ","2023-12-02: Win at Buffalo (345)  80 to 65 ","2023-12-09: Win vs Siena (355)  89 to 56 ","2023-12-22: Win vs Binghamton (280)  90 to 64 ","2024-01-17: Win vs Rhode Island (201)  99 to 64 ","2023-11-06: Win vs VMI (353)  93 to 75 ","2023-11-11: Win vs Siena (355)  90 to 48 ","2023-11-25: Win vs Queens University (272)  90 to 61 ","2023-12-02: Win vs William & Mary (331)  88 to 69 ","2023-12-21: Win vs Buffalo (345)  72 to 66 ","2023-12-30: Win vs Lafayette (307)  59 to 38 ","2024-01-24: Win vs George Washington (203)  82 to 74 ","2024-02-10: Win vs La Salle (193)  82 to 65 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(166,205,242,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(166,205,242,1)"}},"hoveron":"points","name":"Quad 4 Win","legendgroup":"Quad 4 Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6,17,18,20,3,8,12,14,15,19,20,24,25,27,21,16,19,20,21,22,25,4,14,18,19,22,24,26,27,1,13,15,17,19,20,22,23,5,7,12,13,23,28,4,10,14,21,4,10],"y":[10,10,10,10,9,9,9,9,9,9,9,9,9,9,8,7,7,7,7,7,7,6,6,6,6,6,6,6,6,5,4,4,4,4,4,4,4,3,3,3,3,3,3,2,2,2,2,1,1],"text":["2023-11-25: Loss vs Alabama (6)  91 to 99 ","2024-01-18: Loss at Colorado (32)  70 to 86 ","2024-01-21: Loss at Utah (53)  77 to 80 ","2024-01-27: Loss vs Arizona (4)  78 to 87 ","2023-11-13: Loss at Purdue (2)  71 to 83 ","2023-12-01: Loss vs Houston (1)  60 to 66 ","2023-12-20: Loss at St. John's (44)  66 to 81 ","2024-01-03: Loss at Villanova (38)  65 to 66 ","2024-01-10: Loss vs UConn (3)  75 to 80 ","2024-01-23: Loss at Creighton (11)  78 to 85 ","2024-01-28: Loss at UConn (3)  56 to 99 ","2024-02-10: Loss vs Creighton (11)  71 to 78 ","2024-02-14: Loss at Seton Hall (61)  70 to 88 ","2024-02-25: Loss at Marquette (12)  64 to 88 ","2024-01-27: Loss at Indiana State (33)  86 to 95 ","2024-01-10: Loss vs Wisconsin (22)  60 to 71 ","2024-01-23: Loss at Nebraska (41)  69 to 83 ","2024-01-27: Loss at Northwestern (52)  58 to 83 ","2024-01-30: Loss vs Illinois (17)  75 to 87 ","2024-02-02: Loss at Iowa (60)  77 to 79 ","2024-02-13: Loss at Wisconsin (22)  54 to 62 ","2023-11-17: Loss at Villanova (38)  40 to 57 ","2024-01-02: Loss vs Purdue (2)  53 to 67 ","2024-01-17: Loss at Northwestern (52)  69 to 72 ","2024-01-21: Loss vs Michigan State (24)  59 to 61 ","2024-02-03: Loss at Michigan State (24)  54 to 63 ","2024-02-10: Loss at Ohio State (66)  75 to 79 ","2024-02-17: Loss vs Illinois (17)  80 to 85 ","2024-02-20: Loss at Wisconsin (22)  70 to 74 ","2023-11-06: Loss at Purdue (2)  45 to 98 ","2024-01-06: Loss at Kansas State (73)  52 to 77 ","2024-01-13: Loss vs BYU (14)  58 to 63 ","2024-01-20: Loss at Houston (1)  42 to 57 ","2024-01-27: Loss at Cincinnati (45)  57 to 68 ","2024-01-31: Loss vs Baylor (13)  69 to 77 ","2024-02-10: Loss at Texas Tech (34)  59 to 66 ","2024-02-13: Loss at BYU (14)  88 to 90 ","2023-11-19: Loss vs San Diego State (18)  97 to 100 ","2023-12-02: Loss vs Colorado State (27)  81 to 86 ","2023-12-29: Loss at Colorado (32)  69 to 73 ","2023-12-31: Loss at Utah (53)  90 to 95 ","2024-02-08: Loss at Oregon (63)  80 to 85 ","2024-02-24: Loss at Arizona (4)  75 to 91 ","2023-11-17: Loss vs Auburn (7)  60 to 77 ","2023-12-16: Loss vs Florida Atlantic (36)  54 to 64 ","2024-01-06: Loss at Richmond (72)  54 to 65 ","2024-02-02: Loss at Dayton (20)  71 to 76 ","2023-11-20: Loss vs Colorado (32)  59 to 64 ","2023-12-09: Loss vs Florida (30)  76 to 87 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,196,181,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(255,196,181,1)"}},"hoveron":"points","name":"Quad 1 Loss","legendgroup":"Quad 1 Loss","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[10,22,24,27,4,26,8,9,25,27,2,10,15,17,27,8,15,2,2,9,24,3,14,17,19,20,22,17,18,3,9,22],"y":[10,10,10,10,9,9,8,8,8,8,7,7,7,7,7,6,6,5,4,4,4,3,3,3,3,3,3,2,2,1,1,1],"text":["2023-12-17: Loss vs Syracuse (85)  63 to 83 ","2024-02-03: Loss at UCLA (112)  63 to 71 ","2024-02-10: Loss vs Washington State (35)  56 to 62 ","2024-02-24: Loss at California (115)  64 to 69 ","2023-11-17: Loss vs Washington (70)  71 to 74 ","2024-02-21: Loss vs Providence (54)  75 to 79 ","2023-12-02: Loss vs Indiana State (33)  77 to 85 ","2023-12-05: Loss at Akron (95)  52 to 67 ","2024-02-10: Loss vs Drake (46)  67 to 74 ","2024-02-18: Loss at Northern Iowa (119)  63 to 74 ","2023-11-10: Loss vs Texas A&M (58)  66 to 73 ","2023-12-09: Loss at Penn State (96)  80 to 83 ","2024-01-06: Loss at Indiana (107)  65 to 71 ","2024-01-15: Loss at Michigan (120)  65 to 73 ","2024-02-22: Loss at Minnesota (78)  79 to 88 ","2023-12-01: Loss at Indiana (107)  53 to 65 ","2024-01-07: Loss at Minnesota (78)  62 to 65 ","2023-11-10: Loss at VCU (76)  65 to 75 ","2023-11-10: Loss at Miami (90)  72 to 88 ","2023-12-10: Loss vs Ole Miss (74)  68 to 70 ","2024-02-17: Loss vs Cincinnati (45)  74 to 76 ","2023-11-12: Loss vs Nevada (42)  76 to 83 ","2024-01-04: Loss vs Oregon (63)  74 to 76 ","2024-01-14: Loss at UCLA (112)  61 to 73 ","2024-01-20: Loss at Stanford (116)  80 to 90 ","2024-01-24: Loss vs Colorado (32)  81 to 98 ","2024-02-03: Loss vs Washington State (35)  87 to 90 ","2024-01-20: Loss at George Mason (89)  60 to 69 ","2024-01-23: Loss at Duquesne (105)  50 to 54 ","2023-11-15: Loss at Boston College (88)  61 to 68 ","2023-12-06: Loss at Northern Iowa (119)  73 to 78 ","2024-02-03: Loss at VCU (76)  52 to 63 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,155,128,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(255,155,128,1)"}},"hoveron":"points","name":"Quad 2 Loss","legendgroup":"Quad 2 Loss","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5,7,9,7,11,24,23,2,3,23,20,27,25,26,23,26,7,24],"y":[10,9,9,8,8,8,7,6,6,6,5,5,4,3,2,2,1,1],"text":["2023-11-24: Loss vs Santa Clara (106)  82 to 88 ","2023-11-27: Loss vs Oakland (130)  76 to 78 ","2023-12-05: Loss vs Delaware (153)  80 to 87 ","2023-11-29: Loss at Murray State (146)  72 to 79 ","2023-12-18: Loss vs Duquesne (105)  67 to 69 ","2024-02-07: Loss at Evansville (199)  70 to 73 ","2024-02-06: Loss vs Indiana (107)  73 to 76 ","2023-11-10: Loss vs Davidson (108)  61 to 64 ","2023-11-12: Loss vs UAB (118)  63 to 66 ","2024-02-06: Loss vs Rutgers (98)  53 to 56 ","2024-01-24: Loss at Furman (136)  68 to 78 ","2024-02-17: Loss at Mercer (233)  84 to 88 ","2024-02-20: Loss at West Virginia (147)  67 to 77 ","2024-02-17: Loss vs California (115)  80 to 82 ","2024-02-10: Loss vs Duquesne (105)  69 to 75 ","2024-02-21: Loss at La Salle (193)  59 to 72 ","2023-11-29: Loss at Wichita State (161)  68 to 80 ","2024-02-14: Loss vs Massachusetts (87)  59 to 69 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,106,73,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(255,106,73,1)"}},"hoveron":"points","name":"Quad 3 Loss","legendgroup":"Quad 3 Loss","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[10,6,2,15],"y":[8,4,2,2],"text":["2023-12-15: Loss vs Cleveland State (192)  69 to 76 ","2023-11-26: Loss vs Stetson (212)  82 to 85 ","2023-11-11: Loss vs Canisius (261)  67 to 70 ","2024-01-13: Loss vs Fordham (182)  74 to 80 "],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(254,0,0,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(254,0,0,1)"}},"hoveron":"points","name":"Quad 4 Loss","legendgroup":"Quad 4 Loss","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[28,29,30,31,28,29,30,31,30,31,29,30,31,29,30,31,30,31,27,28,29,30,29,30,31,28,29,30,28,29,30,31],"y":[10,10,10,10,9,9,9,9,8,8,7,7,7,6,6,6,5,5,4,4,4,4,3,3,3,2,2,2,1,1,1,1],"text":"Not yet played","type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(190,190,190,1)","opacity":1,"size":18.897637795275593,"symbol":"square","line":{"width":1.8897637795275593,"color":"rgba(190,190,190,1)"}},"hoveron":"points","name":"TBD","legendgroup":"TBD","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":47.481942714819425,"r":7.3059360730593621,"b":40.182648401826491,"l":122.54047322540474},"plot_bgcolor":"rgba(250,248,246,1)","paper_bgcolor":"rgba(250,248,246,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"<b> Tournament Resumes: Bubble Teams <\/b>","font":{"color":"rgba(0,0,0,1)","family":"Space Grotesk","size":21.253632212536321},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.5,32.5],"tickmode":"array","ticktext":["0","10","20","30"],"tickvals":[0,10,20,30],"categoryorder":"array","categoryarray":["0","10","20","30"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":false,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"y","title":{"text":"Game","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.40000000000000002,10.6],"tickmode":"array","ticktext":["Richmond","St Bonaventure","Washington","UCF","Samford","Maryland","Ohio State","Bradley","Xavier","Oregon"],"tickvals":[1,2,3,4,5,6.0000000000000009,7,8,9,10],"categoryorder":"array","categoryarray":["Richmond","St Bonaventure","Washington","UCF","Samford","Maryland","Ohio State","Bradley","Xavier","Oregon"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":15.940224159402243},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":false,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"x","title":{"text":"","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"Source Sans 3","size":15.940224159402243},"title":{"text":"Outcome","font":{"color":null,"family":null,"size":0}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"635c2d4234d5":{"x":{},"y":{},"colour":{},"text":{},"type":"scatter"}},"cur_data":"635c2d4234d5","visdat":{"635c2d4234d5":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

\#export

``` r
ggsave(paste0(proj, "/visuals/top_25.png"))
```

    ## Saving 7 x 5 in image

\#datawrapper export

``` r
top_25_sched = df_rank %>%
  mutate(quad_result = paste0(quad, " ", result)) %>%
  mutate(heat = case_when(quad_result == "Q1 W" ~ 1,
                          quad_result == "Q2 W" ~ 2,
                          quad_result == "Q3 W" ~ 3,
                          quad_result == "Q4 W" ~ 4,
                          quad_result == "Q1 L" ~ 5,
                          quad_result == "Q2 L" ~ 6,
                          quad_result == "Q3 L" ~ 7,
                          quad_result == "Q4 L" ~ 8)) %>%
  select(team, game_order, heat) %>%
  pivot_wider(names_from = game_order, values_from = heat) 
```

``` r
write_csv(top_25_sched, paste0(proj, "/data/top_25.csv"))
```
