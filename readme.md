
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
