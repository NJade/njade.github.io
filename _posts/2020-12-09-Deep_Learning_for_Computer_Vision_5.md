---
title: Deep Learning for Computer Vision 5 Neural Network
author: Njade
date: 2020-12-09 23:00:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Motivation
---
* linear classifiers는 매우 간단하고 이해하기 쉽다.
* 하지만 성능은 충분히 좋지 않다.
* 예를 들어 데이터가 원형으로 분리되어 있는 경우 선형 분류기로는 분리할 수 없다.
* 또한 다른 형태의 같은 객체를 파악하기 어렵다.
* 데이터를 feature engineering을 통해 데이터를 feature space로 변환한다면 선형 분류기로 해결할 수 있다.
* 하지만 어떤 형태의 변환이 적절할지 판단하기 매우 어렵다.
* 컴퓨터비전에서는 color histogram, HoG(Histogram of Oriented Gradients) 등을 사용해왔다.
* 특이한 형태로 Bag of Words가 있는데 데이터에서 유도된 feature로 전체 함수를 명시적으로 정의할 필요가 없는 feature이다.

---

## Neural Network
---
* Neural Network는 feature를 어떻게 뽑을지 학습하는 것으로 생각할 수 있다.
* 이전의 Linear score function은 f = Wx였다면
* 2-layer NN은 f = W_2 * max(0, W_1x) 로 표시할 수 있으며 같은 방식으로 layer를 계속 늘려갈 수 있다.
* 전체 원소가 연결된 형태를 Fully-connected NN 혹은 MLP(Multi-Layer Perceptron)이라고 한다.
* 선형 분류기와 별개로 NN 모델에서 템플릿은 명확하지 않으나 일부 명확한 이미지를 통해 선형 분류기가 가지고 있던 템플릿이 1개인 문제를 극복하는 것을 확인할 수 있다.
* 다음 레이어에서 여러 템플릿을 재조합하는 과정을 통해 성능이 좋아지는 것을 설명할 수 있다.
* NN의 템플릿을 확인하면 이전의 연구였던 edge 등을 확인할 수 있다.

---

## Activation Functions
---
* 위 2-layer NN에서 보인 max(0,)는 activation function이다.
* 만약 activation function을 사용하지 않으면 s = W_2 * W_1 * x = W * x로 선형 분류기와 동일해진다.
* 때로는 모델의 설명력을 위해 activation function을 사용하지 않는 deep linear classifier를 만들기도 한다.
* 종류로는 sigmoid, tanh, relu, leaky relu, maxout, elu 등 많은 종류가 있으나 relu는 일반적인 선택이다.
* 신경망에서 시냅스사이의 물질을 주고 받는 것을 함수로 나타내면 비선형 함수로 나타난다.
* 이러한 신경망과 인공신경망을 맵핑하여 생각할 수 있다.
* 하지만 신경망과 인공신경망은 매우 다르다.
* 직선이 두개가 존재한다면 4개의 구역으로밖에 나눌 수 없으며 데이터가 선형으로 분류가 불가능하다면 선형변환은 성능에 도움을 주지 않는다.
* 비선형 변환을 한다면 데이터의 분포가 유효하게 바뀌게 되고 이에 따라 선형분류기로 분류가 가능해진다.
* 활성화 함수는 이러한 비선형 변환을 하게 되어 모델의 복잡도를 올리게 된다.
* 모델이 커지면 overfitting이 일어나고 이를 방지하기 위해 L2를 올리게되면 모델의 decision boundary가 완만해져서 좋은 아이디어가 아니다.

---

## Universal Approximation
---
* NN의 히든레이어는 단순하게 R^n 차원에서 R^m 차원으로 맵핑되는 함수이다.
* hidden unit 4개로 bump function을 만들 수 있다.
* 히든 유닛이 많아지면 bump의 위치를 자유롭게 배치할 수 있게되며 이에 따라 전체 함수는 bump가 될 수 있다.
* 이에 대한 질문으로 bump 사이의 차이, 다른 non-linear, 고차원 함수에 대해서는 어떤가에 대해 질문이 있을 수 있으나 강의에서는 넘어간다.
* 실제론 bump를 학습하지는 않는다.
* 이 universal approximation은 우리에게 NN은 어떤 함수도 표현할 수 있음을 말해준다.
* 이 universal approximation은 SGD를 통해 어떤 함수도 학습할 수 있음과 얼마나 많은 데이터가 필요한지는 말해주지 않는다.
* KNN또한 universal approximtor이지만 실제론 모든 문제를 풀 수 없다.

---

## Convex Function
---
* f가 R^N차원에서 R로 맵핑하는 함수이며, x1, x2가 R^N에 속하고 t가 0에서 1사이일 때 f(t * x1 + (1-t) * x2) <= t * f(x1) + (1 - t) * f(x2)이면 convec function이다.
* 그냥 볼록함수이다. 예시로 x^2이 있으며 cox(x)는 convex가 아니다.
* 고차원에서는 bowl같은 형태가 되며 이러한 함수는 optimize하기 쉬우며 global minimum이 존재하는 것이 보장된다.
* 선형 분류기인 softmax나 svm은 convex function으로 최적화된다.
* 하지만 NN은 convex임이 보장되지 않는다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 