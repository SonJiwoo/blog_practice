---
collapsible: false
date: "2022-07-21T10:08:56+09:00"
description: AnoGAN
draft: false
title: AnoGAN
weight: 1
---

# AnoGAN
Schlegl, T., Seeböck, P., Waldstein, S. M., Schmidt-Erfurth, U., & Langs, G. (2017, June). Unsupervised anomaly detection with generative adversarial networks to guide marker discovery. In International conference on information processing in medical imaging (pp. 146-157). Springer, Cham.


## In Short
Semi-supervised Anomaly Detection, 근데 GAN을 곁들인. (솔직히 unsupervised는 아니다.)

## 1. Introduction
![AnoGAN2](images/posts/deep_learning/AnoGAN/AnoGAN2.JPG)
![AnoGAN1g](images/posts/deep_learning/AnoGAN/AnoGAN.JPG)

정상데이터만으로 `DCGAN`을 학습시킨 후, image space에서 latent space로의 mapping을 base로 하는 새로운 anoamly score를 제안한 방법론

## 2. Related Work
### 2-1. DCGAN
![DCGAN](images/posts/deep_learning/AnoGAN/DCGAN.JPG)

DCGAN: Deep Convolutional Generative Adversarial Networks
- 특징. **Walking in the latent space**
  - latent vector을 조금씩 변경하면 이미지(그림)도 부드럽게 변경된다.
  - Generator에서 Memorization이 일어나는 것이 아니다. (이미지를 외워서 보여주는 것이 아니다.)
  - 즉, 이미지와 overfitting되는 것이 아니다.

### 2-2. t-SNE embedding
![tsne](images/posts/deep_learning/AnoGAN/tsne.JPG)

t-distributed stochastic neighbor embedding

## 3. Methods

### 3-1. DCGAN
![DCGAN2](images/posts/deep_learning/AnoGAN/DCGAN2.JPG)

`$$\min_G\max_D V(D,G) = E_{x\sim p_{data}(x)}[\log D(x)] + E_{z\sim p_z(z)}[\log(1-D(G(z)))] \\
z : \text{latent vetor} \\
x: \text{data sample} \\
G(z): \text{generated sample using z} \\
D(x): \text{probability that x is a real sample}$$`

정상데이터로 DCGAN을 학습시킨다. 즉, 정상데이터의 Manifold(특징공간)를 학습한다.

### 3-2. Anomaly Score

`$$\begin{align}
&\text{Loss Function } &L(z_\gamma) &= (1-\gamma) \cdot L_R(z_\gamma) + \gamma \cdot L_D(z_\gamma) \\
&\text{Residual Loss } &L_R(z_\gamma) &= \sum|x-G(z_\gamma)| \\
&\text{Discrimination Loss } &L_D(z_\gamma) &= \sum|D(x) - D(G(z_\gamma))|
\end{align}$$`

가중치 `\(\gamma\)`는 주로 0.9으로 준다고 한다.

`Residual Loss`는 간단하게 Generator Anomaly score라고 생각하면 된다. 즉, 실제 데이터와 재구축된 데이터의 차이를 비교하는 값이다.

`Discrimination Loss`는 Discriminator Anomaly Score라고 생각하면 된다. 즉, 실제 데이터의 가능도와 재구축된 데이터의 가능도를 비교하는 값이다.

### 3-3. Invert Mapping

#### 문제 제기
GAN의 generator를 통해서 Latent Vector z에 상응하는 이미지를 생성할 수 있지만, **반대로** 이미지에 맞는 latent space를 생성하는 것은 기존의 방법론을 통해서는 불가능하다.

#### 방법 제시
1. Latent Space 상에서 임의의 latent vector `\(z_0\)`를 잡는다.
2. `\(z_0\)`에 상응하는 `\(x'_0\)`를 구성한다.
3. test data x에 좀 더 가까운 latent vector `\(z_1\)`로 업데이트한다.
4. n번의 업데이트를 시행하여 test data x에 가장 잘 상응하는 latent vector를 찾는다.

## 4. Performance Comparison
### 4-1. Dataset
clinical high resolution SD-OCT volumes of the retina with 49 B-scans 

### 4-2. Baseline
1. `aCAE`: adversarial convolutional autoencoder, runtime 면에서는 효율적. Anomaly score는 residual loss와 discrimination loss 모두 사용하나, 이때 generator는 encoder-decoder 구조
2. `GAN_R`: AnoGAN 형태이나, anomaly scoring 할 때나, invert mapping 할 때 reference anomaly score로 discrimination score만 사용됨.
3. `P_D`: DCGAN

### 4-3. Main Results
![result1](images/posts/deep_learning/AnoGAN/result1.JPG)

![result2](images/posts/deep_learning/AnoGAN/result2.JPG)

![result3](images/posts/deep_learning/AnoGAN/result3.JPG)

![result4](images/posts/deep_learning/AnoGAN/result4.JPG)

## 5. Conclusion
1. 정상데이터만으로 학습 가능
2. 데이터 불균형 문제 해결
3. F-AnoGAN, GANomaly 등으로 발전

# ---

## Critical Point (MY OWN OPINION)
1. 솔직하게 이 방법론은 DCGAN에서 정상데이터만으로 학습하기 때문에, unsupervised라고 하기에는 어렵지 않나 싶다. 굳이 따지자면 semi-supervised가 맞지 않나 싶다.

# ---

## Reference
[1] https://sensibilityit.tistory.com/506
[2] [Youtube 영상](https://www.youtube.com/watch?v=t2eZzmeRcAg)
