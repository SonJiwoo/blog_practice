---
collapsible: false
date: "2021-02-21T10:08:56+09:00"
description: null
draft: false
title: dplyr
weight: 2
---

# dplyr 패키지 훑어보기

```r
library(tidyverse)
library(MASS)
```

# 목차
1. rowwise
1-1. pmax
2. slice
3. relocate
4. lag, lead
5. between, near
6. coalsece
7. recode
8. first, last, nth
9. rownames_to_column, column_to_rownames
10. bind_rows, bind_cols
11. mutate_all, mutate_if
12. inner_join, left_join, right_join, full_join
13. semi_join, anti_join

### 데이터
{{< expand "data" >}}

```r
tmp <- tibble(x=round(rnorm(n=5, mean=5, sd=1)),
       y=round(rnorm(n=5, mean=5, sd=3)),
       z=round(rnorm(n=5, mean=5, sd=5)))
tmp
```

```
## # A tibble: 5 x 3
##       x     y     z
##   <dbl> <dbl> <dbl>
## 1     6     7    -1
## 2     5     4    -2
## 3     5     2     6
## 4     6     2    12
## 5     5     4     2
```

```r
data(survey)
glimpse(survey)
```

```
## Rows: 237
## Columns: 12
## $ Sex    <fct> Female, Male, Male, Male, Male, Female, Male, Female, Male, ...
## $ Wr.Hnd <dbl> 18.5, 19.5, 18.0, 18.8, 20.0, 18.0, 17.7, 17.0, 20.0, 18.5, ...
## $ NW.Hnd <dbl> 18.0, 20.5, 13.3, 18.9, 20.0, 17.7, 17.7, 17.3, 19.5, 18.5, ...
## $ W.Hnd  <fct> Right, Left, Right, Right, Right, Right, Right, Right, Right...
## $ Fold   <fct> R on L, R on L, L on R, R on L, Neither, L on R, L on R, R o...
## $ Pulse  <int> 92, 104, 87, NA, 35, 64, 83, 74, 72, 90, 80, 68, NA, 66, 60,...
## $ Clap   <fct> Left, Left, Neither, Neither, Right, Right, Right, Right, Ri...
## $ Exer   <fct> Some, None, None, None, Some, Some, Freq, Freq, Some, Some, ...
## $ Smoke  <fct> Never, Regul, Occas, Never, Never, Never, Never, Never, Neve...
## $ Height <dbl> 173.00, 177.80, NA, 160.00, 165.00, 172.72, 182.88, 157.00, ...
## $ M.I    <fct> Metric, Imperial, NA, Metric, Metric, Imperial, Imperial, Me...
## $ Age    <dbl> 18.250, 17.583, 16.917, 20.333, 23.667, 21.000, 18.833, 35.8...
```
{{< /expand >}}

#### 1. rowwise()
행별로 최대값 구하기
{{< expand "rowwise 예시" >}}

```r
#올바른 버전
tmp %>%
  rowwise() %>% 
  mutate(max = max(x,y,z))
```

```
## # A tibble: 5 x 4
## # Rowwise: 
##       x     y     z   max
##   <dbl> <dbl> <dbl> <dbl>
## 1     6     7    -1     7
## 2     5     4    -2     5
## 3     5     2     6     6
## 4     6     2    12    12
## 5     5     4     2     5
```

```r
#잘못된 버전
tmp %>%
  mutate(max = max(x,y,z))
```

```
## # A tibble: 5 x 4
##       x     y     z   max
##   <dbl> <dbl> <dbl> <dbl>
## 1     6     7    -1    12
## 2     5     4    -2    12
## 3     5     2     6    12
## 4     6     2    12    12
## 5     5     4     2    12
```
{{< /expand >}}

#### 1-1. pmax
그런데 사실은 여기서 `rowwise`를 사용하지 않고, `pmax`를 사용하면 보다 간단하게 구할 수 있기도 하다.
{{< expand "pmax 예시" >}}

```r
#간단한 버전
tmp %>% 
  mutate(max = pmax(x,y,z))
```

```
## # A tibble: 5 x 4
##       x     y     z   max
##   <dbl> <dbl> <dbl> <dbl>
## 1     6     7    -1     7
## 2     5     4    -2     5
## 3     5     2     6     6
## 4     6     2    12    12
## 5     5     4     2     5
```
{{< /expand >}}

#### 2. slice()
행 선택
{{<expand "slice 예시">}}

```r
# choose 10 rows with smallest 10 pulse values.
survey %>%
  slice_min(Pulse, n = 10)
```

```
##       Sex Wr.Hnd NW.Hnd W.Hnd    Fold Pulse    Clap Exer Smoke Height      M.I
## 1    Male   20.0   20.0 Right Neither    35   Right Some Never 165.00   Metric
## 2  Female   16.5   17.0 Right  L on R    40    Left Freq Never 167.64 Imperial
## 3  Female   18.0   17.5 Right  R on L    48 Neither Freq Never 165.00   Metric
## 4    Male   21.0   21.0 Right  L on R    48 Neither Freq Never 174.00   Metric
## 5  Female   18.0   17.9 Right  R on L    50    Left None Never 165.00   Metric
## 6  Female   15.5   15.5 Right Neither    50   Right Some Regul     NA     <NA>
## 7    Male   18.0   19.0 Right  L on R    54 Neither Some Regul     NA     <NA>
## 8    Male   22.0   21.5  Left  R on L    55    Left Freq Never 200.00   Metric
## 9    Male   20.5   19.5 Right  L on R    56   Right Freq Never 179.00   Metric
## 10   Male   19.8   20.0  Left  L on R    59   Right Freq Never 180.00   Metric
##       Age
## 1  23.667
## 2  17.417
## 3  18.667
## 4  21.333
## 5  30.750
## 6  18.500
## 7  17.750
## 8  18.500
## 9  17.417
## 10 17.417
```

```r
# choose 10 columns with greatest 10 pulses values.
survey %>%
  slice_max(Pulse, n = 10)
```

```
##       Sex Wr.Hnd NW.Hnd W.Hnd   Fold Pulse    Clap Exer Smoke Height      M.I
## 1    Male   19.5   20.5  Left R on L   104    Left None Regul  177.8 Imperial
## 2  Female   19.0   18.5  Left L on R   104    Left Freq Never  170.0   Metric
## 3  Female   18.5   18.0  Left L on R   100 Neither Some Never  171.0   Metric
## 4    Male   21.0   20.4 Right L on R   100   Right Freq Heavy  184.0   Metric
## 5  Female   17.5   17.5 Right R on L    98    Left Freq Never     NA     <NA>
## 6    Male   17.5   17.0  Left L on R    97 Neither None Never  165.0   Metric
## 7    Male   22.5   23.0 Right R on L    96   Right None Never  170.0   Metric
## 8    Male   21.4   21.0 Right L on R    96 Neither Some Never  180.0   Metric
## 9  Female   17.5   17.8 Right R on L    96   Right Some Never     NA     <NA>
## 10 Female   18.5   18.0 Right R on L    92    Left Some Never  173.0   Metric
## 11 Female   18.0   17.7  Left R on L    92    Left Some Never     NA     <NA>
## 12 Female   18.5   18.0 Right R on L    92   Right Freq Never  172.0   Metric
## 13 Female   18.0   18.0 Right L on R    92 Neither Freq Never  165.0   Metric
## 14 Female   16.3   16.2 Right L on R    92   Right Some Regul  152.4 Imperial
## 15   Male   20.0   19.5 Right R on L    92   Right Some Never  179.1 Imperial
##       Age
## 1  17.583
## 2  17.250
## 3  18.917
## 4  20.083
## 5  17.667
## 6  19.500
## 7  19.417
## 8  19.000
## 9  18.667
## 10 18.250
## 11 17.583
## 12 17.500
## 13 20.000
## 14 23.500
## 15 18.917
```

```r
# randomly select 10 rows.
survey %>%
  slice_sample(n = 10)
```

```
##       Sex Wr.Hnd NW.Hnd W.Hnd    Fold Pulse    Clap Exer Smoke Height      M.I
## 1  Female   18.3   18.5 Right  R on L    75    Left Freq Never 170.00   Metric
## 2    Male   20.0   19.5 Right  R on L    92   Right Some Never 179.10 Imperial
## 3  Female   17.5   18.0 Right  R on L    68 Neither Freq Never 157.48 Imperial
## 4  Female   18.5   18.2 Right  R on L    72 Neither Freq Never 167.64 Imperial
## 5  Female   20.5   20.5 Right  R on L    NA    Left Freq Regul     NA     <NA>
## 6    Male   18.0   16.0 Right  R on L    NA   Right Some Never 180.34 Imperial
## 7  Female   17.7   17.0 Right  R on L    76   Right Some Never 167.00   Metric
## 8    Male   16.0   15.5 Right Neither    71   Right Freq Never 154.94 Imperial
## 9    Male   18.5   18.5 Right  L on R    NA Neither Freq Never 171.00   Metric
## 10   Male   20.5   19.5  Left  L on R    80   Right Some Occas 182.88 Imperial
##       Age
## 1  18.750
## 2  18.917
## 3  17.750
## 4  17.333
## 5  19.250
## 6  20.750
## 7  17.250
## 8  17.167
## 9  18.333
## 10 18.667
```

```r
# randomly select 10% of the data observations.
survey %>%
  slice_sample(prop = .05)
```

```
##       Sex Wr.Hnd NW.Hnd W.Hnd    Fold Pulse    Clap Exer Smoke Height      M.I
## 1  Female   17.0   17.0 Right  L on R    79   Right Some Never 163.00   Metric
## 2  Female   17.7   17.0 Right  R on L    76   Right Some Never 167.00   Metric
## 3  Female   16.7   15.1 Right Neither    NA   Right None Never 157.48 Imperial
## 4    Male   19.5   20.5  Left  R on L   104    Left None Regul 177.80 Imperial
## 5    Male   19.5   20.2 Right  R on L    60 Neither Freq Never 185.42 Imperial
## 6  Female   13.0   13.0  <NA>  L on R    70    Left Freq Never 180.34 Imperial
## 7  Female   18.0   17.8 Right  L on R    68   Right Some Never 168.90 Imperial
## 8    Male   20.5   20.0 Right  L on R    68   Right Freq Never 190.00   Metric
## 9    Male   20.5   21.0 Right  R on L    60   Right Freq Never 185.00   Metric
## 10 Female   19.5   19.2 Right  R on L    70   Right Some Never 170.00   Metric
## 11 Female   16.2   16.4 Right  R on L    NA   Right Freq Occas 172.00   Metric
##       Age
## 1  24.667
## 2  17.250
## 3  18.167
## 4  17.583
## 5  32.667
## 6  17.417
## 7  17.083
## 8  17.500
## 9  17.917
## 10 18.167
## 11 17.000
```
{{</expand>}}

#### 3. relocate()
relocate: changes the order of the columns.
{{<expand "relocate 예시">}}

```r
# move columns with factor variables to the front
survey %>%
  relocate(where(is.factor)) %>% colnames()
```

```
##  [1] "Sex"    "W.Hnd"  "Fold"   "Clap"   "Exer"   "Smoke"  "M.I"    "Wr.Hnd"
##  [9] "NW.Hnd" "Pulse"  "Height" "Age"
```

```r
# move Pulse before Height
survey %>%
  relocate(Pulse, .before = Height) %>% colnames()
```

```
##  [1] "Sex"    "Wr.Hnd" "NW.Hnd" "W.Hnd"  "Fold"   "Clap"   "Exer"   "Smoke" 
##  [9] "Pulse"  "Height" "M.I"    "Age"
```

```r
# move Pulse to the end
survey %>%
  relocate(Pulse, .after = last_col()) %>% colnames()
```

```
##  [1] "Sex"    "Wr.Hnd" "NW.Hnd" "W.Hnd"  "Fold"   "Clap"   "Exer"   "Smoke" 
##  [9] "Height" "M.I"    "Age"    "Pulse"
```
{{</expand>}}

#### 4. lag(), lead()
{{<expand "lag, lead 예시">}}

```r
lag(1:5)
```

```
## [1] NA  1  2  3  4
```

```r
lag(1:5, n = 2)
```

```
## [1] NA NA  1  2  3
```

```r
lag(1:5, default = 0)
```

```
## [1] 0 1 2 3 4
```

```r
lead(1:5)
```

```
## [1]  2  3  4  5 NA
```

```r
lead(1:5, default = 6)
```

```
## [1] 2 3 4 5 6
```
{{</expand>}}

#### 5. between(), near()
{{<expand "between, near 예시">}}

```r
# between: >=, <= 조건을 한번에 사용하기
between(1:12, 7, 9)
```

```
##  [1] FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE
```

```r
# near: ==의 안전한 버전(특히 소수점 계산시)
sqrt(2) ^ 2 == 2
```

```
## [1] FALSE
```

```r
near(sqrt(2) ^ 2, 2)
```

```
## [1] TRUE
```
{{</expand>}}

#### 6. coalesce()
각 위치별로 NA가 아닌 값을 첫번째 값을 반환
{{<expand "coalesce 예시">}}

```r
library(tidyverse)
a <- NA
b <- 3
c <- 5
coalesce(a,b,c)
```

```
## [1] 3
```

```r
# 응용: coalesce를 NA imputation으로 활용하기
x <- sample(c(1:5, NA, NA, NA))
coalesce(x, 0)
```

```
## [1] 0 2 0 3 1 4 0 5
```

```r
y <- c(1, 2, NA, NA, 5)
z <- c(NA, NA, 3, 4, 5)
coalesce(y, z)
```

```
## [1] 1 2 3 4 5
```

```r
vecs <- list(
  c(1, 2, NA, NA, 5),
  c(NA, NA, 3, 4, 5)
)
coalesce(!!!vecs)
```

```
## [1] 1 2 3 4 5
```
{{</expand>}}

#### 7. recode()
case_when의 특수한 형태로서 데이터를 교체할 때 사용할 수 있을 것이다.
{{<expand "recode 예시">}}

```r
tmp_char <- sample(c("a", "b", "c"), 10, replace = TRUE)
recode(tmp_char, a = "Apple")
```

```
##  [1] "b"     "b"     "Apple" "Apple" "b"     "b"     "Apple" "b"     "Apple"
## [10] "c"
```

```r
recode(tmp_char, a = "Apple", b = "Banana")
```

```
##  [1] "Banana" "Banana" "Apple"  "Apple"  "Banana" "Banana" "Apple"  "Banana"
##  [9] "Apple"  "c"
```

```r
recode(tmp_char, a = "Apple", b = "Banana", .default = NA_character_)
```

```
##  [1] "Banana" "Banana" "Apple"  "Apple"  "Banana" "Banana" "Apple"  "Banana"
##  [9] "Apple"  NA
```

```r
# 숫자형은 아래와 같이 ``표시가 들어가야 한다.
tmp_num <- sample(c(1,2,3), 10, replace=TRUE)
recode(tmp_num, `1`=5)
```

```
##  [1] 3 5 3 5 2 3 3 2 5 3
```

```r
# !!!을 활용하면, python에서 dictionary 형태로 활용하는 것처럼 쓸 수 있다.
level_key <- c(a = "apple", b = "banana", c = "carrot")
recode(tmp_char, !!!level_key)
```

```
##  [1] "banana" "banana" "apple"  "apple"  "banana" "banana" "apple"  "banana"
##  [9] "apple"  "carrot"
```
{{</expand>}}

#### 8. first(), last(), nth()
첫번째, 마지막 또는 특정 위치에 있는 요소를 반환하는 함수이다.
{{<expand "first, last, nth 예시">}}

```r
x <- 1:10
y <- 10:1

first(x)
```

```
## [1] 1
```

```r
last(y)
```

```
## [1] 1
```

```r
nth(x, 3)
```

```
## [1] 3
```

```r
nth(y, 4)
```

```
## [1] 7
```
{{</expand>}}

#### 9. rownames_to_column(), column_to_rownames()
{{<expand "rownames_to_column, column_to_rownames 예시">}}

```r
a <- rownames_to_column(iris, var = "C")
head(a)
```

```
##   C Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1 1          5.1         3.5          1.4         0.2  setosa
## 2 2          4.9         3.0          1.4         0.2  setosa
## 3 3          4.7         3.2          1.3         0.2  setosa
## 4 4          4.6         3.1          1.5         0.2  setosa
## 5 5          5.0         3.6          1.4         0.2  setosa
## 6 6          5.4         3.9          1.7         0.4  setosa
```

```r
b <- column_to_rownames(a, var = "C")
head(b)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```
{{</expand>}}

#### 10. bind_rows(), bind_cols()
기존의 rbind랑 cbind 대신에 활용하면 될 것 같다.
{{<expand "bind_rows, bind_cols 예시">}}

```r
# bind_rows
one_r <- starwars[1:4, ]
two_r <- starwars[9:12, ]
three_r <- starwars[9:12, 3]
bind_rows(one_r, two_r)
```

```
## # A tibble: 8 x 14
##   name  height  mass hair_color skin_color eye_color birth_year sex   gender
##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
## 1 Luke~    172    77 blond      fair       blue            19   male  mascu~
## 2 C-3PO    167    75 <NA>       gold       yellow         112   none  mascu~
## 3 R2-D2     96    32 <NA>       white, bl~ red             33   none  mascu~
## 4 Dart~    202   136 none       white      yellow          41.9 male  mascu~
## 5 Bigg~    183    84 black      light      brown           24   male  mascu~
## 6 Obi-~    182    77 auburn, w~ fair       blue-gray       57   male  mascu~
## 7 Anak~    188    84 blond      fair       blue            41.9 male  mascu~
## 8 Wilh~    180    NA auburn, g~ fair       blue            64   male  mascu~
## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
## #   vehicles <list>, starships <list>
```

```r
bind_rows(one_r, three_r) # 에러 안 뜸. NA로 채움
```

```
## # A tibble: 8 x 14
##   name  height  mass hair_color skin_color eye_color birth_year sex   gender
##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
## 1 Luke~    172    77 blond      fair       blue            19   male  mascu~
## 2 C-3PO    167    75 <NA>       gold       yellow         112   none  mascu~
## 3 R2-D2     96    32 <NA>       white, bl~ red             33   none  mascu~
## 4 Dart~    202   136 none       white      yellow          41.9 male  mascu~
## 5 <NA>      NA    84 <NA>       <NA>       <NA>            NA   <NA>  <NA>  
## 6 <NA>      NA    77 <NA>       <NA>       <NA>            NA   <NA>  <NA>  
## 7 <NA>      NA    84 <NA>       <NA>       <NA>            NA   <NA>  <NA>  
## 8 <NA>      NA    NA <NA>       <NA>       <NA>            NA   <NA>  <NA>  
## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
## #   vehicles <list>, starships <list>
```

```r
# bind_cols
one_c <- starwars[,1:4]
two_c <- starwars[,7:9]
three_c <- starwars[10:50,7:9]
bind_cols(one_c, two_c)
```

```
## # A tibble: 87 x 7
##    name               height  mass hair_color    birth_year sex    gender   
##    <chr>               <int> <dbl> <chr>              <dbl> <chr>  <chr>    
##  1 Luke Skywalker        172    77 blond               19   male   masculine
##  2 C-3PO                 167    75 <NA>               112   none   masculine
##  3 R2-D2                  96    32 <NA>                33   none   masculine
##  4 Darth Vader           202   136 none                41.9 male   masculine
##  5 Leia Organa           150    49 brown               19   female feminine 
##  6 Owen Lars             178   120 brown, grey         52   male   masculine
##  7 Beru Whitesun lars    165    75 brown               47   female feminine 
##  8 R5-D4                  97    32 <NA>                NA   none   masculine
##  9 Biggs Darklighter     183    84 black               24   male   masculine
## 10 Obi-Wan Kenobi        182    77 auburn, white       57   male   masculine
## # ... with 77 more rows
```

```r
# bind_cols(one_c, three_c) # 에러 뜸, bind_rows와 차이점
```
{{</expand>}}

#### 11. mutate_all, mutate_if
모든 변수를 다 특정 함수를 거친 형태로 바꾸거나, 조건을 주어서 특정 함수를 거친 형태로 바꾸는 함수이다.
{{<expand "mutate_all, mutate_if 예시">}}

```r
iris  %>% mutate_all(as.integer) %>% head() # Species까지 integer로 바꿔버림
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1            5           3            1           0       1
## 2            4           3            1           0       1
## 3            4           3            1           0       1
## 4            4           3            1           0       1
## 5            5           3            1           0       1
## 6            5           3            1           0       1
```

```r
iris %>% mutate_if(is.double, as.integer) %>% head()
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1            5           3            1           0  setosa
## 2            4           3            1           0  setosa
## 3            4           3            1           0  setosa
## 4            4           3            1           0  setosa
## 5            5           3            1           0  setosa
## 6            5           3            1           0  setosa
```
{{</expand>}}

#### 12. inner_join, left_join, right_join, full_join
SQL에서 봤던 join 함수가 역시나 그대로 있다. 그런데 친절하게 무엇을 기준으로 join 했는지도 위에 알려줘서 좋은 것 같다.
{{<expand "join 예시">}}

```r
band_members
```

```
## # A tibble: 3 x 2
##   name  band   
##   <chr> <chr>  
## 1 Mick  Stones 
## 2 John  Beatles
## 3 Paul  Beatles
```

```r
band_instruments
```

```
## # A tibble: 3 x 2
##   name  plays 
##   <chr> <chr> 
## 1 John  guitar
## 2 Paul  bass  
## 3 Keith guitar
```

```r
band_members %>% inner_join(band_instruments)
```

```
## Joining, by = "name"
```

```
## # A tibble: 2 x 3
##   name  band    plays 
##   <chr> <chr>   <chr> 
## 1 John  Beatles guitar
## 2 Paul  Beatles bass
```

```r
band_members %>% left_join(band_instruments)
```

```
## Joining, by = "name"
```

```
## # A tibble: 3 x 3
##   name  band    plays 
##   <chr> <chr>   <chr> 
## 1 Mick  Stones  <NA>  
## 2 John  Beatles guitar
## 3 Paul  Beatles bass
```

```r
band_members %>% right_join(band_instruments)
```

```
## Joining, by = "name"
```

```
## # A tibble: 3 x 3
##   name  band    plays 
##   <chr> <chr>   <chr> 
## 1 John  Beatles guitar
## 2 Paul  Beatles bass  
## 3 Keith <NA>    guitar
```

```r
band_members %>% full_join(band_instruments)
```

```
## Joining, by = "name"
```

```
## # A tibble: 4 x 3
##   name  band    plays 
##   <chr> <chr>   <chr> 
## 1 Mick  Stones  <NA>  
## 2 John  Beatles guitar
## 3 Paul  Beatles bass  
## 4 Keith <NA>    guitar
```
{{</expand>}}

#### 13. semi_join, anti_join
{{<expand "semi_join, anti_join 예시">}}

```r
#이미 조인된 결과값
band_members %>% inner_join(band_instruments) 
```

```
## Joining, by = "name"
```

```
## # A tibble: 2 x 3
##   name  band    plays 
##   <chr> <chr>   <chr> 
## 1 John  Beatles guitar
## 2 Paul  Beatles bass
```

```r
#band_members 중 join될 값 확인
band_members %>% semi_join(band_instruments) 
```

```
## Joining, by = "name"
```

```
## # A tibble: 2 x 2
##   name  band   
##   <chr> <chr>  
## 1 John  Beatles
## 2 Paul  Beatles
```

```r
#band_members 중 join되지 않을 값 확인
band_members %>% anti_join(band_instruments) 
```

```
## Joining, by = "name"
```

```
## # A tibble: 1 x 2
##   name  band  
##   <chr> <chr> 
## 1 Mick  Stones
```
{{</expand>}}

###### 참고
[1] slice와 relocate 예시는 slack `슬기로운통계생활`을 참고하였습니다.
