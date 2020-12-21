---
title: Deep Learning for Computer Vision 6 Backpropagation
author: Njade
date: 2020-12-22 00:00:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Backpropagation
---
* Loss를 w로 미분하여 gradient를 계산하는 것은 매우 많은 계산이 필요하며 로스가 바뀌면 다시 계산하여야 한다.
* 즉 유연하게 확장이나 로스 변경이 불가능하다.
* 다음으로 나온 아이디어는 Computational Graphs이다.
* 예를 들어 f(x,y,z) = (x+y)*z 인 경우
* 우선 순전파(Forward pass)를 계산한다. - 단순 아웃풋 계산을한다. 
    * q = x + y, f = qz
* 이후 역전파(Backward pass)를 계산한다. - 미분의 체인룰을 이용하여 편미분을 계산한다.
    * df/dx = df/dq\*dq/dx, df/dy = df/dq\*dq/dy, df/dz
* 이러한 계산은 로컬 노드(오직 하나의 노드)의 인풋과 아웃풋에만 국한되기 때문에 모듈화가 가능하다.
* 상위 노드에서 아웃풋쪽으로 오는 gradients를 upstream gradients라고 한다.
* 로컬 노드에서 계산되는 gradients를 local gradients라고 한다.
* 인풋으로 나가는 gradients를 downstream gradients라고 한다.
* 여러 기본 함수(exp, 1/x, ...)를 반복적으로 미분하는 것이 귀찮으면 일부를 합칠 수 있으며 그 예시가 시그모이드이다.
* 시그모이드 함수를 미분하면 (1-sigmoid) * sigmoid로 계산이 된다.
* add gate는 gradient를 분산시킨다. upstream이 2라면 downstream들도 2가 된다.
* copy gate는 gradient를 add 한다. upstream으로 2와 4가 오면 downstream은 6이 된다.
* mul gate는 곱을 교환한다. input이 2, 3이고 upstream으로 5이면 2쪽의 downstream은 3\*5, 3쪽은 2\*5가 된다.
* max gate는 graident router이다. max쪽이 upstream의 gradient를 모두 받는다.

---

## Backprop: vecotr-valued function
---
* x, y 모두 스칼라이면 dy/dx는 스칼라이며 정규 미분으로 가능하다.
* x가 N차원 벡터, y가 스칼라이면 gradient로 계산되며 dy/dx는 N차원 벡터이다.
* x가 N차원 벡터, y가 M차원 벡터이면 Jacobian으로 계산되며 dy/dx는 NxM 행렬이다.
* 딥러닝의 jacobian은 대부분 sparse한 형태로 계산이 되므로 명시적으로 사용하지 않는 것이 좋다.
* Y = WX일 때 dL/dX = (dL/dY) * W_T 로 계산된다. 
* dL/dW = X_T * (dL/dY)로 계산된다.
* 역전파를 이용하면 hessian등과 같은 고차 미분식도 1차 미분식과 동일하게 계산할 수 있다.
* 이러한 특성을 이용하여 gradient의 norm을 이용한 정규화도 있다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 