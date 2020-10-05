Visualizations
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

``` r
library(ggridges)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors( 
    c("USW00094728", "USC00519397", "USS0023B17S"), 
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
#rnoaa package allows you to pull public data from online. 
#want data drom these three weather stations
    mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  #the degrees that come from rnoaa are in a 10ths of a deg C. 
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: /Users/madhusudhanpatel/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2020-10-05 13:38:35 (7.522)

    ## file min/max dates: 1869-01-01 / 2020-10-31

    ## using cached file: /Users/madhusudhanpatel/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2020-10-05 13:38:42 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: /Users/madhusudhanpatel/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2020-10-05 13:38:44 (0.88)

    ## file min/max dates: 1999-09-01 / 2020-10-31

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## Scatterplots\!\!

Create my first scatterplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

New approach, same plot

``` r
weather_df %>% #allows to add mutate, select, etc
ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Save and edit a plot object

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Advanced scatterplot

Start with same one and make it fancy\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point() + 
  geom_smooth(se = FALSE) #adds nice smooth curves that run through data set (i do want to know how they are calculated tho)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

What about the `aes` placement

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name)) +
  geom_smooth(se = FALSE) #we only see one blue line, not separated by colors.  this color only applies to the scatterplot, bc the asthetic is added to point. define asthetics in ggplot and they get passed on to everything below.
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Let’s facet some things\!\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + #if i add alpha = tmin in the aes here, it would gradient for the min levels to be more transparent
  geom_point(alpha = .02, size = 3) + #alpha is transparency, size is size of points; can be used in geom_smooth too. 
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name) # . says don't create (rows in this case), ~ separates (x ~ y), y creates different columns according to names. 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Let’s combine some elements and try a new plot.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = 0.5) + 
  geom_smooth(se = FALSE) +
  facet_grid(name ~ .)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
#make a v simple scatterplot first and tweak as time goes on. 
```

## Small notes

How many geoms have to exist?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

do not need the points, I can have any geoms I want

I can use neat geom

``` r
 weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  #geom_hex() #geom_hex won't work for me.
  #geom_bin2d() same as hex, but with squares.
  geom_density2d() +
  geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density2d).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Univariate plots

Histograms are really great.

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Can we add color

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + #used fill instead of color
  geom_histogram(position = "dodge") + #to dodge instead of stack the histograms.
  facet_grid(.~name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Let’s try a new geometry

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_density(alpha = 0.3, adjust = 0.5)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

What about box plots?

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin)) + 
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

Trendy plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_violin(alpha = 0.4) + #cross between boxplot and density plot.
  stat_summary()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 15 rows containing non-finite values (stat_summary).

    ## No summary function supplied, defaulting to `mean_se()`

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Ridge plot- most popular plot od 2017

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) + 
  geom_density_ridges() #density curves. load from ggridges package. 
```

    ## Picking joint bandwidth of 1.67

    ## Warning: Removed 15 rows containing non-finite values (stat_density_ridges).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## Save and Embed

Let’s save a scatterplot

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, fill = name)) + 
  geom_point(alpha = 0.5)

ggsave("./weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

What about embedding…

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

Embed at different size

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_ppglot1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->
