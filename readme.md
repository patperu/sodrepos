
`sodrepos` — Collect information about `spaghetti-open-data` repos using `ghql`

### Todo:

-   Get the date of the first and last commit

Load libraries
--------------

``` r
library("dplyr")
library("httr")
library("jsonlite")
library("ghql")

options(stringsAsFactors = FALSE)
```

Initialize Client
-----------------

``` r
token <- Sys.getenv("GITHUB_GRAPHQL_TOKEN")

cli <- graphql(
  url = "https://api.github.com/graphql",
  headers = add_headers(Authorization = paste0("Bearer ", token))
)
```

Building Query
--------------

``` r
cli$query('
{
  repositoryOwner(login: "spaghetti-open-data") {
    repositories(first:30, orderBy: {field:PUSHED_AT, direction:DESC}) {
      edges {
        node {
          name
          isFork
          homepageURL
          stargazers{
            totalCount
          }
          watchers{
            totalCount
          }
          }
        }
      }
    }
  }
')
```

``` r
cli$query_string
## [1] "{  repositoryOwner(login: \"spaghetti-open-data\") {    repositories(first:30, orderBy: {field:PUSHED_AT, direction:DESC}) {      edges {        node {          name          isFork          homepageURL          stargazers{            totalCount          }          watchers{            totalCount          }          }        }      }    }  }"
```

``` r
res <- cli$exec()
res <- res$data$repositoryOwner$repositories$edges$node
res <- tbl_df(flatten(res))
```

Result
------

``` r
res %>% knitr::kable()
```

| name                              | isFork | homepageURL                  |  stargazers.totalCount|  watchers.totalCount|
|:----------------------------------|:-------|:-----------------------------|----------------------:|--------------------:|
| eu-research-funding-network       | FALSE  | NA                           |                      5|                   12|
| code4health-amianto               | FALSE  |                              |                      4|                   11|
| ar-kan                            | FALSE  | NA                           |                      1|                   10|
| visualizing-self-diagnosis        | FALSE  | NA                           |                      4|                    9|
| fp7-funding-network-analysis      | FALSE  | NA                           |                      8|                    8|
| opengov\_books                    | TRUE   | NA                           |                      4|                    5|
| beni-confiscati-aperti            | FALSE  | NA                           |                     18|                   19|
| scriptorivm                       | FALSE  | NA                           |                      2|                   18|
| Potenza-gtfs                      | FALSE  | NA                           |                      0|                   14|
| osservatorio-carburanti           | FALSE  | NA                           |                      2|                    4|
| bussola\_scraper                  | FALSE  | NA                           |                      1|                   39|
| checklist-piattaforme-opendata    | TRUE   | NA                           |                      3|                   38|
| documentazione                    | FALSE  | NA                           |                      1|                   47|
| tweetyourmep                      | FALSE  | NA                           |                     12|                   40|
| twitAntonio                       | FALSE  | <http://www.twitantonio.it>  |                     17|                   36|
| nodejs-simple-twitter-harvester   | TRUE   |                              |                      5|                    5|
| sod14                             | TRUE   | NA                           |                      6|                   30|
| acqualta                          | TRUE   | NA                           |                      1|                    4|
| lascra-elezionistorico-interno-it | TRUE   | NA                           |                      0|                    2|
| scraper-elezionistorico           | FALSE  | NA                           |                      4|                   36|
| node-live-earthquakemap           | TRUE   | NA                           |                      4|                   36|
| edgeryders-mapper                 | TRUE   | NA                           |                      2|                   36|
| node-express-twitter-bootstrap    | TRUE   |                              |                      1|                   36|
| opendatamanual                    | TRUE   | <http://opendatamanual.org/> |                      2|                   37|
| Drupal-Istat                      | TRUE   | <http://www.twinbit.it>      |                      0|                   36|

Session Info
------------

``` r
devtools::session_info()
##  setting  value                       
##  version  R version 3.3.1 (2016-06-21)
##  system   x86_64, linux-gnu           
##  ui       X11                         
##  language en_US                       
##  collate  en_US.UTF-8                 
##  tz       <NA>                        
##  date     2016-09-24                  
## 
##  package    * version    date       source                        
##  assertthat   0.1        2013-12-06 CRAN (R 3.3.0)                
##  curl         2.1        2016-09-22 CRAN (R 3.3.1)                
##  DBI          0.5-1      2016-09-10 CRAN (R 3.3.1)                
##  devtools     1.12.0     2016-06-24 CRAN (R 3.3.1)                
##  digest       0.6.10     2016-08-02 CRAN (R 3.3.1)                
##  dplyr      * 0.5.0      2016-06-24 CRAN (R 3.3.1)                
##  evaluate     0.9        2016-04-29 CRAN (R 3.3.0)                
##  formatR      1.4        2016-05-09 CRAN (R 3.3.1)                
##  ghql       * 0.0.2.9100 2016-09-24 Github (ropensci/ghql@56d9678)
##  highr        0.6        2016-05-09 CRAN (R 3.3.1)                
##  htmltools    0.3.5      2016-03-21 CRAN (R 3.3.0)                
##  httr       * 1.2.1      2016-07-03 CRAN (R 3.3.1)                
##  jsonlite   * 1.1        2016-09-14 CRAN (R 3.3.1)                
##  knitr        1.14       2016-08-13 CRAN (R 3.3.1)                
##  lazyeval     0.2.0      2016-06-12 CRAN (R 3.3.0)                
##  magrittr     1.5        2014-11-22 CRAN (R 3.3.0)                
##  memoise      1.0.0      2016-01-29 CRAN (R 3.3.0)                
##  R6           2.1.3      2016-08-19 CRAN (R 3.3.1)                
##  Rcpp         0.12.7     2016-09-05 CRAN (R 3.3.1)                
##  rmarkdown    1.0        2016-07-08 CRAN (R 3.3.1)                
##  stringi      1.1.1      2016-05-27 CRAN (R 3.3.0)                
##  stringr      1.1.0      2016-08-19 CRAN (R 3.3.1)                
##  tibble       1.2        2016-08-26 CRAN (R 3.3.1)                
##  withr        1.0.2      2016-06-20 CRAN (R 3.3.1)                
##  yaml         2.1.13     2014-06-12 CRAN (R 3.3.0)
```
