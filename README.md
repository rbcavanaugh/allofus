
<!-- README.md is generated from README.Rmd. Please edit that file -->

# `allofus` R Package

The goal of the `allofus` R package is to streamline the use of R within
the [All of Us Researcher
Workbench](https://www.researchallofus.org/data-tools/workbench/).

*The `allofus` R package is a not affiliated with or endorsed by the All
of Us Research Program.*

The `allofus` package helps researchers query the database and build
transparent and reproducible analysed pipeline. The package allows
researchers to easily use other database packages, such as `dbplyr` and
`DBI`, with the All of Us database. The package also includes functions
to help researchers manage storage in their workspace and bucket, as
well as take advantage of tools developed for [OMOP
CDM](https://www.researchallofus.org/faq/what-is-omop/) data.

<!-- badges: start -->

[![R-CMD-check](https://github.com/roux-ohdsi/allofus/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/roux-ohdsi/allofus/actions/workflows/R-CMD-check.yaml)
[![CRAN
status](https://www.r-pkg.org/badges/version/allofus)](https://CRAN.R-project.org/package=allofus)
[![](http://cranlogs.r-pkg.org/badges/grand-total/allofus)](https://cran.r-project.org/package=allofus)
<!-- badges: end -->

### Installation

``` r
install.packages("remotes")
# or install dev version from GitHub
remotes::install_github("roux-ohdsi/allofus")
```

### Use

``` r
library(allofus)
```

Use `aou_connect()` to establish a database connection.

``` r
con <- aou_connect()
```

This connection object is a [DBI](https://dbi.r-dbi.org/) connection
object, so you can use it with other packages that use DBI connections,
such as [dbplyr](https://dbplyr.tidyverse.org/).

For example, you can use `tbl()` from
[dplyr](https://dplyr.tidyverse.org/) to access the person table from
the All of Us database:

``` r
person <- dplyr::tbl(con, "person")
```

You can then use `dplyr` functions on the table:

``` r
person %>%
  dplyr::filter(gender_concept_id == 8507)
```

and join the table to other tables in the database:

``` r
person %>%
  dplyr::left_join(dplyr::tbl(con, "observation"), by = "person_id")
```

The `aou_join()` function is a wrapper around the `dplyr` join functions
that allows you to join tables more easily:

``` r
person %>%
  aou_join("observation", type = "left")
```

See `vignette("sql", package = "allofus")` for more examples.

<!-- ## Example -->
<!-- Suppose we want to create a cohort of All of Us participants who have experienced a stroke. There are a couple of ways we could go about this. -->
<!-- ### Using the observation table -->
<!-- The following code would start a query for the first survey date for the All of Us survey "The Basics" (sometimes, but rarely, participants completed this survey over multiple dates). -->
<!-- *While the ds_survey table is conveniently organized, it does not include the "skip" response for some surveys, so we don't recommend using it unless you're sure all potential responses have been recorded in the table. It's safest to use the observation table. You can see a detailed description of the survey ETL here: https://support.researchallofus.org/hc/en-us/articles/6085114880148.* -->
<!-- ```{r, eval = FALSE} -->
<!-- library(tidyverse) -->
<!-- survey_dates = tbl(con, "ds_survey") %>% -->
<!--   filter(survey == "The Basics") %>% -->
<!--   group_by(person_id) %>% -->
<!--   filter(survey_datetime == min(survey_datetime)) %>% -->
<!--   distinct(person_id, survey_datetime) -->
<!-- ``` -->
<!-- And this code would start with the all of us specific person table and join that to the survey dates table to incorporate information about demographics. -->
<!-- ```{r, eval = FALSE} -->
<!-- demo <- tbl(con, "cb_search_person") %>% -->
<!--   filter(age_at_consent >= 40 & age_at_consent <= 120, -->
<!--          has_ehr_data == 1) %>% -->
<!--   distinct(person_id, sex_at_birth, dob)  %>% -->
<!--   inner_join(survey_dates, by = "person_id") %>% -->
<!--   select(person_id, sex_at_birth, dob, survey_datetime) -->
<!-- ``` -->
<!-- We could leave this as a query, or we could pull the data into our local session: -->
<!-- ```{r, eval = FALSE} -->
<!-- demo_collected <- demo %>% collect() -->
<!-- ``` -->

## Bugs

Please leave us comments, requests, and report bugs using the “Issues”
tab on github located here:
<https://github.com/roux-ohdsi/allofus/issues>.
