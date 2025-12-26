# Regression Table for Chosa Jisshu

調査実習用の回帰分析結果を作成する関数です。

## Usage

``` r
jisshu_reg(object)

# S3 method for class 'glm'
jisshu_reg(object)

# S3 method for class 'lm'
jisshu_reg(object)

# S3 method for class 'multinom'
jisshu_reg(object)
```

## Arguments

- object:

  an object for which a summary is desired. For example, a model object,
  such as those returned by `lm`, `glm`, and `multinom`.

## Details

`lm`や`glm`、[`nnet::multinom`](https://rdrr.io/pkg/nnet/man/multinom.html)の結果から、執筆要項に沿ったエクセルの表が作成できます。

エクセルへの保存はexampleを参照してください。

## Examples

``` r
sample_data <- tibble::tibble(
  x1 = rnorm(100, mean = 0, sd = 1),
  x2 = rnorm(100, mean = 0, sd = 1),
  y = 0.2 + 0.3*x1 + 0.5*x2 + rnorm(100, mean = 0, sd = 0.1),
  y_bin = rbinom(100, 1, plogis(y)),
  y_multinom = cut(
    y,
    breaks = quantile(y, probs = c(0, 0.25, 0.75, 1)),
    labels = c('Q1', 'Q2', 'Q3'),
    include.lowest = TRUE
  )
)

sample_data
#> # A tibble: 100 × 5
#>          x1     x2       y y_bin y_multinom
#>       <dbl>  <dbl>   <dbl> <int> <fct>     
#>  1 -2.44    -1.06  -1.07       1 Q1        
#>  2 -0.00557 -0.796 -0.227      1 Q1        
#>  3  0.622   -1.76  -0.736      1 Q1        
#>  4  1.15    -0.691  0.206      1 Q2        
#>  5 -1.82    -0.559 -0.736      0 Q1        
#>  6 -0.247   -0.537 -0.206      1 Q1        
#>  7 -0.244    0.227  0.0339     1 Q2        
#>  8 -0.283    0.978  0.869      0 Q3        
#>  9 -0.554   -0.209 -0.186      1 Q2        
#> 10  0.629   -1.40  -0.345      1 Q1        
#> # ℹ 90 more rows

# Create a regression table
# Linear regression
model_lm <- lm(y ~ x1 + x2, data = sample_data)
result_lm <- jisshu_reg(model_lm)
#> # A tibble: 6 × 4
#>   独立変数               偏回帰係数 標準誤差 p.value
#>   <chr>                  <chr>      <chr>    <chr>  
#> 1 （定数）               0.190      0.010    ***    
#> 2 x1                     0.311      0.010    ***    
#> 3 x2                     0.525      0.010    ***    
#> 4 自由度調整済み決定係数 0.973      NA       NA     
#> 5 F値                    1794.833   ***      NA     
#> 6 N                      100        NA       NA     

# Logistic regression
model_glm <- glm(y_bin ~ x1 + x2, data = sample_data, family = binomial(link = 'logit'))
result_glm <- jisshu_reg(model_glm)
#> # A tibble: 6 × 4
#>   独立変数           偏回帰係数 標準誤差 p.value
#>   <chr>              <chr>      <chr>    <chr>  
#> 1 （定数）           0.056      0.207    ""     
#> 2 x1                 0.184      0.200    ""     
#> 3 x2                 0.423      0.209    "*"    
#> 4 Nagelkerke決定係数 0.065      NA        NA    
#> 5 モデルχ2乗値       5.010      +         NA    
#> 6 N                  100        NA        NA    

# Multinomial logistic regression
model_multinom <-
  nnet::multinom(y_multinom ~ x1 + x2, data = sample_data, model = TRUE, trace = FALSE)
result_multinom <- jisshu_reg(model_multinom)
#> # A tibble: 9 × 5
#>   y_multinom 独立変数           偏回帰係数 標準誤差 p.value
#>   <chr>      <chr>              <chr>      <chr>    <chr>  
#> 1 Q2         （定数）           26.479     19.040   ""     
#> 2 Q2         x1                 24.474     17.203   ""     
#> 3 Q2         x2                 38.787     28.327   ""     
#> 4 Q3         （定数）           14.289     19.564   ""     
#> 5 Q3         x1                 31.114     17.373   "+"    
#> 6 Q3         x2                 51.485     28.725   "+"    
#> 7 NA         Nagelkerke決定係数 0.964      NA        NA    
#> 8 NA         モデルχ2乗値       185.638    ***       NA    
#> 9 NA         N                  100        NA        NA    


# Glance the regression table
if (FALSE) { # \dontrun{
  result_lm$open()
  result_glm$open()
  result_multinom$open()
} # }

# Save the regression table as an excel file
if (FALSE) { # \dontrun{
  result_lm$save('hoge_lm.xlsx')
  result_glm$save('hoge_glm.xlsx')
  result_multinom$save('hoge_multinom.xlsx')
} # }
```
