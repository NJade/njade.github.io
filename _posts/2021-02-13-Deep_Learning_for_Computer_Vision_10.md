---
title: Deep Learning for Computer Vision 10 Training Neural Networks
author: Njade
date: 2021-02-13 22:00:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Activation Functions
---
### Sigmoid
* 0~1 사이로 확률로 해석됨.
* x가 매우 작거나 크면 grad가 사라지는 문제가 있음
* 결과가 모두 양수이다 > 여러 레이어를 거치면 x가 항상 양수이므로 grad가 항상 양수이거나 음수가 되며 지그재그로 학습되어 학습이 느려진다. > 미니배치가 도움이 된다.
* exp 함수의 비용이 비싸다.

### Tanh
* -1~1 사이로 0이 중심이지만 여전히 grad가 사라진다.

### Relu
* grad가 사라지지 않으며 계산이 효율적이다.
* 0이 중심히 아니다.
* 전체 데이터 셋이 음수라면 dead Relu로 절대 학습되지 않는다.

### Leaky Relu
* max(-0.1x, x)로 relu의 장점과 더불어 절대 죽지 않는다.
* 유사한 형태로 Parametric Rectifier (PRelu)가 있음. (max(ax, x))

### Exponential Linear Unit(ELU)
* x > 0일 때는 x, x < 0 일때는 a(exp(x) - 1) 을 사용
* Relu의 장점을 가지며 음수영역을 쳐내 노이즈에 로버스트하며 거의 zero mean을 달성한다.
* 하지만 exp 연산이 필요하다.

### Scaled Exponential Linear Unit(SELU)
* x > 0 일 때, lambda * x, x < 0 일 때, lambda * (aexp(x - a))
* a = 1.67..., lambda = 1.05...인 상수
* scaled된 elu이며 deep network에서 더 잘 동작함.
* BatchNorm없이 혼자 Normalizing됨.

* activate function은 성능에 큰 영향을 주지 않으므로 그냥 relu를 사용하여도 괜찮다.

---

## Data Preprocessing
---
* 데이터의 값이 모두 같은 부호라면 평균을 0으로 바꿔주는 것이 좋을 수 있다.
* 데이터가 상관관계를 가진다면 상관관계를 없애주는 것을 고려하는 것이 좋다.
* 대아터가 normalize 되지 않으면 변화에 민감하고 optimize하기 어렵다.
* 이미지는 채널별로 normalize하는 형태를 취할 수도 있으며 PCA와 같은 decorrelation은 일반적이지 않다.

---

## Weight Initialization
---
* 모든 W와 b를 0으로 초기화하면 모든 gradient가 동일하게 된다.
* 작은 랜덤값(~N(0, 0.01)으로 초기화시 네트워크가 깊어지면 grad가 0으로 수렴하여 학습이 잘 되지 않는다.
* std를 크게 하면 grad가 사라지는 영역으로 가버려 학습이 되지 않는다.
* Xavier는 N(0, 1/Din)으로 초기화한다. Conv인 경우 kernel_size^2 * input_channels로 한다.
* Xavier는 relu에 사용시 잘 동작하지 않는다.
* relu는 반대편 절반을 없애기 때문에 var을 2/Din으로 잡는다.
* resnet의 경우 x가 그대로 전파되므로 두 번째 conv의 경우 0으로 초기화한다.

---

## Regularizer
---
* overfitting을 막을 수 있다.
### Dropout
* 순전파시 일부 노드를 0으로 셋팅한다.
* 테스트 타임 시 기댓값을 계산한다 > 단순하게 dropout 확률을 곱한다.
* test time 시를 바꾸지 않고 training time 때 w를 dropout 확률로 나누는 형태로 변경이 가능하다.

### Batch Normalization
* 학습 시 mini batch의 통계량을 사용하고 test 시 training의 값을 기억하였다가 사용

### Data Augmentation
* 데이터를 회전, 반전, 리사이즈 등을 하여 변경하는 것.
* 데이터의 라벨은 바꾸지 않으면서 양을 늘릴 수 있음.
* 테스트 시 고정된 데이터 변형 셋의 평균으로 평가

### Drop Connect
* dropout과 유사하게 랜덤으로 연결
* 테스트시 풀 커넥트

### Fractional Pooling
* 랜덤으로 pooling regions를 결정

### Stochastic Depth
* ResNet에서 residual block을 skip

### Cutout
* 랜덤으로 이미지의 일부를 지움

### Mixup
* 여러 이미지를 섞어 새 이미지를 만든 후 예측값의 확률을 사용하여 학습

* 추세는 Batch Normalization, Data Augmentation, Cutout, Mixup 을 사용

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 