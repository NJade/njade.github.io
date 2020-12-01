---
title: Deep Learning for Computer Vision 3 Linear Classifiers
author: Njade
date: 2020-11-29 23:07:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Linear Classifier
---
- Linear Classifier를 쌓은 것이 Neural Network가 된다.
- CIFAR10(50000 train data, 10000 test data, 32x32x3 image) 데이터를 사용할 계획이다.

---

## Algebraic Viewpoint
---
- f(x, W)로 표시될 것이며 W는 가중치 혹은 파라미터라고 표현한다.
- f(x, W) = Wx + b이며 CIFAR10 기준으로 f(x, W)와 b는 (10,) vector이며 W는 (10, 3072) x는 (3072,) 벡터이다.
- b는 가중치
- 10은 data의 라벨 수, 3072는 32x32x3을 펼친 벡터
- b는 x의 마지막에 1을 추가하고 W에 붙일 수 있다. (선형회귀에서 b0항)
- f(cx, W) = W(cx) = c * f(x, W) 로 표기되어 이미지의 전체 값에 0.5를 곱하면 스코어는 바뀌더라도 예측값은 바뀌지 않는다.

---

## Visual Viewpoint
---
- 학습된 W를 이미지로 변경하면 data의 라벨만큼의 이미지가 나오게 되고 이것은 template으로 볼 수 있다.
- 이런 template를 뽑아보면 어떤 경우에 예측에 실패할지 예측할 수 있다.
- template은 오직 하나만 있기 때문에 데이터의 여러 형태를 하나의 template에 반영한다.

---

## Geometric Viewpoint
---
- 픽셀 하나 혹은 두개에 대해서 값을 바꿔보며 그래프를 그리면 선형직선의 형태를 가지게 된다.
- 이 직선은 template이 되게 되고 이 직선에 직각인 방향으로 가게 되면 라벨의 점수가 높아진다.
- 이러한 직선이 라벨을 나누는 hyperplain이다.
- hyperplain은 각 라벨을 나누는 평면이다.
- hyperplain에 대한 자세한 내용은 SVM(Support Vector Machin)을 찾아보자.

---

## Hard Case
---
- XOR 형태의 데이터
- 도넛이든 단순 원이든 원형 데이터가 존재하는 경우
- 위의 경우는 단순 선형직선으로 나눌 수가 없다.

---

## Choose a good W
---
- loss function을 잘 선택해서 w를 최적화한다.
- loss function은 모델이 얼마나 잘 동작하는지 보여주는 함수이다.
- loss가 낮을수록 잘 동작하는 것을 의미한다.
- objective function, cost function이라고도 부른다. (cost와 loss는 실제로는 구분하기도 한다.)
- 반대로 높은 점수일 수록 잘 동작하는 것을 의미하는 함수는 reward, profit, utility, fitness function 등으로 부른다.
- 각각의 데이터에 대해 loss를 계산 후 전체 평균을 계산하여 최종 loss로 사용한다.

---

## Hinge Loss
---
- Multiclass SVM은 Hinge Loss를 사용한다.
- 올바르게 동작한 경우는 0을 나머지는 선형으로 증가한다.
- s = f(x_i, W), class score
- L = sum(max(0, s_j - s_y_i + 1)), i = 1 ~ N, j는 y_i와 같지 않다.
- 올바르게 분류된 경우 데이터가 살짝 바뀌더라도 올바르게 분류된 것이 변경되지 않는다면 loss는 바뀌지 않는다.
- minimum은 0이며 maximum은 infinite이다.
- 모든 score을 랜덤으로 주었을 때 loss의 기댓값은 클래스 하나에 대한 loss는 1이되여 최종적으로 C-1(올바른 클래스 제외)가 된다.
- 이러한 기댓값은 모델을 디버깅할 때 도움이 된다. 만약 loss가 C-1보다 크다면 랜덤보다 못한 성능을 보이거나 버그가 있다고 생각할 수 있다.
- j = y_i인 경우까지 모두 더한다면 단지 1이 추가된 loss를 얻게 되고 다른 영향은 없다.
- sum대신에 mean을 사용하더라도 동일한 결과를 보인다.
- hinge loss를 제곱한 경우는 위와 다르게 non-linear 변환이기 때문에 결과가 바뀌게 된다.

---

## Regularization
---
- L = 0이 되게하는 W는 유일하지 않다. > 선형함수이기 때문에 W에 상수를 곱하여도 동일한 결과를 제공한다.
- L = 0을 만족하는 cW 중 무엇을 사용할 것인지를 정해햐하며 이를 정해줄 방법이 regularization이다.
- 이 정규화는 training data에만 잘 적합되는 것을 막아준다.
- 정규화를 추가한 loss는 L(W) = 1/N * sum(f(x_i, W), y_i) + lamdba * R(W) 이다.
- lamdba는 정규화 강도를 정하는 hyperparameter이다.
- R(W)는 L2, L1, elastic net 등을 사용할 수 있다.
- 정규화를 사용하는 목적은 
    1. training error를 최소화하는 모델들 중 최적화를 찾아준다.
    2. overfitting을 막아준다.
    3. 모델에 곡률을 추가하여 준다. > 선형이 아닌 모델을 적합시키는 것에도 도움을 준다.
- L2는 가중치를 퍼트려주는 역할을 할 수 있다. 
    - [1, 0, 0, 0]보다 [.25, .25, .25, .25]가 loss를 더 낮게 해준다.
- L1은 반대로 하나로 가중치를 뭉쳐주게 해준다. > 모델 압축 등에 도움을 준다.
- 간단한 모델이 해석과 추가적인 데이터 등에 더 robust한 결과를 보여주므로 정규화를 통해 모델을 간단하게 만드는 것이 좋다.

---

## Cross-Entropy Loss
---
- Multinomial logistic Regression에서 사용한다.
- SVM은 스코어로 제공하지만 결과가 확률로 표현되길 기대한다.
- Softmax function을 통해 확률로 변환할 수 있다.
- Softmax function은 e^s_k / sum(e^s_i) 이다.
- cross entropy loss는 -log(Y=y_i\|X=x_i)로 표현한다.
- cross entropy loss를 통한 추정량은 MLE와 동일하게 된다.
- 이 loss의 유도는 Kullback-Leibler divergence로 유도가 된다.
- Kullback-Leibler divergence는 D_KL(P\|\|Q) = sum(P(y)*log(P(y)/Q(y)))이다. (Hogg 책을 펼쳐보자...)
- minimum은 0이고 maximum은 infinite이다.
- 모든 스코어를 완전 랜덤(N(0, sigma))으로 추출할 시 loss의 기댓값은 log(C)가 된다.

---

## Hinge Loss VS Cross-Entropy Loss
---
- Hinge는 0이 되지만 cross-entropy는 절대 0이 되지 않는다.
- 이 차이는 Cross-Entropy Loss를 사용한 모델이 계속 학습되도록 한다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
