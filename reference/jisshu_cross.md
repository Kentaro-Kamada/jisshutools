# Two Way Cross Table for Chosa Jisshu

調査実習用の2次元クロス表を作成する関数です。

## Usage

``` r
jisshu_cross(.data, .x, .y)
```

## Arguments

- .data:

  a `tibble` or `data.frame`

- .x:

  a column name in `.data`

- .y:

  a column name in `.data`

## Value

an object of class `wbWorkbook` exported from `openxlsx2` package. The
object can be easily saved as an excel file. See examples for details.

## Details

データと変数2つを指定するとクロス表が作成されます。

クラメールのVとカイ2乗検定のp値も算出されます。2×2のクロス表の場合はイェーツの連続修正が入ります。

エクセルへの保存はexampleを参照してください。

## Examples

``` r
# Create a sample data
data <-
  data.frame(
    x = c(rep('A', 50), rep('B', 50)),
    y = c(rep(1:5, 20))
  )

# Create a cross table
result <- data |> jisshu_cross(x, y)
#> [[1]]
#> # A tibble: 3 × 8
#>   x          `1`   `2`   `3`   `4`   `5`   合計  N      
#>   <chr>      <chr> <chr> <chr> <chr> <chr> <chr> <chr>  
#> 1 A（％）    20.0  20.0  20.0  20.0  20.0  100.0 （50） 
#> 2 B（％）    20.0  20.0  20.0  20.0  20.0  100.0 （50） 
#> 3 合計（％） 20.0  20.0  20.0  20.0  20.0  100.0 （100）
#> 
#> [[2]]
#> [1] "クラメールのV：0.000　　有意差なし　p>0.999"
#> 

# Save the cross table as an excel file
if (FALSE) { # \dontrun{
result$save('hoge.xlsx')
} # }
```
