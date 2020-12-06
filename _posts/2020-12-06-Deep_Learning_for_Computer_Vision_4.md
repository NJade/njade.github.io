---
title: Deep Learning for Computer Vision 4 Optimization
author: Njade
date: 2020-12-06 18:00:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Optimization
---
* w^* = argmin_w(L(w)) > loss를 최소화하는 w를 찾는 것

---

## 찾는 방법
### Random Search
* 랜덤으로 w를 뽑으며 loss를 최소한으로 하는 w를 찾는 것
* 정확도는 나쁘지 않지만(15%) sota(95%)에 비하면 매우 나쁘다.

### Follow the slope
* 현재 있는 곳에서 내려가는 곳으로 생각되는 곳으로 진행
* 이 과정을 반복하여 최종적인 목표에 도달하길 기대함
* 미분을 통해 결정                                                          
* W에 대해 dL/dW를 결정하며 W와 dL/dW는 차원이 동일
* 각 차원에 대해 Numeric Gradient로 dL/dW를 결정가능하지만 매우 느림
* 또한 미분정의의 h를 얼마로 정의하느냐에 따라 부정확한 결과를 제공할 수 있으나 쉽다.
* analytic gradient를 통해 정확하고 빠르게 계산이 가능하나 수학적인 미분이 필요하며 여기서 에러를 만들 수 있다.
* numeric graident를 이용하여 analytic gradient를 검증할 수 있다. (gradient check)
* pytorch에서도 구현체를 확인할 수 있으며 gradgradcheck도 존재한다.

---

## Gradient Descent
---
* W를 초기화 한 후 loop를 통해 learning rate와 dw를 곱한 값으로 w를 계속 업데이트한다.
* hyperparameter로 W 초기화 방법, 반복 횟수, learning rate가 존재한다.
* minima를 찾아 갈 때 직선으로 내려가지 않으며 초기에는 빠르게 피팅되나 바닥으로 내려갈수록 평평해지기 때문에 천천히 피팅된다.

---

## Stochastic Gradient Descent
---
* Batch Gradient Descent: loss는 모든 데이터의 합으로 계산되며 이 전체 합을 통해 gradient를 계산하는 것은 매우 비싸다.
* Stochastic Gradient Descent(SGD): minibatch를 이용하여 subset만으로 경사강하를 실시
* Hyperparameter로 W 초기화 방법, 반복 횟수, learning rate, batch size, data sampling 방법이 있다.
* batch size는 gpu등의 메모리에 맞춰서 쓰게 되며 data sampling은 랜덤 혹은 shuffle등의 방법이 있다.
* SGD는 x와 y의 joint distribution의 mc 추정치로 생각할 수 있다. 
* 문제점으로는 Poor Conditioning(한 차원은 빠르게 다른 차원은 느리게 동작하는 경우) 많은 시간이 소모되는 점이 있다.
* local minima 혹은 saddle point에서는 gradient가 0이기 때문에 더 진행되지 않을 수도 있다.
* minibatch로부터 계산된 각각의 gradient들은 매우 noisy하다.

---

## 추가적인 방법
---
### Momentum
* velocity라는 gradient의 평균을 추가적으로 사용
* v = rho * v + dw
* rho는 friction으로 가중평균을 위해 사용되는 값
* v = rho * v - learning_rate * dw 로 변경이 가능하다. 
* local minima나 saddle point에서도 이전의 gradient들이 있기 때문에 탈출이 가능하다. 
* Poor Conditioning의 경우에도 도움이 된다.
* Nesterov Momentum: Gradient를 현재점에서 계산하는 것이 아닌 Velocity계산 후의 점에서 계산
* v_t+1 = rho * v_t - learning_rate * df(x_t + rho * v_t)
* x_t+1 = x_t + v_t+1
* 위의 df(x_t + rho * v_t)를 x_t의 형태로 정리하면
* x_tilt_t = x_t + rho * v_t
* v_t+1 = rho * v_t - learning_rate * df(x_tilt_t)
* x_tilt_t+1 = x_tilt_1 - rho * v_t + (1+rho) * v_t+1 = x_tilt_t + v_t+1 + rho(v_t+1 - v_t) 

### AdaGrad & RMSProp
* dw의 제곱의 힙을 sqrt를 나누어줌
* h += dw * dw
* w -= learing_rate * dw / sqrt(h)
* 경사가 빠른 차원은 천천히, 느린 차원은 빠르게 바꿔준다.
* 학습이 계속되면 h가 계속 커져 학습이 중지된다.
* 이를 극복하기 위한 알고리즘이 RMSProp이다.
* h = decay_rate * h + (1 - decay_rate) * dw * dw

### Adam
* RMSProp + Momentum
* moment1 = beta1 * moment1 + (1 - beta1) * dw
* moment2 = beta2 * moment2 + (1 - beta2) * dw * dw
* w -= learning_rate * moment1 / sqrt(moment2)
* t = 0이고 beta2가 0.999인 경우 moment2가 매우 커져 발산할 수 있다.
* 이를 해결하기 위해 bias correction이 추가된다.
* moment1_unbias = moment1 / (1 - beta1 ** t) 
* moment2_unbias = moment2 / (1 - beta2 ** t) 
* 경험적으로 beta1 = 0.9d, beta2 = 0.999, learning_rate = 1e-3, 5e-4, 1e-4인 경우 많은 경우에 잘 동작한다.

### n-Order Optimization
* First-Order: gradient를 이용해 linear approximation하고 minimize한다.
* Second-Order: Hessian을 이용하여 quadratic approximation하여 minimize한다.
* Second-Order은 더 robust하다.
* Talyor Expansion을 통해 유도되며 비용이 매우 크다.
* Hessian은 O(N^2)이며 inverting은 O(N^3)
* First-Order는 Adam 혹은 잘 튜닝된 momentum이 잘 동작하며
* Second-Order는 L-BFGS가 일반적으로 잘 동작한다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 