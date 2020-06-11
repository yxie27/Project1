Project 1
================
Yilin Xie
June 12, 2020

## Data type (JSON) description

### What it is

JSON, short for JavaScript Object Notation, is a data exchange format.
It is a data format popularized by Douglas Crockford in 2001
(<https://www.json.org/json-en.html>), became the mainstream data format
since 2005-2006. JSON comes in a text format that is completely
independent of any programming language, making it an ideal data
exchange language. JSON is the syntax for storing and exchanging textual
information, similar to XML.

### Where it gets used

There is no other place where JSON is broadly used more than the Web.
Now the data interface basically returns JSON, and the detailed
scenarios include:

  - Ajxa accesses data asynchronously
  - RPC remote call
  - The front and back end separate the data returned by the back end
  - Open API, such as Baidu, Aude and other open interfaces
  - Interface for cooperation between enterprises
  - Simplify object creation in JS

### Why it is a good way to store data

JSON was born because the details of XML integration into HTML vary from
browser to browser. One drawback of XML formats, however, is the
complexity of document construction and the need to transfer more bytes.
In this case, JSON’s portability gained traction, replacing XML as the
dominant data transfer format. JSON is smaller, faster, and easier to
parse than XML. The biggest difference between XML and JSON is that it
is full of redundant information. Most of the time we don’t need
redundant information, but we just can’t do without it when we need it.
This is the biggest difference between XML and JSON. Json exists as a
typical 20% feature that addresses 80% of the requirements. In other
words, the fly swatter is found to be more handy where a cannon would
have to be used against mosquitoes. The concise and clear hierarchy
makes JSON an ideal data exchange language. Easy to read and write, and
also easy to machine parsing and generation, and effectively improve the
network transmission efficiency.

## Discussion of relevant R packages

Primaryly, there are three packages used for reading JSON data into R,
which are `rjson`, `RJSONIO`, and `jsonlite`.

### rjson

`rjson` was first implemented for R in 2007 by Alex Couture-Beil. It
allows R users to convert JSON objects into R object and vice-verse.
There are three functions available under `rjson` package: *fromJSON*,
*toJSON* and *newJSONParser*.

### RJSONIO

`RJSONIO` was created in 2010 by Duncan Temple Lang on GitHub. It allows
the R objects are inserted into the JavaScript/ECMAScript/ActionScript
code, and allow R programmers reading the JSON content and transform it
into R objects. `RJSONIO` is extensible, but it does not use the S4/S3
method. It allows others to define S4 methods for different R
classes/types, as well as for callers to specify different callback
handlers. Unlike the rjson package, the `RJSONIO` package USES a c++
library-libjson, instead of implementing another JSON parser, which
parses faster than pure interpreted R code. There are three main
functions in this package :*fromJSON*, *toJSON*, and *asJSVars*.

### jsonlite

`jsonlite` started from 2013 but has been completely rewritten in recent
versions. Like `RJSONIO`, it also provides functions, such as
*fromJSON()* and *toJSON() *to convert between JSON data and R objects.
It could also interact with web APIs, building pipelines and streaming
data between R and JSON.

I would choose `jsonlite` in to practice for several reasons:

  - `jsonlite` provides base64\_dec and base64\_enc to convert between
    raw vectors to text while the other two packages don’t have this
    function.
  - Validating strings in JSON format is provided by `RJSONIO`
    (isJSONValid function) and `jsonlite` (validate) while `rjson`
    doesn’t have.
  - `jsonlite` also provides the capability of re-formatting JSON file
    into: 1). structure with indentation added from prettify, 2). file
    by removing all unnecessary indentation and white spaces which is
    actually adopted by a lot of JavaScript libraries.

For further information, [this
page](https://rstudio-pubs-static.s3.amazonaws.com/31702_9c22e3d1a0c44968a4a1f9656f1800ab.html)
gives readers a brief comparison between three packages.

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