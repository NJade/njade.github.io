---
title: Deep Learning for Computer Vision 7 Convolutional Networks
author: Njade
date: 2021-01-05 00:35:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Motivation
---
* 이전의 full connect linear model은 이미지의 구조를 표현할 수 없다.
* 예를 들어 2x2 이미지를 학습한다면 4x1으로 변경하여야 한다.
* 이를 해결하기 위해 구조적인 형태의 노드를 사용한다.

---

## Convolutional Network
---
* CIFAR10 데이터를 기준으로 이미지는 32x32x3의 형태를 가진다.
* 여기서 3은 depth 혹은 channels로 불린다.
* convlutional filter는 3xnxn이 되며 데이터를 슬라이딩하며 dot product(내적)를 계산한다.
* 이 때 필터의 depth는 데이터의 depth와 동일해야 한다.
* 이런 convolutional filter 여러개를 슬라이딩하며 내적을 계산하면 결과로 나오는 데이터는 w와 h는 줄어들고 convolution filter의 갯수만큼의 depth를 가지게 된다.
* 예를 들어 3x32x32 데이터에 6(filter의 갯수)x3(depth)x5x5 convolution layer를 적용하면 6x28x28이 된다.
* 높이와 너비는 변화하지만 이미지의 구조는 유지가 된다.
* 하나의 이미지만 계산을 하기보다 여러 이미지로 배치 계산을 할 수 있다.
* 일반적인 형태는 인풋은 N x C_in x H x W 이며 Convolution Layer는 C_out x C_in x K_w x K_h이며 아웃풋은 N x C_out x H' x W' 이다.
* 이런 convolution layer를 stacking할 수 있다.
* bias는 convolution layer의 갯수만큼만 가지게 된다. (C_out x 1)
* CNN의 첫 번째 레이어는 보통 엣지를 학습하게 된다.

---

## Padding
---
* CNN의 layer를 계속 쌓으면 아웃풋의 사이즈가 줄어들게된다.
* 이 문제를 해결하기 위해 Input의 각 끝에 데이터를 채워넣는 padding을 하게 된다.
* 여러가지 패딩이 있지만 0을 채워 넣는 것이 가장 일반적이다.
* 패딩 사이즈는 일반적으로 (K(Filter(Kernel) 사이즈) - 1) / 2 로 결정한다.
* Input이 W, Filter가 K, Padding이 P이면 Output은 W - K + 1 + 2*P가 되며 P를 (K - 1) / 2로 잡으면 인풋 사이즈가 유지된다.

---

## Stride
---
* Convolution의 kernel size가 K이면 아웃풋이 의존하는 인풋의 K x K 영역을 recptive field라고 한다.
* L layer일 때 receptive field는 1 + L * (K-1)이 된다.
* 많은 레이어를 쌓게 되면 아웃풋의 1개의 픽셀에 영향을 주는 인풋픽셀이 많아진다. 즉 output의 하나의 영역에 input의 모든 정보가 반영된다.
* 이에 대한 해결책으로 또 다른 하이퍼 파라미터인 stride가 있다. 이는 filter가 얼마나 움직일지 결정하는 인수이다. 이 값을 통해 데이터를 downsampling하게 된다.
* Input이 W, Fitler가 K, Padding이 P, Stride가 S이면 output은 (W - K + 2P) / S + 1 이 된다.

---

## Other dimension CNN
---
* 1x1 convolution은 network in network로 때로 유용한 트릭이다.
* 또 다른 활용으로 1D conv가 있다. inputd이 C_in x W 이면 W가 C_out x C_in x K가 된다. 이는 텍스트, 오디오 데이터 등에 사용된다.
* 3D conv도 존재한다. input이 C_in x H x W x D이고 W가 C_out x C_in x K x K x K가 된다. 이는 3D 데이터나 클라우드 데이터에서 사용된다.

---

## Pooling
---
* Pooling Layer는 downsample을 위한 다른 방법으로 conv와 비슷하게 kernel size, stride, pool function이 하이퍼 파라미터로 존재한다.
* pooling은 kernel 사이즈의 영역에서 scala 데이터를 추출하는 것으로 max, average 등이 있다. 예를 들어 max인 경우 kernel 사이즈영역에서 max값 하나만 추출하게 된다.
* CNN의 일반적인 구조는 conv > pool > conv > pool > fcn > fcn 이다.
* 이러한 구조에서 spatial size는 pooling이나 stride 등으로 줄어들고 channel의 숫자는 증가한다.

---

## Normalizaion
---
* normalization 레이어가 존재하며 일반적인 것은 batch normalization이다.
* 이런 normalization을 하는 이유는 인풋의 분포가 이전 layer를 거치면서 분포가 변하게 되기 때문에 학습에 악영향을 준다는 것에서부터 시작한다. (internal covariate shift)
* input이 NxD인 x일 때 각 채널 별로 shape가 D인 mean(mu)과 variance(sigma^2)를 계산하여 x를 (x - mu) / sqrt(sigma^2 + eps) 로 정규화한다.
* 이후 학습 파라미터인 gamma(sigma)와 beta(mu)를 이용하여 비정규화한다.
* 이러한 batch normalization의 문제는 minibatch에 의존하여 test time에 적용하기 어렵다는 것이다.
* 따라서 학습동안에 mu와 sigma의 이동평균을 계산하여 사용한다.
* mu, sigma, gamma, beta의 차원은 fcn에서는 1xD가 되며 conv에서는 1xCx1x1이 된다.
* 이러한 batch normalization layer는 nonlinearity function 전에 사용한다.
* BN을 사용하게 되면 더 큰 learning rate를 사용할 수 있게 되어 학습을 빠르게 진행할 수 있다.
* 이론적으로 아직 완벽하게 이해되지 않았다.
* training과 test 데이터에서 다르게 동작하는 버그를 발생시킬 수 있다.
* 다른 방법은 layer normalization이 있다.
* 이 layer normalization은 training과 test시에 동일하게 동작하며 mu, sigma, gamma, beta의 차원은 Nx1이다.
* 또 다른 방법은 Instance Normalization이 있으며 이 역시 train/test가 동일하게 동작하며 mu, sigma는 NxCx1x1, gamma, beta는 1xCx1x1의 차원을 가진다.
* group noramlization도 존재하며 layer normalization과 instance normalization의 중간단계이다.

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 