
# 向量化操作（apply）

- 参考1：[掌握R语言中的apply函数族](http://blog.fens.me/r-apply/)
- 参考2：[R Grouping functions: sapply vs. lapply vs. apply. vs. tapply vs. by vs. aggregate](http://stackoverflow.com/questions/3505701/r-grouping-functions-sapply-vs-lapply-vs-apply-vs-tapply-vs-by-vs-aggrega)
- 参考3：[R语言apply家族函数的用法及其比较](http://www.xueqing.tv/cms/article/158)

## 循环迭代之 lapply/sapply

**sapply，lapply 的简化版**

第一参数是 X，官方解释说，可接收向量，list，或表达式对象，其他对象将被强制按 list，第二个参数则是函数，返回一个向量或矩阵。


```r
func <- function(x){
  if (x %% 2 == 0){
    ret <- 'even'
  }
  else {
    ret <- 'odd'
  }
  return(ret)
}
vec <- round(runif(4)*100)
func(vec)
#> Warning in if (x%%2 == 0) {: the condition has length > 1 and only the
#> first element will be used
#> [1] "even"
vec
#> [1]  8 83 60 16
sapply(vec, func)
#> [1] "even" "odd"  "even" "even"
```

**lapply**

跟 sapply 的区别在于返回的是列表


```r
lapply(vec, func)
#> [[1]]
#> [1] "even"
#> 
#> [[2]]
#> [1] "odd"
#> 
#> [[3]]
#> [1] "even"
#> 
#> [[4]]
#> [1] "even"
mylist <- as.list(iris[, 1:4]) # 不转list，也是强按
yourdata <- iris[, 1:4]

sapply(mylist, mean) # 还可以对列表进行计算
#> Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
#>         5.84         3.06         3.76         1.20
lapply(mylist, mean) # return a list
#> $Sepal.Length
#> [1] 5.84
#> 
#> $Sepal.Width
#> [1] 3.06
#> 
#> $Petal.Length
#> [1] 3.76
#> 
#> $Petal.Width
#> [1] 1.2

myfunc <- function(x) {
  ret <- c(mean(x),sd(x))
  return(ret)
}
result <- lapply(mylist, myfunc)
result
#> $Sepal.Length
#> [1] 5.843 0.828
#> 
#> $Sepal.Width
#> [1] 3.057 0.436
#> 
#> $Petal.Length
#> [1] 3.76 1.77
#> 
#> $Petal.Width
#> [1] 1.199 0.762

t(sapply(result, '[')) # list数据转 data.frame
#>              [,1]  [,2]
#> Sepal.Length 5.84 0.828
#> Sepal.Width  3.06 0.436
#> Petal.Length 3.76 1.765
#> Petal.Width  1.20 0.762
```

## 分组运算

**apply**

第一个参数接收数组或矩阵，MARGIN 参数按行或列计算


```r
set.seed(1)
vec <- round(runif(12)*100)
mat <- matrix(vec, nrow = 3, ncol = 4)
apply(mat, MARGIN = 1, sum) # 1为行，2为列
#> [1] 218 144 228
```

**tapply**

tapply用于分组的循环计算，通过INDEX参数可以把数据集X进行分组，相当于group by的操作。


```r
head(iris)
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> 4          4.6         3.1          1.5         0.2  setosa
#> 5          5.0         3.6          1.4         0.2  setosa
#> 6          5.4         3.9          1.7         0.4  setosa
with(iris, tapply(Sepal.Length, list(Species), mean))
#>     setosa versicolor  virginica 
#>       5.01       5.94       6.59
```

**aggregate**

比较友好，返回的是数据框


```r
aggregate(iris$Sepal.Length, list(iris$Species), mean)
#>      Group.1    x
#> 1     setosa 5.01
#> 2 versicolor 5.94
#> 3  virginica 6.59
```

## 多参数运算

**mapply**

这个函数目前理解的还不是很透彻，待补。

```r
mapply(rep, times = 1:4, x = 4:1)
#> [[1]]
#> [1] 4
#> 
#> [[2]]
#> [1] 3 3
#> 
#> [[3]]
#> [1] 2 2 2
#> 
#> [[4]]
#> [1] 1 1 1 1
```
