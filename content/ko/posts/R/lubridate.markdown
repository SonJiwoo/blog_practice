---
collapsible: false
date: "2021-03-19T10:08:56+09:00"
description: null
draft: false
title: lubridate
weight: 2
---

# lubridate 패키지 훑어보기
lubridate는 날짜 데이터를 처리하기 위한 패키지입니다.


```r
library(lubridate)
```
{{< img src="/images/posts/r/lubridate/lubridate.png" width="250px" position="center" >}}

## 목차
1. parse datetimes

#### 1. parse datetimes
{{<expand "다양한 형태 #1">}}

```r
# 다양한 형태가 있다.
# ymd_hms(), ymd_hm(), ymd_h()
# ydm_hms(), ydm_hm(), ydm_h()
# mdy_hms(), mdy_hm(), mdy_h()
# dmy_hms(), dmy_hm(), dmy_h()
# ymd(), ydm()
# mdy(), myd()
# dmy(), dym()
# yq() Q for quarter

ymd_hms("2017-11-28T14:02:00")
```

```
## [1] "2017-11-28 14:02:00 UTC"
```

```r
ydm_hms("2017-22-12 10:00:00")
```

```
## [1] "2017-12-22 10:00:00 UTC"
```

```r
mdy_hms("11/28/2017 1:02:03")
```

```
## [1] "2017-11-28 01:02:03 UTC"
```

```r
dmy_hms("1 Jan 2017 23:59:59")
```

```
## [1] "2017-01-01 23:59:59 UTC"
```

```r
ymd(20170131)
```

```
## [1] "2017-01-31"
```

```r
mdy("July 4th, 2000")
```

```
## [1] "2000-07-04"
```

```r
dmy("4th of July '99")
```

```
## [1] "1999-07-04"
```

```r
yq("2001: Q3")
```

```
## [1] "2001-07-01"
```
{{</expand>}}

{{<expand "다양한 형태 #2">}}

```r
today()
```

```
## [1] "2021-03-23"
```

```r
now()
```

```
## [1] "2021-03-23 11:24:45 KST"
```

```r
date_decimal(2021.5) #2021년 5월이 아니라, 2021년의 절반
```

```
## [1] "2021-07-02 12:00:00 UTC"
```

```r
fast_strptime('9/1/01', '%y/%m/%d')
```

```
## [1] "2009-01-01 UTC"
```

```r
parse_date_time("9/1/01", "ymd") # fast_strptime과 달리, 조금 더 디테일하게 써줘야한다.
```

```
## Warning: All formats failed to parse. No formats found.
```

```
## [1] NA
```

```r
parse_date_time("2009/1/01", "ymd")
```

```
## [1] "2009-01-01 UTC"
```
{{</expand>}}
