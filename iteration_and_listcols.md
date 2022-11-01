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
    ## 1  4.26  4.45

``` r
mean_and_sd(list_norm[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -12.1  3.39

``` r
mean_and_sd(list_norm[[3]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  17.0 0.406

``` r
mean_and_sd(list_norm[[4]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.754

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
    ## 1  4.26  4.45
    ## 
    ## [[2]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -12.1  3.39
    ## 
    ## [[3]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  17.0 0.406
    ## 
    ## [[4]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.754

## `map`

``` r
map(list_norm, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.26  4.45
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -12.1  3.39
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  17.0 0.406
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.754

what about other functions …

``` r
map(list_norm, median)
```

    ## $a
    ## [1] 4.020526
    ## 
    ## $b
    ## [1] -12.34606
    ## 
    ## $c
    ## [1] 17.0279
    ## 
    ## $d
    ## [1] 100.125

``` r
map(list_norm, var)
```

    ## $a
    ## [1] 19.78675
    ## 
    ## $b
    ## [1] 11.4635
    ## 
    ## $c
    ## [1] 0.1649877
    ## 
    ## $d
    ## [1] 0.56807

``` r
map(list_norm, summary)
```

    ## $a
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  -3.105   2.244   4.021   4.260   6.673  13.777 
    ## 
    ## $b
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -18.000 -14.800 -12.346 -12.065  -9.961  -6.431 
    ## 
    ## $c
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   16.17   16.83   17.03   17.01   17.21   17.89 
    ## 
    ## $d
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   98.35   99.62  100.13  100.09  100.55  101.52

map variants

``` r
map_dbl(list_norm, median)
```

    ##          a          b          c          d 
    ##   4.020526 -12.346063  17.027901 100.125020

``` r
output = map_df(list_norm, mean_and_sd)
```

## List columns

``` r
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"),
    norm = list_norm
  )

listcol_df[["norm"]]
```

    ## $a
    ##  [1]  6.5723276  2.7213607 -2.5202763  3.5472236 13.7768944  1.6277888
    ##  [7]  4.2864784 10.6809270 -2.4724074  2.4488755  4.6753661  5.2440915
    ## [13]  4.9008324  7.2301519  3.7545734 11.2067299  0.8161885  6.9746761
    ## [19]  2.8233419 -3.1048043
    ## 
    ## $b
    ##  [1] -10.091127 -10.762183 -15.791263 -18.000498 -13.463020 -14.469530
    ##  [7] -12.738578  -6.960312 -11.629999 -13.091516 -12.788570  -6.431275
    ## [13] -15.958713  -9.570973  -6.871145  -7.996300 -11.953548 -10.964524
    ## [19] -15.956685 -15.815248
    ## 
    ## $c
    ##  [1] 16.89004 17.28608 16.16702 16.36902 17.18106 16.96514 16.56631 17.16933
    ##  [9] 17.07184 17.45387 16.65693 17.52720 17.01964 16.93198 16.60223 17.88629
    ## [17] 17.16946 16.97859 17.03616 17.34010
    ## 
    ## $d
    ##  [1] 100.28976 101.17474  99.65968 100.16546  99.17147  99.63666 100.15235
    ##  [8]  99.87964  98.34873  99.28366 100.71622 100.00901  99.42073  99.58058
    ## [15] 100.85806 101.51915 100.44975 100.09769 100.49622 100.84724

``` r
map(listcol_df[["norm"]], mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.26  4.45
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -12.1  3.39
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  17.0 0.406
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  100. 0.754

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
    ## 1 a          4.26 4.45 
    ## 2 b        -12.1  3.39 
    ## 3 c         17.0  0.406
    ## 4 d        100.   0.754

## Something more realistic

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-03-22 14:08:46 (7.641)

    ## file min/max dates: 1869-01-01 / 2022-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-06 10:41:29 (1.697)

    ## file min/max dates: 1965-01-01 / 2020-02-29

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-06 10:41:31 (0.949)

    ## file min/max dates: 1999-09-01 / 2022-09-30

Let’s nest within weather stations

``` r
weather_nest_df = 
  weather_df %>% 
  nest(data = date:tmin)
```

Really is a list column!

``` r
weather_nest_df[["data"]]
```

    ## [[1]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows
    ## 
    ## [[2]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0  26.7  16.7
    ##  2 2017-01-02     0  27.2  16.7
    ##  3 2017-01-03     0  27.8  17.2
    ##  4 2017-01-04     0  27.2  16.7
    ##  5 2017-01-05     0  27.8  16.7
    ##  6 2017-01-06     0  27.2  16.7
    ##  7 2017-01-07     0  27.2  16.7
    ##  8 2017-01-08     0  25.6  15  
    ##  9 2017-01-09     0  27.2  15.6
    ## 10 2017-01-10     0  28.3  17.2
    ## # … with 355 more rows
    ## 
    ## [[3]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01   432  -6.8 -10.7
    ##  2 2017-01-02    25 -10.5 -12.4
    ##  3 2017-01-03     0  -8.9 -15.9
    ##  4 2017-01-04     0  -9.9 -15.5
    ##  5 2017-01-05     0  -5.9 -14.2
    ##  6 2017-01-06     0  -4.4 -11.3
    ##  7 2017-01-07    51   0.6 -11.5
    ##  8 2017-01-08    76   2.3  -1.2
    ##  9 2017-01-09    51  -1.2  -7  
    ## 10 2017-01-10     0  -5   -14.2
    ## # … with 355 more rows

``` r
weather_nest_df[["data"]][[1]]
```

    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows

``` r
lm(tmax ~ tmin, data = weather_nest_df[["data"]][[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest_df[["data"]][[1]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
lm(tmax ~ tmin, data = weather_nest_df[["data"]][[2]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest_df[["data"]][[2]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     20.0966       0.4509

``` r
lm(tmax ~ tmin, data = weather_nest_df[["data"]][[3]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest_df[["data"]][[3]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.499        1.221

Let’s write a short lil of function

``` r
weather_lm = function(df) {
  
  lm(tmax ~ tmin, data = df)
  
}


weather_lm(weather_nest_df[["data"]][[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
map(weather_nest_df[["data"]], weather_lm)
```

    ## [[1]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039  
    ## 
    ## 
    ## [[2]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     20.0966       0.4509  
    ## 
    ## 
    ## [[3]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.499        1.221

Can I do all this in a tidy way?

``` r
weather_nest_df =
  weather_nest_df %>% 
  mutate(
    models = map(data, weather_lm)
  )
```

Un-nesting

``` r
weather_nest_df %>% 
  unnest(data)
```

    ## # A tibble: 1,095 × 7
    ##    name           id          date        prcp  tmax  tmin models
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl> <list>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4 <lm>  
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8 <lm>  
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9 <lm>  
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1 <lm>  
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7 <lm>  
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8 <lm>  
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6 <lm>  
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8 <lm>  
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9 <lm>  
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6   <lm>  
    ## # … with 1,085 more rows

## Napoleon

Here’s my scraping function that works

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
read_page_reviews = function(url){
  
  dynamite_html = read_html(url)
  
  review_titles = 
    dynamite_html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()

  review_stars = 
    dynamite_html %>%
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text() %>%
    str_extract("^\\d") %>%
    as.numeric()
  
  review_text = 
    dynamite_html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    str_replace_all("\n", "") %>% 
    str_trim() %>% 
    str_subset("The media could not be loaded.", negate = TRUE) %>% 
    str_subset("^$", negate = TRUE)
  
  reviews = tibble(
    title = review_titles,
    stars = review_stars,
    text = review_text
  )
  
  reviews
  
}
```

What we did last time:

``` r
base_url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber="

vec_urls = str_c(base_url, c(1, 2, 4, 5))

vec_urls = str_c(base_url, 1:5)

dynamite_reviews =
  bind_rows(
    read_page_reviews(vec_urls[1]),
    read_page_reviews(vec_urls[2]),
    read_page_reviews(vec_urls[3]),
    read_page_reviews(vec_urls[4]),
    read_page_reviews(vec_urls[5])
  )

map(vec_urls, read_page_reviews)
```

    ## [[1]]
    ## # A tibble: 10 × 3
    ##    title                                stars text                              
    ##    <chr>                                <dbl> <chr>                             
    ##  1 Still the best                           5 Completely stupid, absolutely no …
    ##  2 70’s and 80’s Schtick Comedy             5 …especially funny if you have eve…
    ##  3 Amazon Censorship                        5 I hope Amazon does not censor my …
    ##  4 Watch to say you did                     3 I know it's supposed to be a cult…
    ##  5 Best Movie Ever!                         5 We just love this movie and even …
    ##  6 Quirky                                   5 Good family film                  
    ##  7 Funny movie - can't play it !            1 Sony 4k player won't even recogni…
    ##  8 A brilliant story about teenage life     5 Napoleon Dynamite delivers dry hu…
    ##  9 HUHYAH                                   5 Spicy                             
    ## 10 Cult Classic                             4 Takes a time or two to fully appr…
    ## 
    ## [[2]]
    ## # A tibble: 10 × 3
    ##    title                                         stars text                     
    ##    <chr>                                         <dbl> <chr>                    
    ##  1 Sweet                                             5 Timeless Movie. My Grand…
    ##  2 Cute                                              4 Fun                      
    ##  3 great collectible                                 5 one of the greatest movi…
    ##  4 Iconic, hilarious flick ! About friend ship .     5 Who doesn’t love this mo…
    ##  5 Funny                                             5 Me and my dad watched th…
    ##  6 Low budget but okay                               3 This has been a classic …
    ##  7 Disappointing                                     2 We tried to like this, b…
    ##  8 Favorite movie 🍿                                 5 This is one of my favori…
    ##  9 none                                              5 this movie was great Nap…
    ## 10 Great movie                                       5 Vote for pedro           
    ## 
    ## [[3]]
    ## # A tibble: 10 × 3
    ##    title                                                             stars text 
    ##    <chr>                                                             <dbl> <chr>
    ##  1 Get this to improve your nunchuck and bowstaff skills. Dancing i…     5 "Got…
    ##  2 Incredible Movie                                                      5 "Fun…
    ##  3 Always loved this movie!                                              5 "I h…
    ##  4 Great movie                                                           5 "Bou…
    ##  5 The case was damaged                                                  3 "It …
    ##  6 It’s classic                                                          5 "Cle…
    ##  7 Irreverent comedy                                                     5 "If …
    ##  8 Great classic!                                                        5 "Fun…
    ##  9 Most Awesomsomest Movie EVER!!!                                       5 "Thi…
    ## 10 Always a favorite                                                     5 "I r…
    ## 
    ## [[4]]
    ## # A tibble: 10 × 3
    ##    title                                                             stars text 
    ##    <chr>                                                             <dbl> <chr>
    ##  1 It’s not working the disc keeps showing error when I tried other…     1 "It’…
    ##  2 Gosh!                                                                 5 "Eve…
    ##  3 An Acquired Taste                                                     1 "Thi…
    ##  4 What is this ?                                                        4 "Nic…
    ##  5 Napoleon Dynamite                                                     2 "I w…
    ##  6 Great movie                                                           5 "Gre…
    ##  7 Good movie                                                            5 "Goo…
    ##  8 Came as Described                                                     5 "Cam…
    ##  9 Oddly on my list of keepers.                                          5 "Goo…
    ## 10 Low budget fun                                                        5 "Odd…
    ## 
    ## [[5]]
    ## # A tibble: 10 × 3
    ##    title                                                             stars text 
    ##    <chr>                                                             <dbl> <chr>
    ##  1 On a scale of 1 to 10 this rates a minus                              1 "Thi…
    ##  2 I always wondered...                                                  5 "wha…
    ##  3 Audio/video not synced                                                1 "I t…
    ##  4 Kind of feels like only a bully would actually laugh at this...       1 "...…
    ##  5 movie                                                                 5 "goo…
    ##  6 An Overdose of Comical Cringe                                         5 "Exc…
    ##  7 Glad I never wasted money on this                                     2 "I r…
    ##  8 A little disappointed                                                 3 "The…
    ##  9 An (almost) gem. Brought me back to the sweet awkwardness of hig…     5 "To …
    ## 10 How Could You Not Love Napoleon??                                     5 "I r…

``` r
napoleon_reviews = 
  tibble(
    page = 1:5,
    page_url = str_c(base_url, page)
  ) %>% 
  mutate(
    reviews = map(page_url, read_page_reviews)
  )

napoleon_reviews %>% 
  select(-page) %>% 
  unnest(reviews)
```

    ## # A tibble: 50 × 4
    ##    page_url                                                    title stars text 
    ##    <chr>                                                       <chr> <dbl> <chr>
    ##  1 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Stil…     5 Comp…
    ##  2 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… 70’s…     5 …esp…
    ##  3 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Amaz…     5 I ho…
    ##  4 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Watc…     3 I kn…
    ##  5 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Best…     5 We j…
    ##  6 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Quir…     5 Good…
    ##  7 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Funn…     1 Sony…
    ##  8 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… A br…     5 Napo…
    ##  9 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… HUHY…     5 Spicy
    ## 10 https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_c… Cult…     4 Take…
    ## # … with 40 more rows
