Writing Functions
================

## Lists

``` r
vec_numeric = 5:8
vec_char = c("My", "name", "is", "Jeff")
vec_logical = c(TRUE, FALSE, TRUE, TRUE)
```

Let’s look at a list

``` r
l = list(
  vec_numeric = 5:8,
  mat         = matrix(1:8, 2, 4),
  vec_logical = c(TRUE, FALSE),
  summary     = summary(rnorm(100))
)
```

Accessing list items

``` r
l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
l[[3]]
```

    ## [1]  TRUE FALSE

``` r
l[["mat"]]
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8

Let’s write a `for` loop to take the mean and SD

``` r
list_norm =
  list(
    a = rnorm(20, 5, 4),
    b = rnorm(20, -12, 3),
    c = rnorm(20, 17, .4),
    d = rnorm(20, 100, 1)
  )
```

Here’s my function

``` r
mean_and_sd = function(x) {

  if (!is.numeric(x)) {
    stop("Z scores only work for numbers")
  }
  
  if (length(x) < 3) {
    stop("Z scores really only work if you have three or more numbers")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  
  tibble(
    mean = mean_x,
    sd = sd_x
  )
  
}
```

Let’s try to make this work

``` r
mean_and_sd(list_norm[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.91  4.31

``` r
mean_and_sd(list_norm[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -10.6  2.64

``` r
mean_and_sd(list_norm[[3]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  16.9 0.223

``` r
mean_and_sd(list_norm[[4]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.976

Let’s try a `for` loop instead

``` r
output = vector("list", length = 4)

output[[1]] = mean_and_sd(list_norm[[1]])

for (i in 1:4) {
  
  output[[i]] = mean_and_sd(list_norm[[i]])
  
}

output
```

    ## [[1]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.91  4.31
    ## 
    ## [[2]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -10.6  2.64
    ## 
    ## [[3]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  16.9 0.223
    ## 
    ## [[4]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.976

## `map`

``` r
map(list_norm, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.91  4.31
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -10.6  2.64
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  16.9 0.223
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.976

what about other functions …

``` r
map(list_norm, median)
```

    ## $a
    ## [1] 4.641307
    ## 
    ## $b
    ## [1] -10.50071
    ## 
    ## $c
    ## [1] 16.88386
    ## 
    ## $d
    ## [1] 100.2768

``` r
map(list_norm, var)
```

    ## $a
    ## [1] 18.57555
    ## 
    ## $b
    ## [1] 6.976901
    ## 
    ## $c
    ## [1] 0.04965024
    ## 
    ## $d
    ## [1] 0.9522141

``` r
map(list_norm, summary)
```

    ## $a
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  -0.369   1.651   4.641   4.912   6.392  18.457 
    ## 
    ## $b
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -16.472 -12.280 -10.501 -10.647  -8.707  -6.722 
    ## 
    ## $c
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   16.46   16.73   16.88   16.87   17.02   17.21 
    ## 
    ## $d
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   98.60   99.62  100.28  100.40  101.14  101.98

map variants

``` r
map_dbl(list_norm, median)
```

    ##          a          b          c          d 
    ##   4.641307 -10.500713  16.883855 100.276765

``` r
output = map_df(list_norm, mean_and_sd)
```

## list columns

``` r
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"),
    norm = list_norm
  )

listcol_df[["norm"]]
```

    ## $a
    ##  [1]  2.7479726  8.2973559  1.6860225  5.4123168  1.5460745  9.9719888
    ##  [7]  5.2019139  6.3799026  3.2457855  0.4671970 -0.3690293  0.4496215
    ## [13]  6.4275468  5.1352091  6.2896902 18.4571485  4.1474054  8.2073876
    ## [19]  3.5987917  0.9441549
    ## 
    ## $b
    ##  [1] -14.550962 -12.155454 -12.974519  -8.486557 -10.989268  -6.721853
    ##  [7]  -7.254889 -16.471655  -7.099109 -10.688243 -10.313183  -7.502879
    ## [13]  -9.398513  -9.494523 -11.865402 -11.877241 -10.217119 -13.447903
    ## [19]  -8.780986 -12.651996
    ## 
    ## $c
    ##  [1] 16.46722 16.78988 16.70059 16.54692 16.89195 16.87576 17.12160 17.20285
    ##  [9] 16.46149 16.90231 16.73604 16.98406 17.20952 16.70339 16.81412 17.06792
    ## [17] 16.79171 16.95952 17.00302 17.09637
    ## 
    ## $d
    ##  [1] 100.88053 101.68031  99.63359 101.00505 100.09293  99.20258 100.52816
    ##  [8]  99.93900  98.59935 101.97962  99.55680  99.71373 100.31524  99.56919
    ## [15] 101.69468 101.53457 100.23829 100.95220 101.57820  99.29916

``` r
map(listcol_df[["norm"]], mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.91  4.31
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -10.6  2.64
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  16.9 0.223
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.976

Can we add list columns and then what

``` r
listcol_df %>% 
  mutate(
    m_sd = map(norm, mean_and_sd)
  )
```

    ## # A tibble: 4 × 3
    ##   name  norm         m_sd            
    ##   <chr> <named list> <named list>    
    ## 1 a     <dbl [20]>   <tibble [1 × 2]>
    ## 2 b     <dbl [20]>   <tibble [1 × 2]>
    ## 3 c     <dbl [20]>   <tibble [1 × 2]>
    ## 4 d     <dbl [20]>   <tibble [1 × 2]>

``` r
listcol_df %>% 
  mutate(
    m_sd = map_df(norm, mean_and_sd)
  ) %>% 
  select(-norm)
```

    ## # A tibble: 4 × 2
    ##   name  m_sd$mean   $sd
    ##   <chr>     <dbl> <dbl>
    ## 1 a          4.91 4.31 
    ## 2 b        -10.6  2.64 
    ## 3 c         16.9  0.223
    ## 4 d        100.   0.976

## Something more realistic
