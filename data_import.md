Data import
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

## Read in some data

Absolute path: start from very beginning to desired folder (do not use
this method) Start from harddrive-\> user-\> nidhipatel-\> dekstop-\>
datasciencei-\> datawanglingi-\>data-\>file. This would only apply to my
computer. Relative path: start where you are and go where you need to
go, which is prob close by. (use this only) Start at data\_import.Rmd
-\> data folder -\> file. Can be reproduced in anyones computer

Readn in the litters dataset.

``` r
litters_df <- read_csv("./data/FAS_litters.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

``` r
# the "." says starting in this folder.
litters_df = janitor::clean_names(litters_df)
#clean_names fn is not in tidyverse, but in janitors package. To load this fn without loading the entire package we use "::" to pull it from the package. 
```

## Take a look at data
