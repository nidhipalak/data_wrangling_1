Tidy data
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## `pivot longer`

``` r
pulse = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

We do not want 4 diff columns for BDI score, but one column. We willend
up with some data duplication (but we’re not worried about that??)

Wide format to long format

``` r
pulse_tidy = 
  pulse %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  )
#when tidying data, the bdi scores will be their own column, they need their own name, which we've named here as "visit". Their values will no longer fall under their current columns and need to be renamed as well to "bdi"

#so, we also don't need the long ass name for the visit variable.  bdi_score_bl can just be "bl" or "baseline".  The `names_prefix` fn removes it.  NEXT time, fix bl to 0m for consistency (IN THE NEXT CODE CHUNK.
```

rewrite, combine and extend (to add mutate)

``` r
pulse_tidy = 
  pulse %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>% 
 relocate(id, visit) %>% 
  mutate(visit = recode(visit, "bl" = "oom"))
```

## `pivot_wider`

Make up some data\!

``` r
analysis = 
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )

#if you want ot put it in a paper, so someone can look at it.

analysis %>% 
  pivot_wider(
    names_from = "time",
    values_from = "mean"
  )
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

``` r
#pull the names from the columns.  The data in the columns become the new column names
```

## Binding rows

Using lord of the rings data. First step: import each table. (create
three sep data structures. I think we can only import certain rows.)

``` r
fellowship = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship")

twotowers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "twotowers")

returnking = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "returnking")

#mutate here adds the variable "movie" and the values are "fellowship", "twotowers", "returnking".
```

Bind all rows together

``` r
lotr_tidy = 
  bind_rows(fellowship, twotowers, returnking) %>% 
#we want to tidy more, no need for male and female columns, it can be under one "sex" and the values under "words"
  janitor::clean_names() %>% 
  relocate(movie) %>% 
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )

#make tidyer and make "Elf" "Hobbit" all lowercase
```