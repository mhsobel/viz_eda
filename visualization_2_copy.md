    library(rnoaa)

    weather_df = 
      rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                          var = c("PRCP", "TMIN", "TMAX"), 
                          date_min = "2017-01-01",
                          date_max = "2017-12-31") %>%
      mutate(
        name = recode(id, USW00094728 = "CentralPark_NY", 
                          USC00519397 = "Waikiki_HA",
                          USS0023B17S = "Waterhole_WA"),
        tmin = tmin / 10,
        tmax = tmax / 10) %>%
      select(name, id, everything())
    weather_df

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp    tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl>   <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01    0.   8.90   4.40
    ##  2 CentralPark_NY USW00094728 2017-01-02   53.   5.00   2.80
    ##  3 CentralPark_NY USW00094728 2017-01-03  147.   6.10   3.90
    ##  4 CentralPark_NY USW00094728 2017-01-04    0.  11.1    1.10
    ##  5 CentralPark_NY USW00094728 2017-01-05    0.   1.10  -2.70
    ##  6 CentralPark_NY USW00094728 2017-01-06   13.   0.600 -3.80
    ##  7 CentralPark_NY USW00094728 2017-01-07   81.  -3.20  -6.60
    ##  8 CentralPark_NY USW00094728 2017-01-08    0.  -3.80  -8.80
    ##  9 CentralPark_NY USW00094728 2017-01-09    0.  -4.90  -9.90
    ## 10 CentralPark_NY USW00094728 2017-01-10    0.   7.80  -6.00
    ## # ... with 1,085 more rows

Start a plot
------------

First scatterplot

    ggplot(weather_df, aes(x = tmin, y = tmax)) + 
      geom_point(aes(color = name), alpha = .5) +
      labs(
        title = "Temperature plot",
        x = "Minimum daily temperature (C)",
        y = "Maxiumum daily temperature (C)",
        caption = "Data from the rnoaa package"
        )
    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-1-1.png" width="90%" />

Tick marks and labels …

    ggplot(weather_df, aes(x = tmin, y = tmax)) + 
      geom_point(aes(color = name), alpha = .5) +
      labs(
        title = "Temperature plot",
        x = "Minimum daily temperature (C)",
        y = "Maxiumum daily temperature (C)",
        caption = "Data from the rnoaa package"
        ) + 
      scale_x_continuous(breaks = c(-15, 0, 15), 
                         labels = c("-15ºC", "0", "15"),   # option + 0 = º
                         limits = c(-20, 30)) + 
      scale_y_continuous(trans = "sqrt", 
                         position = "right")
    ## Warning in self$trans$transform(x): NaNs produced
    ## Warning: Transformation introduced infinite values in continuous y-axis
    ## Warning: Removed 90 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-2-1.png" width="90%" />

Colors and themes
-----------------

Adjust color

    ggplot(weather_df, aes(x = tmin, y = tmax)) + 
      geom_point(aes(color = name), alpha = .5) + 
      labs(
        title = "Temperature plot",
        x = "Minimum daily temperature (C)",
        y = "Maxiumum daily temperature (C)",
        caption = "Data from the rnoaa package"
      ) + 
      scale_color_hue(name = "Location", 
                      h = c(100, 300),         # range of hues 
                      l = 50)    
    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-3-1.png" width="90%" />

Viridis package

    ggplot(weather_df, aes(x = tmin, y = tmax)) + 
      geom_point(aes(color = name), alpha = .5) + 
      labs(
        title = "Temperature plot",
        x = "Minimum daily temperature (C)",
        y = "Maxiumum daily temperature (C)",
        caption = "Data from the rnoaa package"
      ) + 
      viridis::scale_color_viridis(
        name = "Location", 
        discrete = TRUE   # makes discrete from continuous scale 
      ) + 
      theme(legend.position = "bottom")
    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-4-1.png" width="90%" />

Themes
------

    ggplot(weather_df, aes(x = tmin, y = tmax)) + 
      geom_point(aes(color = name), alpha = .5) + 
      labs(
        title = "Temperature plot",
        x = "Minimum daily temperature (C)",
        y = "Maxiumum daily temperature (C)",
        caption = "Data from the rnoaa package"
      ) + 
      viridis::scale_color_viridis(
        name = "Location", 
        discrete = TRUE   # makes discrete from continuous scale 
      ) + 
      theme_bw() +        # ORDER MATTERS! - set theme first, then modify!
      theme(legend.position = "bottom")
    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-5-1.png" width="90%" />

Practice!
---------

    ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
      geom_smooth(se = FALSE) + 
      geom_point(aes(size = prcp), alpha = .75) + 
      labs(
        title = "Temperature Plot", 
        x = "Date", 
        y = "Max Daily Temp (ºC)",
        caption = "Data from the rnoaa package"
      ) +
      viridis::scale_color_viridis(discrete = TRUE) +
      theme_minimal() + 
      theme(legend.position = "bottom")
    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'
    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).
    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-6-1.png" width="90%" />

Arguments to `geom_*`
---------------------

    centralpark_df = weather_df %>%  filter(name == "CentralPark_NY")
    waikiki_df = weather_df %>%  filter(name == "Waikiki_HA")

    ggplot(waikiki_df, aes(x = date, y = tmax, color = name)) +
      geom_point() + 
      geom_point(data = centralpark_df)
    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-7-1.png" width="90%" />

patchwork
---------

Separate the plots…

    ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
      geom_smooth(se = FALSE) + 
      facet_grid( ~ name) +
      geom_point(aes(size = prcp), alpha = .75) + 
      labs(
        title = "Temperature Plot", 
        x = "Date", 
        y = "Max Daily Temp (ºC)",
        caption = "Data from the rnoaa package"
      ) +
      viridis::scale_color_viridis(discrete = TRUE) +
      theme_minimal() + 
      theme(legend.position = "bottom")
    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'
    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).
    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-8-1.png" width="90%" />

…but sometime might want to make distinctly different plots!

    tmax_tmin_p = ggplot(weather_df, aes(x = tmax, y = tmin, color = name)) + 
      geom_point(alpha = .5) +
      theme(legend.position = "none")

    prcp_dens_p = weather_df %>% 
      filter(prcp > 0) %>% 
      ggplot(aes(x = prcp, fill = name)) + 
      geom_density(alpha = .5) + 
      theme(legend.position = "none")

    tmax_date_p = ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
      geom_point(alpha = .5) +
      geom_smooth(se = FALSE) + 
      theme(legend.position = "bottom")

    # stitching together fundamentally different plots
    (tmax_tmin_p + prcp_dens_p) / tmax_date_p
    ## Warning: Removed 15 rows containing missing values (geom_point).
    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'
    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).
    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-9-1.png" width="90%" />

Data manipulation
-----------------

factors…they show up in the order of that factor

    ggplot(weather_df, aes(x = date, y = tmax, fill = name)) + 
      geom_violin()
    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-10-1.png" width="90%" />

Ordering factors that explicitly choose, rather than the way it is

    # select the way you want the levels, "by hand"
    weather_df %>%
      mutate(name = forcats::fct_relevel(name, c("Waikiki_HA", "CentralPark_NY", "Waterhole_WA"))) %>% 
      ggplot(aes(x = name, y = tmax)) + 
      geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
      theme(legend.position = "bottom")
    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-11-1.png" width="90%" />


    # reorder name by tmax 
    weather_df %>%
      mutate(name = forcats::fct_reorder(name, tmax)) %>% 
      ggplot(aes(x = name, y = tmax)) + 
      geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
      theme(legend.position = "bottom")
    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-11-2.png" width="90%" />

Advanced tidying…
-----------------

-   tmax and tmin are different observations
-   focus only on vars needed
-   want to go wide –&gt; long

<!-- -->

    weather_df %>%
      select(name, tmax, tmin) %>% 
      gather(key = observation, value = temp, tmax:tmin) %>% 
      ggplot(aes(x = temp, fill = observation)) +
      geom_density(alpha = .5) + 
      facet_grid(~name) + 
      viridis::scale_fill_viridis(discrete = TRUE)
    ## Warning: Removed 18 rows containing non-finite values (stat_density).

<img src="visualization_2_copy_files/figure-markdown_strict/unnamed-chunk-12-1.png" width="90%" />
