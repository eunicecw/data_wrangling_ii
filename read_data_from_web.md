Read Data From the Web
================

Load the necessary libraries.

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

Import NSDUH data

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

## import the data website url

nsduh_html = 
  read_html(nsduh_url)
```

``` r
marj_use_df = 
  nsduh_html |> 
  html_table() |> ##bec the original website looks like a table,import every single table, stored as a list of table
  first() |> ## take the first element of the list; here is the 1st table
  slice(-1) ## bec the first row is notation, remove it from the table use this function
```

Import star wars …

``` r
swm_url = 
  "https://www.imdb.com/list/ls070150896/"

swm_html = 
  read_html(swm_url)
```

``` r
swm_title_vec = 
  swm_html |> 
  html_elements(".lister-item-header a") |> ##Here I want the title of movie. For each element, I’ll use the CSS selector(selector gadget in website, select the thing you want, double click for things don't want, copy the code appear) in html_elements() to extract the relevant HTML code, and convert it to text. 
  html_text() ## get the text out from html

swm_gross_rev_vec = 
  swm_html |> 
  html_elements(".text-small:nth-child(7) span:nth-child(5)") |> #change the selector gadeget to get a new code
  html_text()
 
## Then create a data frame based on title and revenue
swm_df = 
  tibble(
    title = swm_title_vec,
    gross_rev = swm_gross_rev_vec
  )
```

## APIs Application Programming Interfaces

Get water data from NYC.

``` r
nyc_water_df = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |> ##Get data from api,set to csv
  content() #clean the data, request the content, delete irrelevant info
```

    ## Rows: 44 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
## content("parsed")---show content, show look differently,can use parsed to let it help to guess what happens
```

BRFSS Data

``` r
brfss_df = 
  GET(
    "https://data.cdc.gov/resource/acme-vg9e.csv",
    query = list("$limit" = 5000)) |> ##see this from API Docs, ask for a limit
  content()
```

    ## Rows: 5000 Columns: 23
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Try it now! Pokemon API

Some API are not perfectly organized like this

``` r
poke_df = 
  GET("https://pokeapi.co/api/v2/pokemon/ditto") |> 
  content()
```
