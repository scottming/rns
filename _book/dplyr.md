
# (PART) Explore {-}
# dplyr

## 导入数据

```r
# 用 readr 包把数据读取成 tbl 格式，省去转化的那一步。
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
library(readr)
order <- read_csv('~/Nutstore/R/dplyr/dplyr-data/order.csv')[-1] # 去除空名的第一列
#> Warning: Missing column names filled in: 'X1' [1]
#> Parsed with column specification:
#> cols(
#>   X1 = col_integer(),
#>   orderid = col_integer(),
#>   customerid = col_integer(),
#>   campaignid = col_integer(),
#>   orderdate = col_date(format = ""),
#>   city = col_character(),
#>   state = col_character(),
#>   zipcode = col_character(),
#>   paymenttype = col_character(),
#>   totalprice = col_double(),
#>   numorderlines = col_integer(),
#>   numunits = col_integer()
#> )
order
#> # A tibble: 100,000 x 11
#>   orderid customerid campaignid  orderdate         city state zipcode
#>     <int>      <int>      <int>     <date>        <chr> <chr>   <chr>
#> 1 1002854      45978       2141 2009-10-13       NEWTON    MA   02459
#> 2 1002855     125381       2173 2009-10-13 NEW ROCHELLE    NY   10804
#> 3 1002856     103122       2141 2011-06-02        MIAMI    FL   33137
#> 4 1002857     130980       2173 2009-10-14 E RUTHERFORD    NJ   07073
#> 5 1002886      48553       2141 2010-11-19    BALTIMORE    MD   21218
#> 6 1002887     106150       2173 2009-10-15     ROWAYTON    CT   06853
#> # ... with 9.999e+04 more rows, and 4 more variables: paymenttype <chr>,
#> #   totalprice <dbl>, numorderlines <int>, numunits <int>
```

## 数据筛选 filter，返回满足条件的观测值

```r
# 看 2009-10-15 日的数据
filter(order, orderdate == '2009-10-13')
#> # A tibble: 19 x 11
#>   orderid customerid campaignid  orderdate         city state zipcode
#>     <int>      <int>      <int>     <date>        <chr> <chr>   <chr>
#> 1 1002854      45978       2141 2009-10-13       NEWTON    MA   02459
#> 2 1002855     125381       2173 2009-10-13 NEW ROCHELLE    NY   10804
#> 3 1002442     112970       2173 2009-10-13     BRANFORD    CT   06405
#> 4 1002471     125377       2173 2009-10-13 VALLEY FORGE    PA   19482
#> 5 1002472     133164       2173 2009-10-13   FORT WORTH    TX   76107
#> 6 1003224     129708       2173 2009-10-13  CERRO GORDO    NC   28430
#> # ... with 13 more rows, and 4 more variables: paymenttype <chr>,
#> #   totalprice <dbl>, numorderlines <int>, numunits <int>

# 更细的筛选
filter(order, orderdate == '2009-10-13' & totalprice > 100) -> new_tbl
new_tbl
#> # A tibble: 1 x 11
#>   orderid customerid campaignid  orderdate   city state zipcode
#>     <int>      <int>      <int>     <date>  <chr> <chr>   <chr>
#> 1 1002854      45978       2141 2009-10-13 NEWTON    MA   02459
#> # ... with 4 more variables: paymenttype <chr>, totalprice <dbl>,
#> #   numorderlines <int>, numunits <int>
```

## 子集选取 select，只保留选择的变量

```r
# 选取几个子集
names(order)
#>  [1] "orderid"       "customerid"    "campaignid"    "orderdate"    
#>  [5] "city"          "state"         "zipcode"       "paymenttype"  
#>  [9] "totalprice"    "numorderlines" "numunits"
select(order, city, numunits, zipcode)
#> # A tibble: 100,000 x 3
#>           city numunits zipcode
#>          <chr>    <int>   <chr>
#> 1       NEWTON        3   02459
#> 2 NEW ROCHELLE        1   10804
#> 3        MIAMI        2   33137
#> 4 E RUTHERFORD        1   07073
#> 5    BALTIMORE        1   21218
#> 6     ROWAYTON        1   06853
#> # ... with 9.999e+04 more rows

# 换名, 提取选择的数据列
select(order, date = orderdate, price = totalprice)
#> # A tibble: 100,000 x 2
#>         date price
#>       <date> <dbl>
#> 1 2009-10-13 190.0
#> 2 2009-10-13  10.0
#> 3 2011-06-02  35.2
#> 4 2009-10-14  10.0
#> 5 2010-11-19  10.0
#> 6 2009-10-15  10.0
#> # ... with 9.999e+04 more rows
names(order)
#>  [1] "orderid"       "customerid"    "campaignid"    "orderdate"    
#>  [5] "city"          "state"         "zipcode"       "paymenttype"  
#>  [9] "totalprice"    "numorderlines" "numunits"

# rename,换名，提取所有列
rename(order, date = orderdate, price = totalprice)
#> # A tibble: 100,000 x 11
#>   orderid customerid campaignid       date         city state zipcode
#>     <int>      <int>      <int>     <date>        <chr> <chr>   <chr>
#> 1 1002854      45978       2141 2009-10-13       NEWTON    MA   02459
#> 2 1002855     125381       2173 2009-10-13 NEW ROCHELLE    NY   10804
#> 3 1002856     103122       2141 2011-06-02        MIAMI    FL   33137
#> 4 1002857     130980       2173 2009-10-14 E RUTHERFORD    NJ   07073
#> 5 1002886      48553       2141 2010-11-19    BALTIMORE    MD   21218
#> 6 1002887     106150       2173 2009-10-15     ROWAYTON    CT   06853
#> # ... with 9.999e+04 more rows, and 4 more variables: paymenttype <chr>,
#> #   price <dbl>, numorderlines <int>, numunits <int>
names(order)
#>  [1] "orderid"       "customerid"    "campaignid"    "orderdate"    
#>  [5] "city"          "state"         "zipcode"       "paymenttype"  
#>  [9] "totalprice"    "numorderlines" "numunits"

# 选取以 order 开始的变量
select(order, starts_with('order'))
#> # A tibble: 100,000 x 2
#>   orderid  orderdate
#>     <int>     <date>
#> 1 1002854 2009-10-13
#> 2 1002855 2009-10-13
#> 3 1002856 2011-06-02
#> 4 1002857 2009-10-14
#> 5 1002886 2010-11-19
#> 6 1002887 2009-10-15
#> # ... with 9.999e+04 more rows

# 选取包含id 的变量
select(order, contains('id'))
#> # A tibble: 100,000 x 3
#>   orderid customerid campaignid
#>     <int>      <int>      <int>
#> 1 1002854      45978       2141
#> 2 1002855     125381       2173
#> 3 1002856     103122       2141
#> 4 1002857     130980       2173
#> 5 1002886      48553       2141
#> 6 1002887     106150       2173
#> # ... with 9.999e+04 more rows
```

## 数据排序 arrange 

```r
tbl <- select(order, date = orderdate, price = totalprice)
arrange(tbl, date, desc(price))
#> # A tibble: 100,000 x 2
#>         date price
#>       <date> <dbl>
#> 1 2009-10-04   200
#> 2 2009-10-04   120
#> 3 2009-10-04   100
#> 4 2009-10-04   100
#> 5 2009-10-04    70
#> 6 2009-10-04    50
#> # ... with 9.999e+04 more rows

# 管道操作，更简便
tbl <- select(order, date = orderdate, price = totalprice) %>%
  arrange(date, desc(price))
tbl
#> # A tibble: 100,000 x 2
#>         date price
#>       <date> <dbl>
#> 1 2009-10-04   200
#> 2 2009-10-04   120
#> 3 2009-10-04   100
#> 4 2009-10-04   100
#> 5 2009-10-04    70
#> 6 2009-10-04    50
#> # ... with 9.999e+04 more rows
```

## 数据扩展 mutate

```r
tbl <- select(order, date = orderdate, price = totalprice) %>%
  arrange(date,desc(price)) %>%
  mutate(year = substr(date,1,4), month = substr(date,6,7), 
       day = substr(date,9,10))
tbl
#> # A tibble: 100,000 x 5
#>         date price  year month   day
#>       <date> <dbl> <chr> <chr> <chr>
#> 1 2009-10-04   200  2009    10    04
#> 2 2009-10-04   120  2009    10    04
#> 3 2009-10-04   100  2009    10    04
#> 4 2009-10-04   100  2009    10    04
#> 5 2009-10-04    70  2009    10    04
#> 6 2009-10-04    50  2009    10    04
#> # ... with 9.999e+04 more rows
# transmute 则会删除原有的变量，如同 select 与 rename
```

## 数据汇总 summarise

```r
summarise(tbl, max = max(price),
          min = min(price),
          mean(price))
#> # A tibble: 1 x 3
#>     max   min mean(price)
#>   <dbl> <dbl>       <dbl>
#> 1  6780     0        60.8

summarise(tbl, fisrt = first(date),
          last = last(date))
#> # A tibble: 1 x 2
#>        fisrt       last
#>       <date>     <date>
#> 1 2009-10-04 2014-05-29

# 管道操作
df <- select(order, date = orderdate, price = totalprice) %>%
  summarise(price_sum = sum(price), price_mean = mean(price))
df
#> # A tibble: 1 x 2
#>   price_sum price_mean
#>       <dbl>      <dbl>
#> 1   6077773       60.8
```
  
## 数据连接 join

- left_join 以左边为主，且保留所有值，用的最多，b 表追加在后面
- inner_join 只保留匹配的
- semi_join 只匹配左边的列
- anti_join 与上面函数相反

## 分组汇总 group_by 

```r
tbl <- select(order, date = orderdate, price = totalprice) %>%
  mutate(year = substr(date,1,4), month = substr(date,6,7), 
         day = substr(date,9,10))

by_year <- group_by(tbl, year) %>%
  summarise(mean(price), sum(price), max(month))

by_year
#> # A tibble: 6 x 4
#>    year mean(price) sum(price) max(month)
#>   <chr>       <dbl>      <dbl>      <chr>
#> 1  2009        34.1     262628         12
#> 2  2010        52.2     967429         12
#> 3  2011        51.4    1380637         12
#> 4  2012        68.4    1404113         12
#> 5  2013        76.7    1633005         12
#> 6  2014        84.5     429962         05

order_year <- group_by(tbl, year)
```
