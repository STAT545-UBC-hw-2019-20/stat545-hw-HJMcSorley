---
title: "Homework Assignment 4: Tidy Data and Joins "
author: "Hannah McSorley"
date: "Due 2019-10-08"
output: 
  html_document:
    keep_md: true
    theme: cerulean
---

## Assignment Objective

The focus of this assignment is to practice problem solving in R using two key components of data wrangling: data aggregation and reshaping. In addition, data visualization with ggplo2 will be included. 
The data used in this assignment includes 'gapminder' (for Exercises 1 & 2), and two wedding-planning data sets (for Exercise 3).




```r
library(tidyverse)
library(gapminder)
library(knitr)  # for tidy tables
library(DT)     # for datatable in html
```

# Exercise 1: Univariate Data Reshaping 

__Option 2__

Three Goals:

- 1. Reshape data into wider format
- 2. Produce a plot of the wide data
- 3. Re-lengthening the wider data

This code creates two wide tibbles, with years/continents as either rows or columns.


```r
# mutate and reshape data into wider format
## use tidyr::pivot_wider()
# A: rows = year, columns = continents
A_wide <- gapminder %>% 
  dplyr::arrange(continent, year) %>% 
  group_by(continent, year) %>% 
  summarize(med_lifeExp = median(lifeExp)) %>% 
  tidyr::pivot_wider(id_cols = year,
                         names_from = continent,
                         values_from = med_lifeExp)
# table
knitr::kable(A_wide, digits = 0, 
      caption = "Table 1A-Wide: continental median life expectancy")
```



Table: Table 1A-Wide: continental median life expectancy

 year   Africa   Americas   Asia   Europe   Oceania
-----  -------  ---------  -----  -------  --------
 1952       39         55     45       66        69
 1957       41         56     48       68        70
 1962       43         58     49       70        71
 1967       45         61     54       71        71
 1972       47         63     57       71        72
 1977       49         66     61       72        73
 1982       51         67     64       73        74
 1987       52         69     66       75        75
 1992       52         70     69       75        77
 1997       53         72     70       76        78
 2002       51         72     71       78        80
 2007       53         73     72       79        81

```r
# B: rows = continents, columns = years
B_wide <- gapminder %>% 
  dplyr::arrange(continent, year) %>% 
  group_by(continent, year) %>% 
  summarize(med_lifeExp = median(lifeExp)) %>% 
  tidyr::pivot_wider(id_cols = continent,
                         names_from = year,
                         values_from = med_lifeExp) 
# table
knitr::kable(B_wide, digits = 0, 
      caption = "Table 1B-Wide: median life expectancy over time by continent")
```



Table: Table 1B-Wide: median life expectancy over time by continent

continent    1952   1957   1962   1967   1972   1977   1982   1987   1992   1997   2002   2007
----------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
Africa         39     41     43     45     47     49     51     52     52     53     51     53
Americas       55     56     58     61     63     66     67     69     70     72     72     73
Asia           45     48     49     54     57     61     64     66     69     70     71     72
Europe         66     68     70     71     71     72     73     75     75     76     78     79
Oceania        69     70     71     71     72     73     74     75     77     78     80     81

Visually, it might be more intuitive to read Table 1B-Wide, with years progressing from left to right. However, it seems difficult to plot from that tibble shape, especially because the variables are numbers (years). 


```r
# Produce a plot of (some of) the wide data
# B (easy to read)
B_wide %>% ggplot(aes(x = 1952, y = 2007)) + 
  geom_point(colour = "magenta2") +
  theme_light()
```

<img src="hw04_HJMc_TidyData-and-joins_files/figure-html/E1 wide plot-1.png" style="display: block; margin: auto;" />

```r
# the data from Table 1-B is difficult to plot (at least for me)
# the variables are numbers (years), and are treated as discrete numbers in ggplot

# try plotting the data from Table 1A-Wide
# A
A_wide %>% ggplot(aes(x = year, y = Africa)) + 
  geom_point(colour = "steelblue4") +
  theme_light()
```

<img src="hw04_HJMc_TidyData-and-joins_files/figure-html/E1 wide plot-2.png" style="display: block; margin: auto;" />

```r
# it's easier to plot a contient's median life expectancy over time from Table 1-A
# trickier to plot multiple contients over time though...that's easier from long format

# easy to plot one continent against another
A_wide %>% ggplot(aes(x = Americas, y = Asia)) + 
  geom_point(colour = "tomato") +
  theme_light()
```

<img src="hw04_HJMc_TidyData-and-joins_files/figure-html/E1 wide plot-3.png" style="display: block; margin: auto;" />

It seems that the data shape which is easiest to read, or most intuitive to present, is the most difficult to plot in R. Whereas longer tibbles are easier for R to read and plot. 


```r
# Re-lengthening the wider data
## use tidyr::pivot_longer()
#A
A_long <- A_wide %>% 
  tidyr::pivot_longer(cols = Africa:Oceania,
                      names_to = "continent",
                      values_to = "median_lifeExp") %>% 
  arrange(continent)
# table
DT::datatable(A_long, caption = "Table 1A-Long: continental median life expectancy")
```

<!--html_preserve--><div id="htmlwidget-46ce5b680c59fb04ceae" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-46ce5b680c59fb04ceae">{"x":{"filter":"none","caption":"<caption>Table 1A-Long: continental median life expectancy<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60"],[1952,1957,1962,1967,1972,1977,1982,1987,1992,1997,2002,2007,1952,1957,1962,1967,1972,1977,1982,1987,1992,1997,2002,2007,1952,1957,1962,1967,1972,1977,1982,1987,1992,1997,2002,2007,1952,1957,1962,1967,1972,1977,1982,1987,1992,1997,2002,2007,1952,1957,1962,1967,1972,1977,1982,1987,1992,1997,2002,2007],["Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania"],[38.833,40.5925,42.6305,44.6985,47.0315,49.2725,50.756,51.6395,52.429,52.759,51.2355,52.9265,54.745,56.074,58.299,60.523,63.441,66.353,67.405,69.498,69.862,72.146,72.047,72.899,44.869,48.284,49.325,53.655,56.95,60.765,63.739,66.295,68.69,70.265,71.028,72.396,65.9,67.65,69.525,70.61,70.885,72.335,73.49,74.815,75.451,76.116,77.5365,78.6085,69.255,70.295,71.085,71.31,71.91,72.855,74.29,75.32,76.945,78.19,79.74,80.7195]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>year<\/th>\n      <th>continent<\/th>\n      <th>median_lifeExp<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[1,3]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
# check that you produced what you wanted:
gapminder %>% 
  dplyr::arrange(continent, year) %>% 
  group_by(continent, year) %>% 
  summarize(med_lifeExp = median(lifeExp))
```

```
## # A tibble: 60 x 3
## # Groups:   continent [5]
##    continent  year med_lifeExp
##    <fct>     <int>       <dbl>
##  1 Africa     1952        38.8
##  2 Africa     1957        40.6
##  3 Africa     1962        42.6
##  4 Africa     1967        44.7
##  5 Africa     1972        47.0
##  6 Africa     1977        49.3
##  7 Africa     1982        50.8
##  8 Africa     1987        51.6
##  9 Africa     1992        52.4
## 10 Africa     1997        52.8
## # ... with 50 more rows
```

```r
# Yes!

# Re-lengthen the data from Table 1-B also
B_long <- B_wide %>% 
  tidyr::pivot_longer(cols = (2:13),
                      names_to = "year",
                      values_to = "median_lifeExp") %>% 
  arrange(continent)
# table
DT::datatable(B_long, caption = "Table 1B-Long: continental median life expectancy")
```

<!--html_preserve--><div id="htmlwidget-f5517f608025250effa4" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-f5517f608025250effa4">{"x":{"filter":"none","caption":"<caption>Table 1B-Long: continental median life expectancy<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60"],["Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Africa","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Americas","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Asia","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Europe","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania","Oceania"],["1952","1957","1962","1967","1972","1977","1982","1987","1992","1997","2002","2007","1952","1957","1962","1967","1972","1977","1982","1987","1992","1997","2002","2007","1952","1957","1962","1967","1972","1977","1982","1987","1992","1997","2002","2007","1952","1957","1962","1967","1972","1977","1982","1987","1992","1997","2002","2007","1952","1957","1962","1967","1972","1977","1982","1987","1992","1997","2002","2007"],[38.833,40.5925,42.6305,44.6985,47.0315,49.2725,50.756,51.6395,52.429,52.759,51.2355,52.9265,54.745,56.074,58.299,60.523,63.441,66.353,67.405,69.498,69.862,72.146,72.047,72.899,44.869,48.284,49.325,53.655,56.95,60.765,63.739,66.295,68.69,70.265,71.028,72.396,65.9,67.65,69.525,70.61,70.885,72.335,73.49,74.815,75.451,76.116,77.5365,78.6085,69.255,70.295,71.085,71.31,71.91,72.855,74.29,75.32,76.945,78.19,79.74,80.7195]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>continent<\/th>\n      <th>year<\/th>\n      <th>median_lifeExp<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":3},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

# Exercise 2: Multivariate Data Reshaping 

Two Goals:

- 1. Reshape data into wider format
- 2. Re-lengthening the wider data

__Option 1__ 
_Specifically_

- 1. Make a tibble with one row per year, and columns for life expectancy and GDP per capita (or two other numeric variables) for two or more countries.
- 2. Re-lengthen the data



```r
# filter for two countries' lifeExp & gdpPercap
# pivot_wider()
# then pivot back (pivot_longer())
E2_wide <- gapminder %>% 
  dplyr::arrange(country, year) %>% 
  select(-pop, -continent) %>%
  filter(country == "Guinea" | 
           country == "Slovenia") %>%
  pivot_wider(id_cols = year,
              names_from = country,
              values_from = c(lifeExp, gdpPercap))
knitr::kable(E2_wide, digits = c(0, 0, 0, 2, 2), 
             caption = "Wide format: multivariate countries' GDP and life expectancy")
```



Table: Wide format: multivariate countries' GDP and life expectancy

 year   lifeExp_Guinea   lifeExp_Slovenia   gdpPercap_Guinea   gdpPercap_Slovenia
-----  ---------------  -----------------  -----------------  -------------------
 1952               34                 66             510.20              4215.04
 1957               35                 68             576.27              5862.28
 1962               36                 69             686.37              7402.30
 1967               37                 69             708.76              9405.49
 1972               39                 70             741.67             12383.49
 1977               41                 71             874.69             15277.03
 1982               43                 71             857.25             17866.72
 1987               46                 72             805.57             18678.53
 1992               49                 74             794.35             14214.72
 1997               51                 75             869.45             17161.11
 2002               54                 77             945.58             20660.02
 2007               56                 78             942.65             25768.26

Take the wider tibble and pivot it back to long format. 

```r
# filter for two countries' lifeExp & gdpPercap
# pivot_wider()
# then pivot back (pivot_longer())
E2_longer <- E2_wide %>% 
   pivot_longer(cols = -year,
              names_to = c(".value", "Country"),
              names_sep = "_",
              values_to = c("lifeExp", "gdpPercap")) %>% 
   arrange(Country)
knitr::kable(E2_longer, digits = c(0, 0, 0, 2, 2), 
             caption = "Wide format: multivariate countries' GDP and life expectancy")
```



Table: Wide format: multivariate countries' GDP and life expectancy

 year  Country     lifeExp   gdpPercap
-----  ---------  --------  ----------
 1952  Guinea           34      510.20
 1957  Guinea           35      576.27
 1962  Guinea           36      686.37
 1967  Guinea           37      708.76
 1972  Guinea           39      741.67
 1977  Guinea           41      874.69
 1982  Guinea           43      857.25
 1987  Guinea           46      805.57
 1992  Guinea           49      794.35
 1997  Guinea           51      869.45
 2002  Guinea           54      945.58
 2007  Guinea           56      942.65
 1952  Slovenia         66     4215.04
 1957  Slovenia         68     5862.28
 1962  Slovenia         69     7402.30
 1967  Slovenia         69     9405.49
 1972  Slovenia         70    12383.49
 1977  Slovenia         71    15277.03
 1982  Slovenia         71    17866.72
 1987  Slovenia         72    18678.53
 1992  Slovenia         74    14214.72
 1997  Slovenia         75    17161.11
 2002  Slovenia         77    20660.02
 2007  Slovenia         78    25768.26


# Exercise 3: Table Joins

Read in the made-up wedding guestlist and email addresses:


```r
guest <- read_csv("https://raw.githubusercontent.com/STAT545-UBC/Classroom/master/data/wedding/attend.csv")
```

```
## Parsed with column specification:
## cols(
##   party = col_double(),
##   name = col_character(),
##   meal_wedding = col_character(),
##   meal_brunch = col_character(),
##   attendance_wedding = col_character(),
##   attendance_brunch = col_character(),
##   attendance_golf = col_character()
## )
```

```r
email <- read_csv("https://raw.githubusercontent.com/STAT545-UBC/Classroom/master/data/wedding/emails.csv")
```

```
## Parsed with column specification:
## cols(
##   guest = col_character(),
##   email = col_character()
## )
```

```r
# check the top of each data set:
head(guest)
```

```
## # A tibble: 6 x 7
##   party name  meal_wedding meal_brunch attendance_wedd~ attendance_brun~
##   <dbl> <chr> <chr>        <chr>       <chr>            <chr>           
## 1     1 Somm~ PENDING      PENDING     PENDING          PENDING         
## 2     1 Phil~ vegetarian   Menu C      CONFIRMED        CONFIRMED       
## 3     1 Blan~ chicken      Menu A      CONFIRMED        CONFIRMED       
## 4     1 Emaa~ PENDING      PENDING     PENDING          PENDING         
## 5     2 Blai~ chicken      Menu C      CONFIRMED        CONFIRMED       
## 6     2 Nige~ <NA>         <NA>        CANCELLED        CANCELLED       
## # ... with 1 more variable: attendance_golf <chr>
```

```r
head(email)
```

```
## # A tibble: 6 x 2
##   guest                                                   email            
##   <chr>                                                   <chr>            
## 1 Sommer Medrano, Phillip Medrano, Blanka Medrano, Emaan~ sommm@gmail.com  
## 2 Blair Park, Nigel Webb                                  bpark@gmail.com  
## 3 Sinead English                                          singlish@hotmail~
## 4 Ayra Marks                                              marksa42@gmail.c~
## 5 Jolene Welsh, Hayley Booker                             jw1987@hotmail.c~
## 6 Amayah Sanford, Erika Foley                             erikaaaaaa@gmail~
```

## 3.1

For each guest in the guestlist (guest tibble), add a column for email address, which can be found in the email tibble.

___Note:___
Before joining these tibbles, I separated names that were listed (probably by wedding party) in the tibble 'email'. I found how to use 'strsplit()' with 'unlist()' to accomplish the unlisting on [StackOverflow](https://stackoverflow.com/questions/15347282/split-delimited-strings-in-a-column-and-insert-as-new-rows).



```r
# mutating join
# add data from 'email' to 'guest'
guest %>% 
  left_join(email %>% 
              mutate(guest = strsplit(as.character(guest), ", ")) %>% 
              unnest(guest), # split the lists of guests from tibble 'email' 
            by = c("name" = "guest")) %>%   # the two tibbles have different key names
  DT::datatable(caption = "guestlist with emails")
```

<!--html_preserve--><div id="htmlwidget-7f9cd4ba4bb4f47c1c9b" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-7f9cd4ba4bb4f47c1c9b">{"x":{"filter":"none","caption":"<caption>guestlist with emails<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30"],[1,1,1,1,2,2,3,4,5,5,5,6,6,7,7,8,9,10,11,12,12,12,12,12,13,13,14,14,15,15],["Sommer Medrano","Phillip Medrano","Blanka Medrano","Emaan Medrano","Blair Park","Nigel Webb","Sinead English","Ayra Marks","Atlanta Connolly","Denzel Connolly","Chanelle Shah","Jolene Welsh","Hayley Booker","Amayah Sanford","Erika Foley","Ciaron Acosta","Diana Stuart","Cosmo Dunkley","Cai Mcdaniel","Daisy-May Caldwell","Martin Caldwell","Violet Caldwell","Nazifa Caldwell","Eric Caldwell","Rosanna Bird","Kurtis Frost","Huma Stokes","Samuel Rutledge","Eddison Collier","Stewart Nicholls"],["PENDING","vegetarian","chicken","PENDING","chicken",null,"PENDING","vegetarian","PENDING","fish","chicken",null,"vegetarian",null,"PENDING","PENDING","vegetarian","PENDING","fish","chicken","PENDING","PENDING","chicken","chicken","vegetarian","PENDING",null,"chicken","PENDING","chicken"],["PENDING","Menu C","Menu A","PENDING","Menu C",null,"PENDING","Menu B","PENDING","Menu B","Menu C",null,"Menu C","PENDING","PENDING","Menu A","Menu C","PENDING","Menu C","Menu B","PENDING","PENDING","PENDING","Menu B","Menu C","PENDING",null,"Menu C","PENDING","Menu B"],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","CANCELLED","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED"],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED"],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED"],["sommm@gmail.com","sommm@gmail.com","sommm@gmail.com","sommm@gmail.com","bpark@gmail.com","bpark@gmail.com","singlish@hotmail.ca","marksa42@gmail.com",null,null,null,"jw1987@hotmail.com","jw1987@hotmail.com","erikaaaaaa@gmail.com","erikaaaaaa@gmail.com","shining_ciaron@gmail.com","doodledianastu@gmail.com",null,null,"caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","rosy1987b@gmail.com","rosy1987b@gmail.com","humastokes@gmail.com","humastokes@gmail.com","eddison.collier@gmail.com","eddison.collier@gmail.com"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>party<\/th>\n      <th>name<\/th>\n      <th>meal_wedding<\/th>\n      <th>meal_brunch<\/th>\n      <th>attendance_wedding<\/th>\n      <th>attendance_brunch<\/th>\n      <th>attendance_golf<\/th>\n      <th>email<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":1},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


## 3.2 

Who do we have emails for, yet are not on the guestlist?

I used _anti_join()_, a filtering join that drops observations from the primary tibble (x = 'email') which don't have a match in the joining/reference tibble (y = 'guest').

Like E3.1, this code splits the string of names in the email data set into their own row before joining tibbles.


```r
# filtering join
# find 'email' guests who are not on the 'guests' list
# anti_join(x, y) drops all observations in x that have a match in y
email %>% 
  mutate(guest = strsplit(as.character(guest), ", ")) %>% 
  unnest(guest) %>% 
  anti_join(guest, by = c("guest" = "name")) %>% 
  knitr::kable(caption = "Guests with emails who are not on the guestlist")
```



Table: Guests with emails who are not on the guestlist

guest             email                           
----------------  --------------------------------
Turner Jones      tjjones12@hotmail.ca            
Albert Marshall   themarshallfamily1234@gmail.com 
Vivian Marshall   themarshallfamily1234@gmail.com 


# 3.3 

Make a guestlist that includes everyone we have emails for (in addition to those on the original guestlist). Again, separate the list of guests in 'email' into their own row before joining tibbles. 


```r
# make a super guestlist by joining both tibbles
# superlist will have all the folks from 'guest' tibble, whether we have their email or not, as well as folks who we have email for who weren't included on the guestlist.
full_join(guest, email %>% 
            mutate(guest = strsplit(as.character(guest), ", ")) %>% 
            unnest(guest), 
          by = c("name" = "guest")) %>% 
  DT::datatable(caption = "Super Guestlist: all wedding guests")
```

<!--html_preserve--><div id="htmlwidget-a9fb22f44b0fb25adc44" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-a9fb22f44b0fb25adc44">{"x":{"filter":"none","caption":"<caption>Super Guestlist: all wedding guests<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33"],[1,1,1,1,2,2,3,4,5,5,5,6,6,7,7,8,9,10,11,12,12,12,12,12,13,13,14,14,15,15,null,null,null],["Sommer Medrano","Phillip Medrano","Blanka Medrano","Emaan Medrano","Blair Park","Nigel Webb","Sinead English","Ayra Marks","Atlanta Connolly","Denzel Connolly","Chanelle Shah","Jolene Welsh","Hayley Booker","Amayah Sanford","Erika Foley","Ciaron Acosta","Diana Stuart","Cosmo Dunkley","Cai Mcdaniel","Daisy-May Caldwell","Martin Caldwell","Violet Caldwell","Nazifa Caldwell","Eric Caldwell","Rosanna Bird","Kurtis Frost","Huma Stokes","Samuel Rutledge","Eddison Collier","Stewart Nicholls","Turner Jones","Albert Marshall","Vivian Marshall"],["PENDING","vegetarian","chicken","PENDING","chicken",null,"PENDING","vegetarian","PENDING","fish","chicken",null,"vegetarian",null,"PENDING","PENDING","vegetarian","PENDING","fish","chicken","PENDING","PENDING","chicken","chicken","vegetarian","PENDING",null,"chicken","PENDING","chicken",null,null,null],["PENDING","Menu C","Menu A","PENDING","Menu C",null,"PENDING","Menu B","PENDING","Menu B","Menu C",null,"Menu C","PENDING","PENDING","Menu A","Menu C","PENDING","Menu C","Menu B","PENDING","PENDING","PENDING","Menu B","Menu C","PENDING",null,"Menu C","PENDING","Menu B",null,null,null],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","CANCELLED","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED",null,null,null],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED",null,null,null],["PENDING","CONFIRMED","CONFIRMED","PENDING","CONFIRMED","CANCELLED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","CANCELLED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","PENDING","CONFIRMED","CONFIRMED","PENDING","PENDING","PENDING","CONFIRMED","CONFIRMED","PENDING","CANCELLED","CONFIRMED","PENDING","CONFIRMED",null,null,null],["sommm@gmail.com","sommm@gmail.com","sommm@gmail.com","sommm@gmail.com","bpark@gmail.com","bpark@gmail.com","singlish@hotmail.ca","marksa42@gmail.com",null,null,null,"jw1987@hotmail.com","jw1987@hotmail.com","erikaaaaaa@gmail.com","erikaaaaaa@gmail.com","shining_ciaron@gmail.com","doodledianastu@gmail.com",null,null,"caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","caldwellfamily5212@gmail.com","rosy1987b@gmail.com","rosy1987b@gmail.com","humastokes@gmail.com","humastokes@gmail.com","eddison.collier@gmail.com","eddison.collier@gmail.com","tjjones12@hotmail.ca","themarshallfamily1234@gmail.com","themarshallfamily1234@gmail.com"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>party<\/th>\n      <th>name<\/th>\n      <th>meal_wedding<\/th>\n      <th>meal_brunch<\/th>\n      <th>attendance_wedding<\/th>\n      <th>attendance_brunch<\/th>\n      <th>attendance_golf<\/th>\n      <th>email<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":1},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

