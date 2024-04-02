Double Machine Learning
================
Veronica Bergstrom
Last updated: 02 April, 2024

- [View Data Set](#view-data-set)
- [Pre-process](#pre-process)
- [Perform Double Machine Learning
  Method](#perform-double-machine-learning-method)
- [Compare the Results to a Regular Statistical
  Model](#compare-the-results-to-a-regular-statistical-model)

Here, I will go through a double machine learning flow that allows us to
make causal inferences on observational data. Because this code was
written during my employment at Dacture, I have kept it hidden for
privacy reasons.

## View Data Set

The data set is comprised of 15 variables. We are interested in
estimating the causal effect of the X variable, intervention, on the Y
variable, outcome. The remaining 13 variables labelled V1 to V13 are
variables that may be confounders (i.e., influence both X and Y),
predictive of only the Y variable, or predictive of only the X variable.
For this example we are going to assume that none of the variables are
colliders (i.e., are influenced by both X and Y).

Names of the columns:

``` r
names(df.original)
```

    ##  [1] "outcome"      "intervention" "V1"           "V2"           "V3"          
    ##  [6] "V4"           "V5"           "V6"           "V7"           "V8"          
    ## [11] "V9"           "V10"          "V11"          "V12"          "V13"

Structure of the data set:

``` r
str(df.original)
```

    ## 'data.frame':    100 obs. of  15 variables:
    ##  $ outcome     : num  17.13 22.34 15.78 10.9 8.86 ...
    ##  $ intervention: Factor w/ 2 levels "no","yes": 2 2 2 1 2 2 1 2 1 1 ...
    ##  $ V1          : num  87.5 69.6 84.7 39.3 63.6 ...
    ##  $ V2          : int  0 1 1 0 1 0 0 1 0 0 ...
    ##  $ V3          : int  1 1 1 0 0 1 0 1 1 0 ...
    ##  $ V4          : num  19.8 48.5 35.7 49.5 61.7 ...
    ##  $ V5          : num  55.32 58.05 27.5 53.52 8.08 ...
    ##  $ V6          : num  64.7 55.9 75.8 51.8 43.7 ...
    ##  $ V7          : num  68.3 82.3 38.6 40.3 34.3 ...
    ##  $ V8          : num  18.3 44.9 46.4 60.5 40.9 ...
    ##  $ V9          : num  71 76.9 49.3 68.9 38.9 ...
    ##  $ V10         : num  87.7 48.2 77.4 94.6 43.7 ...
    ##  $ V11         : num  31.6 42.3 65.4 35.4 61.8 ...
    ##  $ V12         : num  12 60.9 51.5 52.6 76.3 ...
    ##  $ V13         : num  44.6 48.9 64.3 66.5 53.5 ...

First six columns of the data set:

``` r
head(df.original)
```

    ##     outcome intervention       V1 V2 V3       V4        V5       V6       V7
    ## 1 17.130259          yes 87.51224  0  1 19.75220 55.321678 64.73782 68.26276
    ## 2 22.336067          yes 69.63501  1  1 48.52690 58.045970 55.90006 82.29055
    ## 3 15.778027          yes 84.70349  1  1 35.66178 27.497938 75.84955 38.61159
    ## 4 10.898374           no 39.26840  0  0 49.47128 53.523750 51.81580 40.27504
    ## 5  8.859082          yes 63.62076  1  0 61.68666  8.076428 43.68241 34.29097
    ## 6 11.652655          yes 64.09558  0  1 43.67835 46.733207 53.12295 44.30621
    ##         V8       V9      V10      V11      V12      V13
    ## 1 18.34615 71.02733 87.68949 31.61477 12.01171 44.55356
    ## 2 44.93052 76.86681 48.20543 42.30602 60.93859 48.92759
    ## 3 46.39266 49.26585 77.37905 65.38438 51.54288 64.30883
    ## 4 60.51776 68.93215 94.59274 35.37877 52.64447 66.53231
    ## 5 40.94481 38.94757 43.70891 61.84578 76.31593 53.47107
    ## 6 15.18759 25.08263 36.97394 62.64310 69.67701 53.42647

Descriptives of the data set:

``` r
df.original |> psych::describe()
```

    ##               vars   n  mean    sd median trimmed   mad min max range  skew
    ## outcome          1 100 13.56  5.82  13.38   13.49  5.53   0  30    30  0.19
    ## intervention*    2 100  1.50  0.50   1.50    1.50  0.74   1   2     1  0.00
    ## V1               3 100 65.70 24.49  63.12   65.55 27.10   5 125   120  0.05
    ## V2               4 100  0.47  0.50   0.00    0.46  0.00   0   1     1  0.12
    ## V3               5 100  0.50  0.50   0.50    0.50  0.74   0   1     1  0.00
    ## V4               6 100 46.64 17.93  47.43   46.40 15.70   0 100   100  0.14
    ## V5               7 100 46.80 21.59  47.71   46.96 20.91   0 100   100 -0.03
    ## V6               8 100 51.32 20.19  51.77   51.49 20.43   0 100   100 -0.03
    ## V7               9 100 43.57 18.95  42.89   43.74 17.91   0 100   100  0.04
    ## V8              10 100 43.07 22.89  44.63   42.81 23.03   0 100   100  0.11
    ## V9              11 100 58.14 16.42  59.65   58.24 14.64   0 100   100 -0.22
    ## V10             12 100 50.42 23.66  48.65   50.50 23.20   0 100   100  0.01
    ## V11             13 100 48.55 18.40  49.53   48.67 18.91   0 100   100 -0.05
    ## V12             14 100 48.17 21.79  50.56   48.61 21.08   0 100   100 -0.14
    ## V13             15 100 56.46 18.38  56.96   56.95 17.55   0 100   100 -0.34
    ##               kurtosis   se
    ## outcome           0.07 0.58
    ## intervention*    -2.02 0.05
    ## V1               -0.45 2.45
    ## V2               -2.01 0.05
    ## V3               -2.02 0.05
    ## V4                0.10 1.79
    ## V5               -0.34 2.16
    ## V6               -0.21 2.02
    ## V7                0.04 1.90
    ## V8               -0.45 2.29
    ## V9                0.82 1.64
    ## V10              -0.55 2.37
    ## V11              -0.23 1.84
    ## V12              -0.46 2.18
    ## V13               0.42 1.84

    ## [1] "intervention_no"
    ## [1] "intervention_yes"

## Pre-process

After performing data cleaning measures, we will now pre-process the
continuous variables by normalizing the variables. In this example, we
have no missing data. If there were missing data, then we could use
knnImpute.

View the first six rows of the pre-processed data:

``` r
head(pp_df)
```

    ##   V2 V3 outcome      V1     V4       V5      V6     V7      V8     V9     V10
    ## 1  0  1   17.13  0.8904 -1.499  0.39478  0.6643  1.303 -1.0801  0.785  1.5753
    ## 2  1  1   22.34  0.1605  0.105  0.52098  0.2267  2.043  0.0815  1.140 -0.0935
    ## 3  1  1   15.78  0.7757 -0.612 -0.89407  1.2145 -0.261  0.1453 -0.540  1.1395
    ## 4  0  0   10.90 -1.0794  0.158  0.31150  0.0244 -0.174  0.7625  0.657  1.8671
    ## 5  1  0    8.86 -0.0851  0.839 -1.79371 -0.3784 -0.489 -0.0927 -1.169 -0.2836
    ## 6  0  1   11.65 -0.0657 -0.165 -0.00305  0.0891  0.039 -1.2181 -2.013 -0.5682
    ##      V11    V12    V13 intervention_yes
    ## 1 -0.921 -1.659 -0.648                1
    ## 2 -0.340  0.586 -0.410                1
    ## 3  0.915  0.155  0.427                1
    ## 4 -0.716  0.205  0.548                0
    ## 5  0.722  1.292 -0.162                1
    ## 6  0.766  0.987 -0.165                1

## Perform Double Machine Learning Method

It’s now time to build our model. Here are the steps:

1.  Regress the outcome variable on the covariates. In this example, we
    will implement a lasso regression (however, you may other methods
    like glmnet or ranger).
2.  Regress the intervention variable on the covariates. In this
    example, we will implement a lasso regression (however, you may
    other methods like glmnet or ranger).
3.  Save the residuals from both models.
4.  Regress the residuals from the outcome model on the residuals of the
    intervention variable.

<!-- -->

    ## 
    ## Call:
    ## lm(formula = target_residuals ~ treatment_residuals, data = residuals_df)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -4.390 -0.969 -0.087  1.038  5.380 
    ## 
    ## Coefficients:
    ##                      Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)         -6.74e-12   1.57e-01    0.00        1    
    ## treatment_residuals  2.78e+00   4.90e-01    5.67  1.5e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.57 on 98 degrees of freedom
    ## Multiple R-squared:  0.247,  Adjusted R-squared:  0.239 
    ## F-statistic: 32.1 on 1 and 98 DF,  p-value: 1.45e-07

You can see that the average treatment effect 2.78, meaning that the
intervention resulted in a 2.78 increase in the outcome. Additionally,
you can see that this effect is statistically significant.

## Compare the Results to a Regular Statistical Model

Here, we are simply regressing the outcome variable on the intervention
variable using a linear regression.

    ## 
    ## Call:
    ## lm(formula = stat.formula, data = pp_df)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -14.745  -2.566   0.114   2.860  12.986 
    ## 
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)        10.103      0.664   15.22  < 2e-16 ***
    ## intervention_yes    6.911      0.939    7.36  5.7e-11 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 4.69 on 98 degrees of freedom
    ## Multiple R-squared:  0.356,  Adjusted R-squared:  0.35 
    ## F-statistic: 54.2 on 1 and 98 DF,  p-value: 5.68e-11

You can see that in this case, the effect is 6.91 and statistically
significant. In other words, using a traditional linear regression
compared with a double machine learning method results in a highly
inflated estimate of the causal average treatment effect. This
highlights the importance of using double machine learning methods if
you are wanting to make causal inferences with observational data.
