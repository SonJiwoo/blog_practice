---
collapsible: false
date: "2021-02-21T10:08:56+09:00"
description: null
draft: false
title: 확률분포
weight: 1
---

# 확률분포(Probability Distribution) 
![확률분포 관계도](images/posts/statistics/prob_dist_relation.png)
<!-- <div style="text-align: center"> 출처: https://artificialnetworkforstarters.readthedocs.io/en/latest/_post/chap6.html </div> -->

##### 연속형(Continuous)
- 정규 분포
- T-분포
- 감마 분포
- 베타 분포
- 카이제곱 분포
- F-분포
- 균일 분포
- 디리클레 분포
- 위샤트 분포
...

##### 이산형(Discrete)
- 이항 분포
- 다항 분포
- 베르누이 분포
- 포아송 분포
- 음이항 분포
- 기하 분포
- 초기하 분포
...

---

#### 정규 분포(Normal Distribution)
{{<expand "정규분포">}}
$$ \text{X~} N(\mu, \sigma^2) \rightarrow f(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp(-\frac{(x-\mu)}{2\sigma^2}^2) $$
$$ E(X) = \mu, Var(X) = \sigma^2$$
{{</expand>}}

#### 다변수 정규분포(Multivariate Normal Distribution)
[관련 포스팅](https://jiwooblog.netlify.app/posts/statistics/statistics/mvn/) 참고

#### T-분포(Student's t-Distribution)
{{<expand "T-분포">}}
$$ \text{X~} t(n) \rightarrow f(x) = \frac{\Gamma(\frac{n+1}{2})}{\Gamma(\frac{n}{2})\cdot\sqrt{\pi n}}(\frac{n}{x^2+n})^\frac{n+1}{2} \ \text{ for } -\infty<x<\infty$$
$$ E(X) = 0, Var(X) = \frac{n}{n-2} $$

T분포는 기본적으로 통계검정을 하기 위해서 작위적으로 고안된 분포이다.  
T분포의 탄생 과정은 아래와 같다.
$$ T = \frac{Z}{\sqrt{\frac{V}{\nu}}} \text{~ } t(df)$$
where `$Z\text{~ }N(0,1), V\text{~ } \chi^2(\nu)$`
{{</expand>}}

#### 이항 분포(Binomial Distribution)
{{<expand "이항분포">}}
$$ \text{X~} Binom(n, p) \rightarrow f(x) = \binom{n}{x}p^x(1-p)^{n-x} $$
$$ E(X) = np, Var(X) = np(1-p) $$
{{</expand>}}

#### 음이항 분포(Negative Binomial Distribution)
n번째 시행에서 r번째 성공을 확률을 구하고자 할 때 활용한다.
음이항 분포에서는 시행 횟수 n이 확률변수이고 성공 횟수 r이 고정되어 있는 반면, 이항분포에서는 시행 횟수 n이 고정되어 있고 성공 횟수 r이 확률변수이다. 그래서 **음이항**분포라고 이름지어진 것이다.
{{<expand "음이항 분포(X : r번째 성공을 얻을 때까지 시행횟수)">}}
$$\text{X~} Negative \  Binomial(r, p) $$
$$\rightarrow f(x) = \binom{x-1}{r-1}p^r(1-p)^{x-r}, \ x=r, r+1, ... $$
$$ E(X) = \frac{r}{p}, \ Var(X) = r \cdot\frac{1-p}{p^2}$$
{{</expand>}}

여기서 x = r+y로 하여, r번째 성공을 얻을 때까지 실패횟수를 계산하면 아래와 같다.  
해당 확률분포 형태에 따라 **음이항**분포라고 이름지어진 것임을 다시 한 번 확인할 수 있다.
{{<expand "음이항 분포(Y : r번째 성공을 얻을 때까지 실패횟수)">}}
$$\text{Y~} Negative \  Binomial(r, p) $$
$$\rightarrow f(y) = \binom{r+y-1}{r-1}p^r(1-p)^y, \ y=0,1,2,...$$
$$\rightarrow f(y) = (-1)^y\binom{-r}{y}p^r(1-p)^y, \ y=0,1,2,... $$
$$ E(Y) = r\cdot\frac{1-p}{p}, \ Var(Y) = r \cdot\frac{1-p}{p^2}$$
{{</expand>}}

#### 감마 분포(Gamma Distribution)
<!--{{<expand "감마분포 (알파: shape, 베타: scale)">}}
$$ \text{X~} Gamma(\alpha, \beta) \rightarrow f(x) = \frac{1}{\beta^\alpha\cdot\Gamma(\alpha)}x^{\alpha-1}e^{-\frac{x}{\beta}}$$
`$\text{for } x>0, \ \alpha>0, \ \beta>0 $`
$$ E(X)=\alpha\beta, \ Var(X)=\alpha\beta^2 $$
{{</expand>}}-->
<!--{{< img src="/images/posts/statistics/gamma_distribution.png" title="Gamma Distribution" caption="k=alpha, theta=beta로 생각하기" width="400px" position="center" >}}-->

참고로, 포아송분포, 지수분포, 카이제곱분포와의 연관성을 생각하면 베타를 rate parameter로 보는 것이 좋다.  
베타를 scale로 보는 방식은 가려두도록 하겠다.
{{<expand "감마분포 (알파: shape, 베타: rate)">}}
$$ \text{X~} Gamma(\alpha, \beta) \rightarrow f(x) = \frac{\beta^\alpha}{\Gamma(\alpha)}x^{\alpha-1}e^{-\beta x}$$
`$\text{for } x>0, \ \alpha>0, \ \beta>0 $`
$$ E(X)=\frac{\alpha}{\beta}, \ Var(X)=\frac{\alpha}{\beta^2} $$
{{</expand>}}
 
{{<expand "역감마 분포 (inverse-Gamma)">}}
$$ \text{X~} inv-Gamma(\alpha, \beta) \rightarrow f(x) = \frac{\beta^\alpha}{\Gamma(\alpha)} \left(\frac{1}{x}\right)^{\alpha+1}exp(-\frac{\beta}{x}) $$
`$\text{for } x>0, \ \alpha>0, \ \beta>0 $`
$$ E(X)=\frac{\beta}{\alpha-1}, \ Var(X)=\frac{\beta^2}{(\alpha-1)^2(\alpha-2)} \ \text{for } \alpha>1$$
{{</expand>}}

{{<expand "스케일된 역감마분포 (scaled inverse-Gamma)">}}
$$\text{X~} \chi^{-2}(\nu, \tau^2) = \Gamma^{-1}(\nu/2, \nu\tau^2/2) $$
$$\rightarrow f(x) = \frac{(\nu\tau^2/2)^{\nu/2}}{\Gamma(\nu/2)}\left(\frac{1}{x}\right)^{\nu/2+1}exp(-\frac{\nu\tau^2}{2x}) $$
{{</expand>}}

#### 베타 분포(Beta Distribution)
{{<expand "베타 분포">}}
$$ \text{X~} Beta(\alpha, \beta) \rightarrow f(x) = \frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}x^{\alpha-1}(1-x)^{\beta-1} $$
`$\text{for } x\in[0,1], \ \alpha>0, \ \beta>0 $`
$$ E(X) = \frac{\alpha}{\alpha+\beta}, \ Var(X) = \frac{\alpha\beta}{(\alpha+\beta)^2(\alpha+\beta+1)}, \ mode(X) = \frac{\alpha-1}{\alpha+\beta-2} (\text{단}, \alpha>1, \ \beta>1)  $$
{{</expand>}}

#### 디리클레 분포(Dirichlet Distribution)
디리클레 분포는 베타분포의 다변량 버전이다.
{{<expand "디리클레 분포">}}
$$\theta \text{ ~ } Dirichlet(\alpha) \rightarrow p(\theta) = \frac{1}{B(\alpha)}\prod_{j=1}^{k}\theta_j^{\alpha_j-1} $$
$$\text{for } B(\alpha) = \frac{\prod\Gamma(\alpha_j)}{\Gamma(\sum \alpha_j)}, \sum_{j=1}^{k}\theta_j=1 $$
{{</expand>}}

#### 포아송 분포(Poisson Distribution)
정해진 시간 안에 어떤 사건이 일어날 횟수에 대한 기댓값을 `$\lambda$` 라고 했을 때, 그 사건이 n회 일어날 확률은 다음과 같다.
{{<expand "포아송 분포">}}
$$ \text{X~} Pois(\lambda) \rightarrow f(x) = \frac{\lambda^x e^\lambda}{x!}$$
for `$x$`: 0이상의 정수, `$\lambda>0$`
$$ E(X) = Var(X) = \lambda $$
{{</expand>}}
{{< img src="/images/posts/statistics/poisson_distribution.png" width="400px" position="center" >}}

#### 지수 분포(Exponential Distribution)
사건이 서로 독립적일 때, 일정 시간동안 발생하는 사건의 횟수가 푸아송 분포를 따른다면, 다음 사건이 일어날 때까지 대기 시간 또는 사건이 한 번 일어날 때까지 걸리는 시간은 지수분포를 따른다. 지수분포는 감마분포의 특수한 형태이다.
{{<expand "지수 분포">}}
$$\text{X~} exp(\lambda) \rightarrow f(x) = \lambda e^{\lambda x} $$
`$exp(\lambda) = \Gamma(1,\lambda) $` where `$\beta$`위치의 모수가 rate parameter를 뜻할 때!
$$ E(X) = \frac{1}{\lambda}, \ Var(X)=\frac{1}{\lambda^2} $$
{{</expand>}}
{{< img src="/images/posts/statistics/exponential_distribution.png" width="400px" position="center" >}}

#### 카이제곱 분포(Chi-squared Distribution)
`$\nu$`개의 서로 독립적인 표준정규분포 확률변수를 각각 제곱한 다음 합해서 얻어지는 분포이다.  
이때 `$\nu$`를 자유도라고 하며, 카이제곱 분포의 매개변수가 된다.
{{<expand "카이제곱 분포">}}
$$\text{X~} \chi^2(\nu) \rightarrow f(x) = \frac{(\frac{1}{2})^\frac{\nu}{2}}{\Gamma(\frac{\nu}{2})}x^{\frac{\nu}{2}-1}e^{-x/2} $$
`$\chi^2(\nu) = \Gamma(\frac{\nu}{2}, \frac{1}{2}) $` where `$\beta$`위치의 모수가 rate parameter를 뜻할 때!
$$ E(X) = \nu, \ Var(X) = 2\nu$$
{{</expand>}}
{{< img src="/images/posts/statistics/chisquared_distribution.png" width="400px" position="center" >}}

{{<expand "역카이제곱 분포 (inverse Chi-squared)">}}
$$\text{X~} \chi^{-2}(\nu) = \Gamma^{-1}(\nu/2, 1/2) $$
$$\rightarrow f(x) = \frac{(1/2)^{\nu/2}}{\Gamma(\nu/2)}\left(\frac{1}{x}\right)^{\nu/2+1}exp(-\frac{1}{2x}) $$ 
{{</expand>}}

{{<expand "스케일된 역카이제곱분포 (scaled inverse chi-squard)">}}
$$\text{X~} \chi^{-2}(\nu, \tau^2) = \Gamma^{-1}(\nu/2, \nu\tau^2/2) $$
$$\rightarrow f(x) = \frac{(\nu\tau^2/2)^{\nu/2}}{\Gamma(\nu/2)}\left(\frac{1}{x}\right)^{\nu/2+1}exp(-\frac{\nu\tau^2}{2x}) $$
{{</expand>}}
 
#### 라플라스 분포(Laplace Distribution)
지수분포를 두 개 붙여놓은 것 같다고 하여 double-exponential distribution이라고도 불린다.
{{<expand "라플라스 분포 (Laplcace Distribution)">}}
$$\text{X~} Laplace(\mu, b) \rightarrow f(x) = \frac{1}{2b}exp(-\frac{|x-\mu|}{b}) $$
$$ E(X) = \mu, Var(X) = 2b^2 $$
{{</expand>}}
{{< img src="/images/posts/statistics/laplace_distribution.png" width="400px" position="center" >}}

---

###### 사진 출처
[1] https://artificialnetworkforstarters.readthedocs.io/en/latest/_post/chap6.html
[2] 위키백과