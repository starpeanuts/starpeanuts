영화제 수상작과 흥행여부의 상관관계-60160566 김소현
================

1. 요약(초록): 전체 분석 절차와 결론
------------------------------------

영화제의 수상작품과 영화의 흥행도 간 상관관계에 대해 알아보았다. 흥행기준을 전국관객수의 정도라고 가정했을 때, 관객수의 정도가 수상개수여부와 상관관계는 없었다. 하지만 수상개수별 작품의 관객수분포를 알아봤을 때에는 수상개수가 높은 작품일수록 관객수가 높았고, 수상개수가 낮을수록 비교적 적은관객이 본 영화가 많은 것을 알게되었다. 수상개수와 영화의 흥행정도는 필연적인 관계는 아니지만 어느정도 관계가 있는 것을 발견하였다.

2. 분석 주제
------------

### 문제의식

영화제에서 수상을 한다는 것은 배우나 감독, 영화를 제작한 사람들에게 엄청난 영향을 미치는 요소이다. 하지만 한국에서 영화제 수상을 한 작품들을 보면 대부분 흥행에 성공한 작품들만 현실이다. 게다가 15년도 대종상영화제에서 국제시장이 10개의 상을 수상하여 논란이 일어났었다. 이 사건으로 인해 영화의 작품성이 흥행여부로 판단되는 것 인가에 대한 의문이 들었다. \#\#\#알아보고자 하는 것 한국의 영화제에서 수상한 작품들의 흥행여부 관계에 대해 분석해보려고 한다. 흥행정도는 전국관객수로 분석할 것이다. 아무래도 영화를 접한 관객 수가 많을수록 흥행여부와 관련성이 더 깊다고 생각하기 때문이다. 이를 통해 수상개수와 전국관객수의 상관관계를 알아보고 수상개수별 작품들의 관객 수 분포정도를 알아볼 것이다.

3. 데이터 선정
--------------

데이터는 영화진흥위원회의 영화DB중 영화의 개봉연도별 흥행순위와 청룡영화제와 대종상영화제의 수상작품을 이용하였다. 청룡영화제와 대종상영화제만을 채택한 이유는 이 두 개의 영화제들 수상여부에 있어 항상 이슈가 되기 때문이다. 그리고 분석된 결과에 대해 체감이 가능할 수 있도록 2006년에서 2016년도에 개봉되고 수상된 작품들 데이터만 사용할 것이다.

4. 분석
=======

전처리
------

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(ggplot2)
load("moviehit.rda")
load("movietheater.rda")
load("movietype.rda")
load("dj.rda")
load("Cheongnyong.rda")

mhit_c<-mhit
mt_c<-mt
mtype_C<-mtype
dj_c<-dj
cm_c<-cm


dj_c<-dj_c %>% filter(!is.na(감독)&!is.na(수상내역))
cm_c<-cm_c %>% filter(!is.na(감독)&!is.na(수상내역))

dj_c <- rename(dj_c, "영화명"="출품작")
cm_c<-rename(cm_c, "영화명"="출품작")


a<-left_join(mhit_c,cm_c,by="영화명")
b<-left_join(mhit_c,dj_c,by="영화명")

table(is.na(a$"수상내역"))
```

    ## 
    ## FALSE  TRUE 
    ##   117  1837

``` r
table(is.na(b$"수상내역"))
```

    ## 
    ## FALSE  TRUE 
    ##   133  1844

``` r
a1<- a %>% filter(!is.na(수상내역))
b1<- b %>% filter(!is.na(수상내역))
```

영화관객수와 수상개수의 상관관계
--------------------------------

``` r
a2<- a1 %>% 
  group_by(연도,영화명,전국관객수) %>% 
  summarise(an=n())
ggplot(data = a2,aes(x= an, y= 전국관객수))+geom_point()
```

![](ksh_files/figure-markdown_github/unnamed-chunk-2-1.png)

``` r
b2<-b1 %>% 
  group_by(연도,영화명, 전국관객수) %>% 
  summarise(bn=n())
ggplot(data = b2,aes(x= bn, y= 전국관객수))+geom_point()
```

![](ksh_files/figure-markdown_github/unnamed-chunk-2-2.png)

``` r
c3<- mhit_c %>% 
  select(영화명,전국관객수)
a2_c<-a2 %>% select(-전국관객수)
b2_c<-b2 %>% select(-전국관객수)
c3<-left_join(c3,a2_c,by="영화명")
c3<-left_join(c3,b2_c,by="영화명")  
c3$연도.x <- ifelse(is.na(c3$연도.x),0,c3$연도.x)
c3$연도.y <- ifelse(is.na(c3$연도.y),0,c3$연도.y)
c3$an <- ifelse(is.na(c3$an),0,c3$an)
c3$bn <- ifelse(is.na(c3$bn),0,c3$bn)
c3<- c3 %>% 
  mutate(k=연도.x+연도.y , cn=an+bn) %>% 
  filter(k!=0) %>% 
  select(영화명,전국관객수,cn)

ggplot(data = c3,aes(x= cn, y= 전국관객수))+geom_point()
```

![](ksh_files/figure-markdown_github/unnamed-chunk-2-3.png)

2006년부터 2016년까지 영화제 수상작품들의 수상개수와 전국관객수의 상관관계를 나타내기 위해 산점도 그래프를 사용하였다. 첫 번째로 청룡영화제를 분석하였는데 그래프를 보면 점들이 한곳에 모여있지 않아 수상개수와 관객수의 관계가 나타나지 않는다. 즉, 관객수가 많다고 해서 수상을 많이 하는 것이 아님을 예상할 수 있다. 두 번째는 대종상영화제를 분석하였는데 청룡영화제의 그래프와 다른 점이 거의 없음을 보아, 대종상영화제도 같은 결과를 예상할 수 있다. 두 개의 영화제가 비슷한 양상을 띄어 수상개수를 합쳐 분석해보았다. 각각의 영화제를 따로 분석했을 때보다 조금 더 골고루 분포된 것을 알 수 있다.

수상개수에 따른 관객수분포
--------------------------

``` r
table(c3$cn)
```

    ## 
    ##  1  2  3  4  5  6  7  8 10 15 
    ## 44 33 13  5  5  1  3  1  1  1

``` r
c3_1<-c3 %>% filter(cn==1) 
boxplot(c3_1$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-1.png)

    ##         [,1]
    ## [1,]    5732
    ## [2,]  311550
    ## [3,] 1736553
    ## [4,] 4300913
    ## [5,] 7328820

``` r
c3_2<-c3 %>% filter(cn==2) 
boxplot(c3_2$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-2.png)

    ##         [,1]
    ## [1,]     196
    ## [2,] 1168427
    ## [3,] 2304487
    ## [4,] 4598583
    ## [5,] 8487894

``` r
c3_3<-c3 %>% filter(cn==3) 
boxplot(c3_3$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-3.png)

    ##          [,1]
    ## [1,]   595872
    ## [2,]  2594028
    ## [3,]  3408144
    ## [4,]  8245523
    ## [5,] 12811206

``` r
c3_4<-c3 %>% filter(cn==4) 
boxplot(c3_4$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-4.png)

    ##         [,1]
    ## [1,]   20041
    ## [2,] 2160265
    ## [3,] 2663170
    ## [4,] 6619498
    ## [5,] 6619498

``` r
c3_5<-c3 %>% filter(cn==5) 
boxplot(c3_5$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-5.png)

    ##          [,1]
    ## [1,]   221925
    ## [2,]  5071619
    ## [3,]  5593551
    ## [4,] 12319542
    ## [5,] 13019740

``` r
c3_7<-c3 %>% filter(cn==7) 
boxplot(c3_7$전국관객수)$stats
```

![](ksh_files/figure-markdown_github/unnamed-chunk-3-6.png)

    ##          [,1]
    ## [1,]  6878264
    ## [2,]  6975014
    ## [3,]  7071763
    ## [4,]  9222107
    ## [5,] 11372451

두개의 영화제의 수상개수를 더한 데이터로 수상개수별 작품들의 관객수분포를 알아보았다. 그 중 수상개수별 작품이 2개이상일때만 분석을 해보았다. 흥행작품을 관객수가 500만일때로 가정한다면, 수상개수가 2개이하인 작품들의 하위75%, 수상개수가 3~4개인 작품들의 50%는관객수를 500만을 넘기지 못한 흥행하지 못한 작품이라 도출할 수 있다.. 특히 수상개수가 7개인 작품들을 보면, 관객수가 500만을 넘는 흥행작품들이라고 할 수 있다. 수상개수별로 작품관객수분포정도를 비교해 보았을 때, 많은 수상을 한 작품은 흥행한 작품이 대다수이고 적은 수상을 한 작품은 흥행하지 않은 작품이 대다수인 것을 알 수 있었다.

논의
----

전국관객수와 수상작품의 관계를 분석을 해보았는데, 순위가 관객수순으로 되어있어서 아쉬웠다.그리고 2006년도에는 2016년보다 영화관의 접근성이 떨어졌을 것이고, 영화관람에 선호가 지금보다 낮은 정도일수도 있다. 2006년에 500만과 2016년의 500만의 흥행정도는 차이가 있다고 생각한다. 그리고 출품작과 수상작의 순위를 구분하고 싶었지만 출품작에 대한 데이터가 나오지않았고 수상기준이 모호해서 아쉬웠다. 하나의 영화제만 비교하는것이 아쉬워서 두개의 영화제를 비교하였지만, 두영화제의 수상기준은 각각 다르고 수상종목도 조금씩달라서 수상종목별 흥행여부를 분석하지 못 한것이 아쉬웠다.
