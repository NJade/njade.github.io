---
title: Deep Learning for Computer Vision 8 CNN Architectures
author: Njade
date: 2021-02-12 14:54:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## AlexNet
---
* 8 layer (5 Convolutional + 3 Fully connected) 구조
* pooling layer는 매우 적은 파라미터와 계산량을 사용하여 선호되었다.
* 대부분의 메모리와 floating point operation은 convolution layer에서 사용된다.
* 하지만 대부분의  파라미터는 fully connected layers에서 사용한다.

---

## ZFNet
---
* 조금 더 크게 만든 AlexNet이다.

---

## VGG
---
* 5x5 conv 하나와 3x3 conv 두 개를 사용하는 것이 param과 계산량 관점에서는 훨씬 적게 사용하며 non-linear 계산은 많아진다.
* 커널 사이즈를 늘리는 것보다 채널 수를 늘리는 것이 메모리는 적게 사용하며 더 많은 파라미터를 사용할 수 있다.
* 배치 노말리제이션이 없을 때는 11개의 레이어를 학습시킨 후 레이어를 추가한 후 더 학습시켰다.

---

## GoogleNet
---
* 효율적인 사용에 초점을 맞춤.
* 첫 번째로 stem network를 사용하여 downsampling을 한다.
* 이후 inception module을 사용한다.
* inception module은 1x1 conv, 3x3 conv, 5x5 conv, 1x1 conv와 1x1 conv bottleneck 및 3x3 max pooling을 사용한다.
* 커널 사이즈를 하이퍼 파라미터에서 지우고 모든 커널 사이즈를 사용하는 것을 목표로 한다.
* 1x1 conv는 channel pooling 효과도 가진다.
* 마지막에는 FC를 사용하지 않고 global average pooling을 사용하여 param 숫자를 줄인다.
* classifier를 마지막에만 하지 않고 중간에도 하며 여기서 에러를 전파시키기도 한다.

---

## Residual Network
---
* 적은 레이어가 테스트 에러가 더 작은 현상이 관측되었으며 처음의 예측은 overfitting이었다.
* 하지만 training error도 떨어진 것이 파악되서 underfitting임을 파악하였다.
* 얕은 모델에서 일부를 복사해오고 추가 레이러를 identity로 설정하여 적어도 얕은 모델만큼의 성능을 보이도록 하며 관찰하였다.
* optimization 문제로 파악하고 추가적인 레이어가 identity 함수를 쉽게 학습할 수 있도록 하였다.
* x가 인풋이고 conv를 거친 결과가 F(x)라면 F(x) + x를 아웃풋이 되도록 한다.
* 기본적이 residual block은 3x3 conv를 두 개 사용, bottleneck res block은 1x1(4C->C), 3x3, 1x1(C->4C) conv를 사용
* bottleneck이 floating 계산은 더 적지만 non-linear 연산이 많음.
* bottleneck을 병렬로 처리한 ResNeXt도 존재.

---

## Densely Connected Nerual Network
---
* 다른 블록끼리 모두 연결하는 것.
* gradient가 사라지는 것을 줄이고 feature를 전파하고 재 사용할 수 있게 해준다.

---

## MobileNets
---
* 모바일 기기 등에서 사용할 수 있는 네트워크

---

## Neural Architecture Search
---
* NN 구조를 자동화
* controller라는 네트워크가 구조를 아웃풋으로 제공
* 매우 많은 리소스를 먹으므로 쉽지 않다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 