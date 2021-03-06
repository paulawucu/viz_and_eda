ggplot 2
================

load in a dataset

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(  # download the dataset 
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), # pick three variabels that you care about
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%  # from 20170101 to 20171231
  mutate(
    name = recode(  # create a new name variable that recode the weather station
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2021-10-05 10:29:22 (7.602)

    ## file min/max dates: 1869-01-01 / 2021-10-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2021-10-05 10:29:25 (1.697)

    ## file min/max dates: 1965-01-01 / 2020-02-29

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2021-10-05 10:29:27 (0.912)

    ## file min/max dates: 1999-09-01 / 2021-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 × 6
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

Start making some plots # Scatterplot tmax vs. tmin in a scatterplot

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.3) + 
  labs(
    title = "Temperature at Three station",
    x = "Minimum daily temp (C)",
    y = "Maximum daily temp (C)",
    caption = "Data from moaa package with three stations"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> ##
Scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.3) + 
  labs(
    title = "Temperature at Three station",
    x = "Minimum daily temp (C)",
    y = "Maximum daily temp (C)",
    caption = "Data from moaa package with three stations"
  ) + 
  scale_x_continuous(  # continuous variable on the x axis
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15")
  ) + 
  scale_y_continuous(
    trans = "sqrt",  # sqrt transformation
    position = "right"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
Color scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.5) + 
  labs(
    title = "Temperature at Three station",
    x = "Minimum daily temp (C)",
    y = "Maximum daily temp (C)",
    caption = "Data from moaa package with three stations"
  ) + 
  #scale_color_hue(h = c(100, 300))
  scale_color_viridis_d()  # d for discrete variable 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- --> ##
Themes aren’t scale/ mapping related, but just to make plots look nicer

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.5) + 
  labs(
    title = "Temperature at Three station",
    x = "Minimum daily temp (C)",
    y = "Maximum daily temp (C)",
    caption = "Data from moaa package with three stations"
  ) + 
  scale_color_viridis_d() +
  theme_minimal() + # change background theme to bw/ classic/ minimal
  #ggthemes::theme_excel() + # graphs look like coming right from excel
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
you can also set global features, codes in www.p8105.com

## `data` in geoms

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")
waikiki %>% 
  ggplot(aes(x = date, y = tmax, color = name )) +
  geom_point() +
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## `patchwork`

``` r
ggp_tmax_tmin = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  theme(legend.position = "none")

ggp_prep_dens = 
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.3) +
  theme(legend.position = "none")  # get rid of the legends

ggp_tmax_date = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth() +
  theme(legend.position = "none")
(ggp_tmax_tmin + ggp_prep_dens) / ggp_tmax_date  # patchwork will stuff graphs together, "/" will put this underneath
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## data manipulation

quick example on factors

``` r
weather_df %>% 
  mutate(
    name = fct_reorder(name, tmax)  # put in order of tmax from low to high
  ) %>% 
  #mutate(name = forcats::fct_relevel(name, c("Waikiki_HA", "CentralPark_NY", "Waterhole_WA"))) %>% 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
  #geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
```

what about tmax and tmin? density plot

``` r
weather_df %>%
  select(name, tmax, tmin) %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation", 
    values_to = "temp") %>% 
  ggplot(aes(x = temp, fill = observation)) +
  geom_density(alpha = .5) + 
  facet_grid(~name) + 
  viridis::scale_fill_viridis(discrete = TRUE)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
pulse_df = 
  haven::read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi",
    names_prefix = "bdi_score_"
  ) %>% 
  mutate(visit = recode(visit, "bl" = "00m"))
pulse_df %>% 
  ggplot(aes(x = visit, y = bdi)) +
  #geom_boxplot()
  geom_point() +
  geom_line(aes(group = id))  # good for longitudinal data analysis
```

    ## Warning: Removed 879 rows containing missing values (geom_point).

    ## Warning: Removed 515 row(s) containing missing values (geom_path).

![](visualization_pt2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
