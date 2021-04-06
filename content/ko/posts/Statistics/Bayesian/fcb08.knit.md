---
date: "2021-01-31T10:08:56+09:00"
description: null
draft: false
title: Hierarchical Model
weight: 8
---




# Chapter 08. <br> Group Comparisons and Hierarchical Modeling
�� �������� **First Course in Bayesian Statistical Methods**�� �����Ͽ���.

![](/images/posts/bayesian/hierarchical_model.png)

Hierarchical Model�� �׷� �� �׸��� �׷� �� variability�� �����ϴ� ���� �����ϴ�.
> Hierarchical Model describes both with-in group and between-group variability.

Hierarchical Model�� ������ ��谡 �ٸ� ����о߿� �θ� ���� ���Ǵ� ������ ��Ⱑ �Ǿ���. �׸��� ���� �׸��� ���ؼ� �� �� �ֵ���, ���� ���� �� �̱� ������ ������ ��Ȳ������ Estimation�� �� �� �ִٴ� ������ �ִ�.

���� ū Ư¡�� �����ϰ� ����غ��ڸ�, ���� �׷쳢�� ���� ������ �ְ�޴´ٴ� ���̴�.

## 1. Exchangeability & De Finetti's Theorem
�̺κп� ���ؼ��� [Chapter2](/posts/statistics/bayesian/fcb02/)���� �̹� �ٷ� ���� �ִ�. �׷��� �̹� é���� �������� ���ؼ� ���缺�� �ο����ֱ� ������ �� �� �� �����غ����� �ϰڴ�. �켱 Exchangeability�� ������ ���� �� �� �ִ�.
$$p(y_1, ..., y_n) = p(y_{\pi_{1}}, ..., y_{\pi_{n}})$$
�׸��� De Finetti's Theorem�� exchangeability�� �����Ǹ�, Conditional Independence�� �����ٴ� ���̾���. (�����, �� ���� �ڸ��ϴ�.) �̶� �������δ� small sample from large population�̶�� �Ѵ�. (�ڼ��� ���� FCB�� �о��.) 

## 2. Hierarchical Beta-Binomial 

### 2-1. Posterior ���� ����
###### Joint Posterior
$$\begin{align}
p(\theta,\psi |y) &\propto p(y|\theta, \psi) \ p(\theta, \psi) \\
&= p(y|\theta) \ p(\theta, \psi) \\ 
&= p(y|\theta) \ p(\theta| \psi) \ p(\psi)
\end{align}$$

###### Conditional Posterior
$$\begin{align}
p(\theta | \psi, y) &\propto p(y, \psi|\theta) \ p(\theta) \\
&= p(y|\theta,\psi) \ p(\psi|\theta) \ p(\theta) \\ 
&= p(y|\theta) \ p(\theta, \psi) \\
&= p(y|\theta) \ p(\theta| \psi) \ p(\psi)
\end{align}$$

Joint Posterior�� Conditional Posterior ������ ���������� ���ٴ� ��ǿ� �ָ��غ���.

###### Marginal Posterior
$$p(\psi|y) = \int_\Theta p(\theta, \psi | y)d\theta \\
p(\psi|y) = \frac{p(\theta,\psi|y)}{p(\theta|\psi, y)}$$
�� �� ���� ������ ����Ѵ�.

### 2-2. ���� ����
$$p(\theta, \alpha, \beta|y) \propto p(\alpha,\beta) \ \prod_{j=1}^{m}\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}\theta_j^{\alpha-1}(1-\theta_j)^{\beta-1} \ \prod_{j=1}^{m}\theta_j^{y_j}(1-\theta_j)^{n_j-y_j}$$

3��: hyperprior `$\psi$`
2��: `$\theta_j|\psi \sim Beta(\alpha,\beta)$`
1��: `$y_j|\theta_j \sim Binom(n_j, \theta_j)$`

### 2-3. hyperprior�� ��� �־�� �ұ�?
��к��� �����ڸ�,
$$p(\alpha, \beta) \propto (\alpha+\beta)^{-\frac{5}{2}}$$
�̷��� �شٰ� �Ѵ�.

�̴� ������ �Ʒ��� ���� ���¿���.
$$p\Big(log\frac{\alpha}{\beta}, (\alpha+\beta)^{-\frac{1}{2}}\Big) \propto 1$$
������ȯ�� ���� �����ϴ� ������ [������ȯ](/posts/statistics/statistics/variable_transformation/) �����ÿ� �ڼ��ϰ� ����Ǿ� �ִ�. �׷��� ��������� �Ʒ��� ���� �� �� �ִ�.

$$\rightarrow p(\theta, \alpha, \beta|y) \propto (\alpha+\beta)^{-\frac{5}{2}} \ \prod_{j=1}^{m}\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}\theta_j^{\alpha-1}(1-\theta_j)^{\beta-1} \ \prod_{j=1}^{m}\theta_j^{y_j}(1-\theta_j)^{n_j-y_j}$$

### 2-4. ����
FCB å�� ���� ���� ��(Rat Tumor) ���ø� ���캸��.
![](/images/posts/bayesian/hierarchical_betabinomial2.jpg)

#### 1) 95% Posterior interval

```r
### Raw data
num_diseased <- c(0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,2,2,2,2,2,2,2,2,
                  2,1,5,2,5,3,2,7,7,3,3,2,9,10,4,4,4,4,4,4,4,10,4,4,4,5,11,12,
                  5,5,6,5,6,6,6,6,16,15,15,9,4)
num_of_obs <- c(20,20,20,20,20,20,20,19,19,19,19,18,18,17,20,20,20,20,19,19,18,18,25,24,
                23,20,20,20,20,20,20,10,49,19,46,27,17,49,47,20,20,13,48,50,20,20,20,20,
                20,20,20,48,19,19,19,22,46,49,20,20,23,19,22,20,20,20,52,46,47,24,14)

### Data frame
DF <- data.frame(cbind(num_diseased,num_of_obs))
colnames(DF) <- c("y","n")
DF$ybar = DF$y/DF$n

### theta's uniform prior: beta(1,1)
DF$postmean = (DF$y + 1) / (DF$n + 1)
DF$lb = mapply(function(y, n) qbeta(0.025, y + 1, n - y + 1), DF$y, DF$n)
DF$ub = mapply(function(y, n) qbeta(1 - 0.025, y + 1, n - y + 1), DF$y, DF$n)
title1 = expression(paste("95% Posterior interval, ", beta(1, 1), " prior"))
p1 = ggplot(DF, aes(x = ybar, ymin = lb, ymax = ub)) +
    geom_linerange() + geom_point(aes(y = postmean)) +
    geom_abline(slope = 1, intercept = 0) +
    labs(title = title1, x = "observed mean", y = "posterior mean")
p1
```

<img src="fcb08_files/figure-html/unnamed-chunk-2-1.png" width="672" />

#### 2) Marginal Distribution of alpha & beta

```r
### Hierarchical model using theta's prior: beta(alpha,beta)
griddens = 1e2
A = seq(0.5, 6, length.out = griddens)
B = seq(3, 33, length.out = griddens)
cA = rep(A, each = length(B))
cB = rep(B, length(A))
lpfun = function(a, b, y, n){ # marginal posterior
    (-5/2)* log(a+b) + sum(lgamma(a+b) - lgamma(a) - lgamma(b) + lgamma(a+y) + lgamma(b+n-y) - lgamma(a+b+n))}
lp = mapply(lpfun, cA, cB, MoreArgs = list(DF$y, DF$n))
df_marg = data.frame(alpha= cA, beta= cB, posterior = exp(lp)/sum(exp(lp))) # posterior prob.���� 1�� �ǵ��� ����
title2 = TeX('The marginal of $\\alpha$ and $\\beta$')
p2 = ggplot(data = df_marg, aes(x=alpha, y=beta))+
    geom_raster(aes(fill = posterior, alpha= posterior), interpolate= T)+
    geom_contour(aes(z= posterior), color = 'black', size= 0.2)+
    coord_cartesian(xlim= c(1,5), ylim= c(4,26))+
    labs(x = TeX('$\\alpha$'), y= TeX('$\\beta$'), title= title2)+
    scale_fill_gradient(low= "cornflowerblue", high= "navy", guide= F)+
    scale_alpha(range= c(0,1), guide=F)
p2
```

<img src="fcb08_files/figure-html/unnamed-chunk-3-1.png" width="672" />








