# Simple Tabulation for Chosa Jisshu

調査実習用の単純集計表を作成する関数です。

## Usage

``` r
jisshu_simple_tabulation(
  data,
  path,
  grade,
  gender,
  include = everything(),
  cont_var = NULL
)
```

## Arguments

- data:

  a `tibble` or `data.frame`

- path:

  a file path to save the excel file

- grade:

  specify the grade (i.e. school year) variable. It must be a factor
  variable with the levels '1年', '2年', '3年'.

- gender:

  specify the gender variable. It must be a factor variable with the
  levels '男', '女'.

- include:

  variables to include in the table. Specify the variables in
  tidy-select style like
  [`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html).
  Default is
  [`everything()`](https://tidyselect.r-lib.org/reference/everything.html),
  e.g. all variables.

- cont_var:

  continuous variables to compute mean instead of frequency. Specify the
  variables in tidy-select style like
  [`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html).
  Default is `NULL`.

## Examples

``` r
library(dplyr)
#> 
#> Attaching package: ‘dplyr’
#> The following objects are masked from ‘package:stats’:
#> 
#>     filter, lag
#> The following objects are masked from ‘package:base’:
#> 
#>     intersect, setdiff, setequal, union
library(haven)
data <-
  tibble(
    ID = 1:100,
    grade =
      sample(1:3, 100, replace = TRUE, prob = c(0.3, 0.3, 0.4)) |>
      labelled_spss(labels = c('1年' = 1, '2年' = 2, '3年' = 3), label = '学年'),
    gender =
      sample(c(1, 2, 9), 100, replace = TRUE, prob = c(0.45, 0.45, 0.1)) |>
      labelled_spss(labels = c('男' = 1, '女' = 2, '無回答' = 9), label = '性別'),
    Q1 = rnorm(100, mean = 0, sd = 1),
    Q1_na = sample(c('無回答', '非該当', '有効'), 100, replace = TRUE, prob = c(0.1, 0.1, 0.8)),
    Q2 =
      sample(c(1:4, 8, 9), 100, replace = TRUE, prob = c(0.2, 0.3, 0.3, 0.1, 0.1, 0.1)) |>
      labelled_spss(
        labels = c(
          'あてはまる' = 1,
          'まああてはまる' = 2,
          'あまりあてはまらない' = 3,
          'まったくあてはまらない' = 4,
          '無回答' = 9,
          '非該当' = 8
        ),
        label = 'あなたは猫好きである'
      ),
  ) |>
  mutate(
    Q1 = case_when(
      Q1_na == '無回答' ~ 9,
      Q1_na == '非該当' ~ 8,
      .default = Q1
    ) |>
      labelled_spss(labels = c('非該当' = 8, '無回答' = 9), label = 'あなたの猫度を答えてください')
  ) |>
  select(!Q1_na)

data
#> # A tibble: 100 × 5
#>       ID grade     gender     Q1          Q2                        
#>    <int> <int+lbl> <dbl+lbl>  <dbl+lbl>   <dbl+lbl>                 
#>  1     1 1 [1年]   1 [男]     -1.58       4 [まったくあてはまらない]
#>  2     2 3 [3年]   2 [女]     -0.0841     2 [まああてはまる]        
#>  3     3 1 [1年]   2 [女]     -2.09       3 [あまりあてはまらない]  
#>  4     4 2 [2年]   2 [女]      0.00357    9 [無回答]                
#>  5     5 1 [1年]   2 [女]     -0.356      2 [まああてはまる]        
#>  6     6 2 [2年]   2 [女]      1.15       2 [まああてはまる]        
#>  7     7 3 [3年]   1 [男]     -0.221      2 [まああてはまる]        
#>  8     8 2 [2年]   9 [無回答]  1.02       1 [あてはまる]            
#>  9     9 1 [1年]   9 [無回答] -0.264      1 [あてはまる]            
#> 10    10 3 [3年]   2 [女]      9 [無回答] 9 [無回答]                
#> # ℹ 90 more rows

count(data, grade)
#> # A tibble: 3 × 2
#>   grade         n
#>   <int+lbl> <int>
#> 1 1 [1年]      28
#> 2 2 [2年]      27
#> 3 3 [3年]      45
count(data, gender)
#> # A tibble: 3 × 2
#>   gender         n
#>   <dbl+lbl>  <int>
#> 1 1 [男]        41
#> 2 2 [女]        49
#> 3 9 [無回答]    10
count(data, Q1, sort = TRUE)
#> # A tibble: 85 × 2
#>    Q1              n
#>    <dbl+lbl>   <int>
#>  1  9 [無回答]    12
#>  2  8 [非該当]     5
#>  3 -2.09           1
#>  4 -2.06           1
#>  5 -1.58           1
#>  6 -1.41           1
#>  7 -1.38           1
#>  8 -1.33           1
#>  9 -1.26           1
#> 10 -1.20           1
#> # ℹ 75 more rows
count(data, Q2)
#> # A tibble: 6 × 2
#>   Q2                             n
#>   <dbl+lbl>                  <int>
#> 1 1 [あてはまる]                17
#> 2 2 [まああてはまる]            33
#> 3 3 [あまりあてはまらない]      23
#> 4 4 [まったくあてはまらない]     5
#> 5 8 [非該当]                     9
#> 6 9 [無回答]                    13

if (FALSE) { # \dontrun{
jisshu_simple_tabulation(
  data |> as_factor(),
  'neko.xlsx',
  grade = grade,
  gender = gender,
  include = !c(ID),
  cont_var = c(Q1)
)
} # }
```
