Project 1
================

add a line

``` r
#load the tidyverse package
library(tidyverse)
```

    ## Warning: package 'tibble' was built under R version 3.6.2

    ## Warning: package 'tidyr' was built under R version 3.6.2

    ## Warning: package 'purrr' was built under R version 3.6.2

``` r
#load the httr and jsonlite packages
library(httr)
library(jsonlite)
```

``` r
#write a function
myfunc <- function(x){
  full_url <- paste0("https://records.nhl.com/site/api", "/", x)
  #Returns id, firstSeasonId and lastSeasonId and name of every team in the history of the NHL
  if(x == "franchise"){
    franchise <- GET(full_url) %>% content("text") %>% fromJSON(flatten=TRUE)
    data <- franchise$data %>% select(id,firstSeasonId,lastSeasonId,teamCommonName)
    tbl_df(data)
  }
  #Returns Total stats for every franchise
  else if(x == "franchise-team-totals"){
    franchise <- GET(full_url) %>% content("text") %>% fromJSON(flatten=TRUE)
    data <- franchise$data %>% select(id,roadTies,roadWins)
    tbl_df(data)
  }
  #Drill-down into season records for a specific franchise
  else if (x %in% "franchise-season-records?cayenneExp=franchiseId="){
    franchiseId <- substr(x, 49, 50)
    #convert a character vector to a numeric vector
    franchiseId <- as.numeric(franchiseId)
    franchise <- GET(full_url) %>% content("text") %>% fromJSON(flatten=TRUE)
    franchise$data %>% filter(id == franchiseId)
  }
  #Goalie records for the specified franchise
  else if (x %in% "franchise-goalie-records?cayenneExp=franchiseId="){
    franchiseId <- substr(x, 49, 52)
    #convert a character vector to a numeric vector
    franchiseId <- as.numeric(franchiseId)
    franchise <- GET(full_url) %>% content("text") %>% fromJSON(flatten=TRUE)
    franchise$data %>% filter(id == franchiseId)
  }
  #Skater records, same interaction as goalie endpoint
  else if (x %in% "franchise-skater-records?cayenneExp=franchiseId="){
    franchiseId <- substr(x, 49, 53)
    #convert a character vector to a numeric vector
    franchiseId <- as.numeric(franchiseId)
    franchise <- GET(full_url) %>% content("text") %>% fromJSON(flatten=TRUE)
    franchise$data %>% filter(id == franchiseId)
  }
}
myfunc("franchise")
```

    ## # A tibble: 38 x 4
    ##       id firstSeasonId lastSeasonId teamCommonName
    ##    <int>         <int>        <int> <chr>         
    ##  1     1      19171918           NA Canadiens     
    ##  2     2      19171918     19171918 Wanderers     
    ##  3     3      19171918     19341935 Eagles        
    ##  4     4      19191920     19241925 Tigers        
    ##  5     5      19171918           NA Maple Leafs   
    ##  6     6      19241925           NA Bruins        
    ##  7     7      19241925     19371938 Maroons       
    ##  8     8      19251926     19411942 Americans     
    ##  9     9      19251926     19301931 Quakers       
    ## 10    10      19261927           NA Rangers       
    ## # … with 28 more rows

``` r
myfunc("franchise-team-totals")
```

    ## # A tibble: 104 x 3
    ##       id roadTies roadWins
    ##    <int>    <int>    <int>
    ##  1     1      123      592
    ##  2     2       NA       63
    ##  3     3      177      714
    ##  4     4       NA       64
    ##  5     5      360     1256
    ##  6     6        7      107
    ##  7     7       NA       90
    ##  8     8      264      850
    ##  9     9      178      750
    ## 10    10       NA       95
    ## # … with 94 more rows

``` r
myfunc("franchise-season-records?cayenneExp=franchiseId=10")
myfunc("franchise-goalie-records?cayenneExp=franchiseId=235")
myfunc("franchise-skater-records?cayenneExp=franchiseId=16891")
#attributes(franchise)
#franchise$data
```
