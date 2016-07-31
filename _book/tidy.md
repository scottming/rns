
# (PART) Wrangles {-}

# 数据整理、转换（reshape、tidy）
## reshape2


```r
library(reshape2)
subdata <-iris[,4:5]
head(subdata)
#>   Petal.Width Species
#> 1         0.2  setosa
#> 2         0.2  setosa
#> 3         0.2  setosa
#> 4         0.2  setosa
#> 5         0.2  setosa
#> 6         0.4  setosa
data_w <- unstack(subdata)
colMeans(data_w)
#>     setosa versicolor  virginica 
#>      0.246      1.326      2.026
```

实践中这种单纯的长宽格式并不多见，更常见的是直接一步得到结果，所以就需要强大的 reshape2 包

参考[An Introduction to reshape2](http://seananderson.ca/2013/10/19/reshape.html)

加载包和改数据名

```r
library(reshape2)
names(airquality) <- tolower(names(airquality))
head(airquality)
#>   ozone solar.r wind temp month day
#> 1    41     190  7.4   67     5   1
#> 2    36     118  8.0   72     5   2
#> 3    12     149 12.6   74     5   3
#> 4    18     313 11.5   62     5   4
#> 5    NA      NA 14.3   56     5   5
#> 6    28      NA 14.9   66     5   6
```

### melt 熔化转长格式


```r
aql <- melt(airquality)
#> No id variables; using all as measure variables
tail(aql)
#>     variable value
#> 913      day    25
#> 914      day    26
#> 915      day    27
#> 916      day    28
#> 917      day    29
#> 918      day    30
head(aql)
#>   variable value
#> 1    ozone    41
#> 2    ozone    36
#> 3    ozone    12
#> 4    ozone    18
#> 5    ozone    NA
#> 6    ozone    28
```

设定不需要变 value 的变量

```r
aql <- melt(airquality,id.vars = c('month', 'day'))
names(airquality)
#> [1] "ozone"   "solar.r" "wind"    "temp"    "month"   "day"
head(aql)
#>   month day variable value
#> 1     5   1    ozone    41
#> 2     5   2    ozone    36
#> 3     5   3    ozone    12
#> 4     5   4    ozone    18
#> 5     5   5    ozone    NA
#> 6     5   6    ozone    28
```

改名

```r
aql <- melt(airquality, id.vars = c("month", "day"),
            variable.name = "climate_variable",
            value.name = "climate_value")
head(aql)
#>   month day climate_variable climate_value
#> 1     5   1            ozone            41
#> 2     5   2            ozone            36
#> 3     5   3            ozone            12
#> 4     5   4            ozone            18
#> 5     5   5            ozone            NA
#> 6     5   6            ozone            28
head(airquality)
#>   ozone solar.r wind temp month day
#> 1    41     190  7.4   67     5   1
#> 2    36     118  8.0   72     5   2
#> 3    12     149 12.6   74     5   3
#> 4    18     313 11.5   62     5   4
#> 5    NA      NA 14.3   56     5   5
#> 6    28      NA 14.9   66     5   6
```

### dcast 重铸，长转宽


```r
aql <- melt(airquality, id.vars = c("month", "day"))
aqw  <- dcast(aql, month + day ~ variable)
head(aql)
#>   month day variable value
#> 1     5   1    ozone    41
#> 2     5   2    ozone    36
#> 3     5   3    ozone    12
#> 4     5   4    ozone    18
#> 5     5   5    ozone    NA
#> 6     5   6    ozone    28
head(aqw)
#>   month day ozone solar.r wind temp
#> 1     5   1    41     190  7.4   67
#> 2     5   2    36     118  8.0   72
#> 3     5   3    12     149 12.6   74
#> 4     5   4    18     313 11.5   62
#> 5     5   5    NA      NA 14.3   56
#> 6     5   6    28      NA 14.9   66
```

不足的id.var 会出错，会把值的数量统计出来

```r
dcast(aql, month ~ variable)
#> Aggregation function missing: defaulting to length
#>   month ozone solar.r wind temp
#> 1     5    31      31   31   31
#> 2     6    30      30   30   30
#> 3     7    31      31   31   31
#> 4     8    31      31   31   31
#> 5     9    30      30   30   30
```

### fun,加上集合函数就能按正确方式集合了。


```r
dcast(aql, month ~ variable, fun.aggregate = mean,
      na.rm = TRUE)
#>   month ozone solar.r  wind temp
#> 1     5  23.6     181 11.62 65.5
#> 2     6  29.4     190 10.27 79.1
#> 3     7  59.1     216  8.94 83.9
#> 4     8  60.0     172  8.79 84.0
#> 5     9  31.4     167 10.18 76.9
```

## tidyr
新建一列数据

```r
mtcars$car <- rownames(mtcars)
colnames(mtcars)
#>  [1] "mpg"  "cyl"  "disp" "hp"   "drat" "wt"   "qsec" "vs"   "am"   "gear"
#> [11] "carb" "car"
mtcars <- mtcars[, c(12, 1:11)]
head(mtcars)
#>                                 car  mpg cyl disp  hp drat   wt qsec vs am
#> Mazda RX4                 Mazda RX4 21.0   6  160 110 3.90 2.62 16.5  0  1
#> Mazda RX4 Wag         Mazda RX4 Wag 21.0   6  160 110 3.90 2.88 17.0  0  1
#> Datsun 710               Datsun 710 22.8   4  108  93 3.85 2.32 18.6  1  1
#> Hornet 4 Drive       Hornet 4 Drive 21.4   6  258 110 3.08 3.21 19.4  1  0
#> Hornet Sportabout Hornet Sportabout 18.7   8  360 175 3.15 3.44 17.0  0  0
#> Valiant                     Valiant 18.1   6  225 105 2.76 3.46 20.2  1  0
#>                   gear carb
#> Mazda RX4            4    4
#> Mazda RX4 Wag        4    4
#> Datsun 710           4    1
#> Hornet 4 Drive       3    1
#> Hornet Sportabout    3    2
#> Valiant              3    1
```

### gather 聚合


```r
library(tidyr)
#> 
#> Attaching package: 'tidyr'
#> The following object is masked from 'package:reshape2':
#> 
#>     smiths
mtcarsNew <- mtcars %>%
  gather(attribute, value, -car) #不仅聚合，且命名了。
head(mtcarsNew)
#>                 car attribute value
#> 1         Mazda RX4       mpg  21.0
#> 2     Mazda RX4 Wag       mpg  21.0
#> 3        Datsun 710       mpg  22.8
#> 4    Hornet 4 Drive       mpg  21.4
#> 5 Hornet Sportabout       mpg  18.7
#> 6           Valiant       mpg  18.1
tail(mtcarsNew)
#>                car attribute value
#> 347  Porsche 914-2      carb     2
#> 348   Lotus Europa      carb     2
#> 349 Ford Pantera L      carb     4
#> 350   Ferrari Dino      carb     6
#> 351  Maserati Bora      carb     8
#> 352     Volvo 142E      carb     2
```

gather mpg:gear 之间的列

```r
mtcarsNew <- mtcars %>%
  gather(attribute, value, mpg:gear)
```

### spread 铺开


```r
mtcarsWide <- mtcarsNew %>%
  spread(attribute, value)
head(mtcarsWide)
#>                  car carb am cyl disp drat gear  hp  mpg qsec vs   wt
#> 1        AMC Javelin    2  0   8  304 3.15    3 150 15.2 17.3  0 3.44
#> 2 Cadillac Fleetwood    4  0   8  472 2.93    3 205 10.4 18.0  0 5.25
#> 3         Camaro Z28    4  0   8  350 3.73    3 245 13.3 15.4  0 3.84
#> 4  Chrysler Imperial    4  0   8  440 3.23    3 230 14.7 17.4  0 5.34
#> 5         Datsun 710    1  1   4  108 3.85    4  93 22.8 18.6  1 2.32
#> 6   Dodge Challenger    2  0   8  318 2.76    3 150 15.5 16.9  0 3.52
```

### unite 合并


```r
set.seed(1)
date <- as.Date('2016-06-01') + 0:14

hour <- sample(1:24, 15)
min <- sample(1:60, 15)
second <- sample(1:60, 15)
event <- sample(letters, 15)
df <- data.frame(date, hour, min, second, event)
head(df)
#>         date hour min second event
#> 1 2016-06-01    7  30     29     u
#> 2 2016-06-02    9  43     36     a
#> 3 2016-06-03   13  58     60     l
#> 4 2016-06-04   20  22     11     q
#> 5 2016-06-05    5  44     47     p
#> 6 2016-06-06   18  52     37     k

df1 <- df %>%
  unite(datehour, date, hour, sep = ' ') %>%
  unite(datetime, datehour, min, second, sep = ':')
df1
#>               datetime event
#> 1   2016-06-01 7:30:29     u
#> 2   2016-06-02 9:43:36     a
#> 3  2016-06-03 13:58:60     l
#> 4  2016-06-04 20:22:11     q
#> 5   2016-06-05 5:44:47     p
#> 6  2016-06-06 18:52:37     k
#> 7  2016-06-07 19:12:43     r
#> 8   2016-06-08 12:35:6     i
#> 9   2016-06-09 11:7:38     e
#> 10  2016-06-10 1:14:21     b
#> 11  2016-06-11 3:20:42     w
#> 12  2016-06-12 14:1:32     t
#> 13 2016-06-13 23:19:52     h
#> 14 2016-06-14 21:41:26     s
#> 15  2016-06-15 8:16:25     o
```

### separate 拆分


```r
df2 <- df1 %>%
  separate(datetime, c('date', 'time'), sep = ' ') %>%
  separate(time, c('hour', 'min', 'second'), sep = ':')
df2
#>          date hour min second event
#> 1  2016-06-01    7  30     29     u
#> 2  2016-06-02    9  43     36     a
#> 3  2016-06-03   13  58     60     l
#> 4  2016-06-04   20  22     11     q
#> 5  2016-06-05    5  44     47     p
#> 6  2016-06-06   18  52     37     k
#> 7  2016-06-07   19  12     43     r
#> 8  2016-06-08   12  35      6     i
#> 9  2016-06-09   11   7     38     e
#> 10 2016-06-10    1  14     21     b
#> 11 2016-06-11    3  20     42     w
#> 12 2016-06-12   14   1     32     t
#> 13 2016-06-13   23  19     52     h
#> 14 2016-06-14   21  41     26     s
#> 15 2016-06-15    8  16     25     o
```

总的来说，tidyr 包比reshape语法更为简洁易懂， 更加方便。
