hw8
================
Abby Bergman
11/22/2018

Part 1: Exploring Population Density
====================================

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 180 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 180 rows containing missing values (geom_point).

![](hw_8_files/figure-markdown_github/unnamed-chunk-4-1.png) As shown above, Life Expectancy appears to increase with Population Density, when plotted on the log scale.

``` r
#regression model
density_model <- lm(lifeExp ~ popden, 
                   data = combined1)
summary(density_model)
```

    ## 
    ## Call:
    ## lm(formula = lifeExp ~ popden, data = combined1)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -36.75 -11.13   0.81  11.46  23.10 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 5.863e+01  3.443e-01 170.300  < 2e-16 ***
    ## popden      6.197e-03  8.713e-04   7.113 1.74e-12 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 12.87 on 1522 degrees of freedom
    ##   (180 observations deleted due to missingness)
    ## Multiple R-squared:  0.03217,    Adjusted R-squared:  0.03154 
    ## F-statistic: 50.59 on 1 and 1522 DF,  p-value: 1.744e-12

The above regression model shows that there is a relationship between Life Expectancy and Population Density.

part 2: Exploring Water Temperature on the Pacific Coast
========================================================

``` r
water <- read_html("https://www.nodc.noaa.gov/dsdt/cwtg/all_meanT.html")

water_south <-  html_nodes(water, css = ".reg:nth-child(21)") %>%
  html_table() %>%
  as.data.frame()


water_clean <- water_south %>%
  mutate(APR = (APR1.15 + APR16.30)/2) %>%
  mutate(MAY = (MAY1.15 + MAY16.31)/2) %>%
  mutate(JUN = (JUN1.15 + JUN16.30)/2) %>%
  mutate(JUL = (JUL1.15 + JUL16.31)/2) %>%
  mutate(AUG = (AUG1.15 + AUG16.31)/2) %>%
  mutate(SEP = (SEP1.15 + SEP16.30)/2) %>%
  mutate(OCT = (OCT1.15 + OCT16.31)/2) %>%
  select(Location, JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV, DEC)

water_month <- gather(water_clean, `JAN`, `FEB`, `MAR`, `APR`, `MAY`, `JUN`, `JUL`, `AUG`, `SEP`, `OCT`, `NOV`, `DEC` ,key = month, value = temp) 

water_month %>%
  ggplot(aes(month, temp)) +
  geom_point() + 
  labs(title = "Average Water Temp by Month, Southern CA", x = "Month", y = "Average Water Temp")
```

![](hw_8_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
water_season <- water_clean %>%
  mutate(Winter = (JAN +FEB +DEC)/3, 
         Spring = (MAR+APR+MAY)/3, 
         Summer = (JUN+JUL+AUG)/3, 
         Fall = (SEP+OCT+NOV)/3) %>%
  select(Location, Winter, Spring, Summer, Fall) %>%
  gather(`Winter`, `Spring`, `Summer`, `Fall`,key = season, value = temp)

water_season %>%
  ggplot(aes(season, temp)) +
  geom_boxplot() + 
  labs(title = "Boxplot of Avg Temperatures per Season", x = "Season", y = "Temperature")
```

![](hw_8_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
water_avg <- water_clean %>%
  mutate(Avg = (JAN + FEB + MAR + APR + MAY + JUN + JUL + AUG + SEP + OCT + NOV + DEC)/12) %>%
  select(Location, Avg)
```

``` r
temp_model <- lm(temp ~ season, 
                   data = water_season)
summary(temp_model)
```

    ## 
    ## Call:
    ## lm(formula = temp ~ season, data = water_season)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5.1053 -1.1250  0.0351  1.4101  5.8947 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   63.7982     0.4781 133.430  < 2e-16 ***
    ## seasonSpring  -4.9211     0.6762  -7.278 3.39e-10 ***
    ## seasonSummer   0.9737     0.6762   1.440    0.154    
    ## seasonWinter  -6.0088     0.6762  -8.886 3.40e-13 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.084 on 72 degrees of freedom
    ## Multiple R-squared:  0.6891, Adjusted R-squared:  0.6762 
    ## F-statistic:  53.2 on 3 and 72 DF,  p-value: < 2.2e-16
