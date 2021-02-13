---
title: Deep Learning for Computer Vision 9 Hardware and Software
author: Njade
date: 2021-02-13 14:54:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Deep Learning Hardware 
---
* 딥러닝이 발전하며 CPU에 비하여 GPU가 매우 크게 성장하였다.
* GPU는 CPU에 비해 코어 하나당 속도는 느리지만 매우 많은 코어가 있다.
* Nvida에서는 tensor core(TPU)와 같은 특별한 하드웨어도 추가하였다.
* 하드웨어 특성에 맞춰 2의 제곱형태의 행렬을 사용하는 것이 효율적이다.
* Nvida tensor core와 유사한 google TPU도 존재하며 이 하드웨어는 살 수는 없다.

---

## Deep Learning Software
---
* 빠른 프로토타입을 만들 수 있으며 gradient도 자동으로 계산해주며 효율적으로 GPU나 TPU를 사용할 수 있게 해준다.

---

## PyTorch
---
* Tensor: numpy array와 유사하지만 GPU에서 실행 가능
* Autograd: Tensor를 이용한 computational graph를 만들어주고 자동으로 gradient를 계산하여 준다.
* Module: NN layer, state를 저장하거나 weight를 계산하여준다.
* torch.no_grad(): 이 영역에서는 grad를 추적하지 않을 것을 선언
* grad_zero(): 기본적으로 grad를 accumulate하기 때문에 초기화
* Dynamic Computation Graphs: backward() 시 모든 그래프를 파괴하였다가 다시 그리는 다이나믹 그래프를 사용한다.
* TyPorch도 JIT을 통해 static graph를 사용하는 것이 가능하다.
* static graph를 사용하면 framework가 최적화를 해줄 수 있다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 