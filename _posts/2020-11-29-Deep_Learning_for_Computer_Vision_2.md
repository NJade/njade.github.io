---
title: Deep Learning for Computer Vision 2 Image Classification
author: Njade
date: 2020-11-29 23:07:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Image Classification
---
- 이미지가 주어지면 어떤 것의 이미지인가 분류하는 것.
- 컴퓨터는 픽셀 크기와 색 채널로 분류 함. (800x600x3)
- 배경색, 조명, 일부분만 노출되는 등이 어려움으로 존재.
- 이미지에 여러개의 object가 있는 경우 등도 존재할 수 있음 > Image Detection 문제로 확장됌.
- 과거에는 edge를 찾는 등의 문제로 해결하고자 하였음.
- 현재는 machine learning 문제로 해결하고자 함.

---

## Machine Learning (ML)
---
- 과거 edge를 찾는 등의 시도는 알고리즘 기반이었다면 ML은 Data-Driven Approach임.
- 단계는 train과 predict 로 나눠짐.
- MNIST, CIFAR10, CIFAR100, ImageNet, Place365와 같이 computer vision관련된 데이터의 픽셀을 계속 늘어남.
- 반대로 Omniglot과 같이 많은 카테고리(1623)에 적은 이미지(각 카테고리별 20개)인 데이터도 존재함.

---

## Nearest Neighbor
---
- 근접 이웃 모델은 train 단계에서는 단순히 주어진 데이터를 기억하고 predict에서 기억하고 있는 데이터와 비교를 함.
- 데이터를 비교하는 것은 거리를 측정하는데 절댓값을 사용하는 L1과 제곱을 사용하는 L2 등이 존재. (더 많은 종류도 존재)
- train은 O(1) 만큼의 속도를 가짐. - 단순히 기억만 하면되기 때문에 특별한 처리가 없음.
- predict는 O(N) 만큼의 속도를 가짐. - 모든 데이터와 비교를 하기 때문에 N만큼의 속도를 가지고 굉장히 느림.
- 이를 위해 [facebook등에서 연구](https://github.com/facebookresearch/faiss)하는 approximate 방법 등이 존재 
- 배경색 등에 따라 영향을 받기 때문에 성능이 잘 나오지는 않음.

---

## K-Nearest Neighbors
---
- 위에서 소개한 Nearest Neighbors알고리즘은 가장 가까운 1개만을 찾게 되고 이는 이상치 등에 크게 영향을 받게 함.
- 이를 해결하기 위해 가까운 K개를 고려하는 형태로 접근.
- 일반적으로 투표방식.
- L1(Manhattan) distance는 절댓값 방식으로 기하학적으로 마름모 형태를 가지고 L2(Euclidean) distance는 제곱 방식으로 기하학적으로 원의 형태를 가짐.
- NLP등에서는 tf-idf 유사도를 사용하는 등 거리 측정에는 많은 방법이 존재.

---

## Hyperparameters
---
- KNN을 사용한다면 어떤 distance를 사용할 것인가? K는 얼마를 설정할 것인가 등이 hyperparameter가 됌.
- 이것을 결정하기 위해서는 여러가지 방법이 있음.
    1. 전체 데이터를 통해 설정. - train 데이터에 최적화됨. KNN의 경우 K=1인 경우가 항상 최상의 결과를 제공.
    2. train/test - 새로운 데이터에 대해 어떻게 동작할지에 대한 아이디어가 없음.
    3. train/validation/test - hyperparameter는 val 데이터로 설정되며 test 데이터는 오직 한 번만 사용. test데이터에 대해 안 좋은 결과를 주면 그 알고리즘은 안 좋은 것.
    4. Cross-Validation (K-folds) - test셋을 따로 두고 train 데이터를 k개로 나눈 후 k번 train 및 val을 통해 접근. - cost가 큰 편이라 depp learning에서는 자주 사용되지 않음.

---

## KNN - Universal Approximation
---
- trian 데이터가 무한하다면 KNN은 항상 성공하는 알고리즘임.
- 하지만 차원이 높아지면 필요로 하는 데이터가 훨씬 많아짐. (차원의 저주)
- 2^(32x32) = 10^308의 숫자를 가지지만 현재 볼 수 있는 우주에서의 데이터는 10^97개로 KNN을 통해 32x32 데이터를 커버할 수도 없음.
- 테스트가 매우 느리고 데이터 일부가 소실, 이동, 필터 등에 의해 잘 동작하지 않을 확률이 높기 때문에 드물게 사용됌.
- ConvNet Features와 NN을 함께 사용하는 경우 좋은 결과를 보여준다는 연구도 존재.
    - ConvNet Features는 차후 나오겠지만 CNN에서 마지막 분류층을 제거하고 데이터로부터 추출한 feature를 의미.
    - 강의에서 나온 논문(Exploring Nearest Neighbor Approaches for Image Captioning)에서는 VGG16의 fc7 레이어를 사용

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
