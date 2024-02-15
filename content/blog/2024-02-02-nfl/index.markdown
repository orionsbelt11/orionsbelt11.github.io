---
title: "Which states are over-represented in the NFL?"
subtitle: "Southern states (and Iowa) are football factories"
excerpt: "Scraping data to find the states that are home to the most NFL players per capita"
date: 2024-02-02
author: "Orion "
draft: false
images:
series:
tags:
categories:
layout: single
---

When it comes to state rankings of education, health, poverty and many other measures, southern states inevitably end up on the wrong end of the list. My own home state of Mississippi is so good at being bad that other southerners coined the phrase “Thank God for Mississippi.”

But no matter how bad our education, health, or income, there is one area where Southerners have a solid claim to excellence: the football field. I decided to check if the south’s love of football in fact translates into an increased chance of professional success for its players. Using data from sport-reference.com, I calculated the total number of active NFL players per million residents from each state.

![Figure 1. NFL players per capita.](img/fig1_map.png)

The numbers don’t lie. The “Deep South” states of Louisiana, Mississippi, Alabama, and Georgia are football factories, claiming the top four spots in producing NFL talent on a per-capita basis. Louisiana slightly edges out my home state for the top spot but, honestly, it’s nice to not be on the bottom for once. Outside of the south, Iowa is the only state with more than 10 players per million residents (something in that corn, I guess).The northeast is particularly weak at producing NFL talent. In the chart below, I pivot the axis by 45 degrees to emphasize the trend-line. States falling below the line are home to fewer NFL players than the national average (around 5 per million). States above the line are home to more than the average. Georgia has four times as many NFL players than New York, despite having half the population.

![Figure 2. NFL players vs population](img/fig2_scatter.png)

## The Process: Scraping tables

The biggest challenge with this project was accessing the player data. Sports-reference.com provides tables of career stats for all NFL players that include home state and birthplace. Here’s a quick peek at the table.

<div id="swawbknfol" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Libre+Franklin:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
#swawbknfol table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#swawbknfol thead, #swawbknfol tbody, #swawbknfol tfoot, #swawbknfol tr, #swawbknfol td, #swawbknfol th {
  border-style: none;
}

#swawbknfol p {
  margin: 0;
  padding: 0;
}

#swawbknfol .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#swawbknfol .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#swawbknfol .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#swawbknfol .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#swawbknfol .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#swawbknfol .gt_bottom_border {
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#swawbknfol .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#swawbknfol .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#swawbknfol .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#swawbknfol .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#swawbknfol .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#swawbknfol .gt_column_spanner {
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#swawbknfol .gt_spanner_row {
  border-bottom-style: hidden;
}

#swawbknfol .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#swawbknfol .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#swawbknfol .gt_from_md > :first-child {
  margin-top: 0;
}

#swawbknfol .gt_from_md > :last-child {
  margin-bottom: 0;
}

#swawbknfol .gt_row {
  padding-top: 7px;
  padding-bottom: 7px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#swawbknfol .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#swawbknfol .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#swawbknfol .gt_row_group_first td {
  border-top-width: 2px;
}

#swawbknfol .gt_row_group_first th {
  border-top-width: 2px;
}

#swawbknfol .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#swawbknfol .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#swawbknfol .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#swawbknfol .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#swawbknfol .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#swawbknfol .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#swawbknfol .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}

#swawbknfol .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#swawbknfol .gt_table_body {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #FFFFFF;
}

#swawbknfol .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#swawbknfol .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#swawbknfol .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#swawbknfol .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#swawbknfol .gt_left {
  text-align: left;
}

#swawbknfol .gt_center {
  text-align: center;
}

#swawbknfol .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#swawbknfol .gt_font_normal {
  font-weight: normal;
}

#swawbknfol .gt_font_bold {
  font-weight: bold;
}

#swawbknfol .gt_font_italic {
  font-style: italic;
}

#swawbknfol .gt_super {
  font-size: 65%;
}

#swawbknfol .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}

#swawbknfol .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#swawbknfol .gt_indent_1 {
  text-indent: 5px;
}

#swawbknfol .gt_indent_2 {
  text-indent: 10px;
}

#swawbknfol .gt_indent_3 {
  text-indent: 15px;
}

#swawbknfol .gt_indent_4 {
  text-indent: 20px;
}

#swawbknfol .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rank">rank</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="player">player</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="pos">pos</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="city">city</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="start">start</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="end">end</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="all_pro_yrs">all_pro_yrs</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="pro_bowl_yrs">pro_bowl_yrs</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="starter_yrs">starter_yrs</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="w_av">w_av</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="games">games</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="passed_cmp">passed_cmp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="passes_att">passes_att</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="passing_yds">passing_yds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="passing_tds">passing_tds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="longest_pass">longest_pass</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="int">int</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="sacked">sacked</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="sacked_lost_yds">sacked_lost_yds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rush_att">rush_att</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rush_yds">rush_yds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rush_tds">rush_tds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="longest_rush">longest_rush</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rec">rec</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rec_yds">rec_yds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="rec_tds">rec_tds</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="longest_rec">longest_rec</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="state">state</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Untz Brewer</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">HB</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1922</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1922</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">8</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Perry Dowrick</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">FB</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">3</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Patsy Gerardi</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">E</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">4</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Sam Kaplan</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">E</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">5</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Cy McDonald</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">G</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1921</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">3</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
    <tr><td headers="rank" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Yannick Ngakoue</td>
<td headers="pos" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DE</td>
<td headers="city" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Washington</td>
<td headers="start" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2016</td>
<td headers="end" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td>
<td headers="all_pro_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="pro_bowl_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">1</td>
<td headers="starter_yrs" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">7</td>
<td headers="w_av" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">46</td>
<td headers="games" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">123</td>
<td headers="passed_cmp" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passes_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="passing_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_pass" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="int" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="sacked_lost_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_att" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rush_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rush" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_yds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="rec_tds" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">0</td>
<td headers="longest_rec" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">NA</td>
<td headers="state" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">DC</td></tr>
  </tbody>
  
  
</table>
</div>

The excellent [sports-reference website](https://www.sports-reference.com/) provides a download option for accessing the tables. Unfortunately, the tables are organized by state, so downloading the tables manually would require navigating to the page for each of the 50 states and downloading. This is where the Rvest package for web scraping comes in handy. The [Rvest website](https://rvest.tidyverse.org/articles/rvest.html) provides an intro to scraping, so I’ll just go over the basic process of pulling a table from a web page.

Whenever I begin a web scraping project I start out by exploring the structure of the website using Google Chrome’s Developer Tools. Once you open the Developer Tools window, you can hover over each element in the code and the element will highlight in the browser. In the sports-reference page, we can see that the player data table is a “table node.”

Now that we know what we’re looking for, we can jump into R.

``` r
library(rvest)
#library(stringi)

#paste the URL 
# I'm separating the root URL from the state signifier to set up a for loop for later

url = paste0("https://www.pro-football-reference.com/friv/birthplaces.cgi?country=USA&state=", "AL")

# The read html function loads the web page into R
html <- read_html(url)

# Grab the table node with html_node
node <- html_node(html, "table")

# The very useful html_table function automatically formats the output as a data frame
table <- html_table(node, header = T)

# Append the state code to the table

table$State = "AL"

# Select only the biographical data we need
# The column "To" indicates most recent year the player was active
table = select(table, 
               Player, 
               City, 
               State, 
               To)
```

We can see the output (limited to five rows) below.

<div id="ztgbwltzbo" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Libre+Franklin:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
#ztgbwltzbo table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#ztgbwltzbo thead, #ztgbwltzbo tbody, #ztgbwltzbo tfoot, #ztgbwltzbo tr, #ztgbwltzbo td, #ztgbwltzbo th {
  border-style: none;
}

#ztgbwltzbo p {
  margin: 0;
  padding: 0;
}

#ztgbwltzbo .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#ztgbwltzbo .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#ztgbwltzbo .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#ztgbwltzbo .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#ztgbwltzbo .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ztgbwltzbo .gt_bottom_border {
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ztgbwltzbo .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ztgbwltzbo .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#ztgbwltzbo .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#ztgbwltzbo .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#ztgbwltzbo .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#ztgbwltzbo .gt_column_spanner {
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#ztgbwltzbo .gt_spanner_row {
  border-bottom-style: hidden;
}

#ztgbwltzbo .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#ztgbwltzbo .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#ztgbwltzbo .gt_from_md > :first-child {
  margin-top: 0;
}

#ztgbwltzbo .gt_from_md > :last-child {
  margin-bottom: 0;
}

#ztgbwltzbo .gt_row {
  padding-top: 7px;
  padding-bottom: 7px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#ztgbwltzbo .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#ztgbwltzbo .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#ztgbwltzbo .gt_row_group_first td {
  border-top-width: 2px;
}

#ztgbwltzbo .gt_row_group_first th {
  border-top-width: 2px;
}

#ztgbwltzbo .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ztgbwltzbo .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#ztgbwltzbo .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#ztgbwltzbo .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ztgbwltzbo .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ztgbwltzbo .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#ztgbwltzbo .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}

#ztgbwltzbo .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#ztgbwltzbo .gt_table_body {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #FFFFFF;
}

#ztgbwltzbo .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ztgbwltzbo .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ztgbwltzbo .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ztgbwltzbo .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ztgbwltzbo .gt_left {
  text-align: left;
}

#ztgbwltzbo .gt_center {
  text-align: center;
}

#ztgbwltzbo .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#ztgbwltzbo .gt_font_normal {
  font-weight: normal;
}

#ztgbwltzbo .gt_font_bold {
  font-weight: bold;
}

#ztgbwltzbo .gt_font_italic {
  font-style: italic;
}

#ztgbwltzbo .gt_super {
  font-size: 65%;
}

#ztgbwltzbo .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}

#ztgbwltzbo .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#ztgbwltzbo .gt_indent_1 {
  text-indent: 5px;
}

#ztgbwltzbo .gt_indent_2 {
  text-indent: 10px;
}

#ztgbwltzbo .gt_indent_3 {
  text-indent: 15px;
}

#ztgbwltzbo .gt_indent_4 {
  text-indent: 20px;
}

#ztgbwltzbo .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="Player">Player</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="City">City</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="State">State</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: 'Source Sans Pro'; text-transform: uppercase;" scope="col" id="To">To</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Julio Jones</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Foley</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Carson Tinker</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Decatur</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Jordan Matthews</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Madison</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Nick Williams</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Birmingham</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">C.J. Mosley</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Theodore</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
    <tr><td headers="Player" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Za'Darius Smith</td>
<td headers="City" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">Montgomery</td>
<td headers="State" class="gt_row gt_left" style="font-family: 'Source Sans Pro'; font-weight: 400;">AL</td>
<td headers="To" class="gt_row gt_right" style="font-family: 'Source Sans Pro'; font-weight: 400;">2023</td></tr>
  </tbody>
  
  
</table>
</div>

Now that we have the process for pulling data for one state, we can simply refactor our code into a for-loop and run through all 50 states. But there is one other issue we need to resolve first.

The table for Alabama returned 200 players. In reality though, Alabama has had 788 players in the NFL. It turns out that the site limits the table rows to 200 per page. We’ll need to incorporate this information into our for-loop.

Here is the final nested for-loop. We first grab the page for the first state in the list and load the first table. We then iterate through each page adding “200 to the URL”&offset=” plus the value of the offset (adding 200 each time). Once we have grabbed all of the tables for a state, we move to the next state in the list.

``` r
# list of states and the ISO codes used in the website URL
state_table = read_csv("./state_iso2_codes.csv")
states = state_table$code

# The website paginates after each 200 players
offsets = seq(0,2600, 200)

#create function
get_cfb = function(states) {
  offsets = seq(0,2600, 200)
  for(i in states) {
    for(x in offsets){
      url = paste0("https://www.pro-football-reference.com/friv/birthplaces.cgi?country=USA&state=", i, "&offset=", x)
      html <- read_html(url)
      node <- html_node(html, "table")
      table <- html_table(node, header = T)}
  
    #add country slug to table
      table$state <- i
  
    #add to empty table
    all_players = rbind(table, all_players)}}
```

The above for-loop worked pretty well but be aware that the sports-reference site will throttle bulk requests. I ended up splitting my calls into batches of 3 states at a time. For anyone who wants to replicate this process, I recently learned about the [polite package](https://cran.r-project.org/web/packages/polite/index.html) that will manage this process for you.
