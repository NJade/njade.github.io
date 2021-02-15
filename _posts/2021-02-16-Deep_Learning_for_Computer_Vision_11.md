---
title: Deep Learning for Computer Vision 11 Training Neural Networks
author: Njade
date: 2021-02-13 22:00:00 +0900
categories: [Deep Learning]
tags: [Deep Learning, Deep Learning for Computer Vision]
---

이 게시글은 미시간 대학의 [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)를 보고 정리한 것입니다.

---

## Learning rate Schedules
---
* 매우 높은 learning rate를 설정하면 빠르게 발산한다.
* 낮은 learning rate를 설정하면 매우 느리게 수렴한다.
* 높은 learning rate를 설정하면 발산하지는 않으나 최적해를 찾지 못한다.
* 시작은 높은 learning rate를 사용하여 점점 줄여가는 방법을 사용한다.

### Step
* 첫 번째 방법은 step으로 일부 epoch 진행 후 점차적으로 낮추는 방법이다.
* 이 방법은 몇 번의 반복마다 얼마나 낮출 것인지라는 하이퍼 파라미터가 생겨난다.

### Cosine
* a_0 * (1 + cos(t*pi/T)) / 2 라는 식을 선택한다.
* 이 식에서는 a_0와 T를 선택해야 한다. t는 반복 횟수.
* computer vision에서 자주 사용된다.

### Linear
* a_0 * (1 - t/T) 라는 식을 상요
* 일부 문제에서는 잘 동작하였었다.
* NLP에서 자주 사용된다.

### Inverse Sqrt
* a_0 / sqrt(t) 를 사용

### Constant
* 그냥 a_9를 유지하는 것이다.
* sota가 목적이 아니라면 일반적으로 괜찮다.

* sgd나 momentum을 사용한다면 어떤 것을 사용할지 중요하지만 rmsprop이나 adam 등은 그냥 constant를 사용해도 된다.
* validation error가 거의 변하지 않는다면 early stopping을 할 수도 있다.

---

## Choosing Hyperparameters
---
### Grid Search
* 여러 파라미터 셋을 임의로(직접) 선택한다.

### Random Search
* 일부 범위에서 랜덤의로 파라미터를 선택하여 학습한다.

### Grid vs Random
* grid search는 범위내의 여러 파라미터를 충분히 고려하지 못하기 때문에 좋은 성능을 보여주지 못한다.
* 또한 같은 파라미터를 여러 번 테스트하기 때문에 파라미터에 대한 정보를 얻을 수 없다.

### Choosing Hyperparameters
1. 첫 에러를 체크한다. > 이 에러보다 커진다면 파라미터 셋팅을 잘못한 것이다.
2. 작은 샘플에 대해 오버핏팅시킨다.
3. 전체 데이터 셋을 대상으로 적은 수의 반복을 통해 loss가 감소하기 시작하는 LR을 찾는다.
4. 일부 하이퍼 파라미터 셋으로 매우 적은 수의(1~5 epoch)을 시도한다.
5. 4번에서 정한 파라미터로 조금 더 학습시켜본다.
6. learning curve를 확인한다.

* 학습곡선에서 시작 부분이 평평하다면 시작이 잘못된 것이다.
* 학습 이후 평평해진다면 learning decay가 잘못된 것이다.
* 학습 이후 아주 살짝 급감하고 평평해진다면 너무 빨리 decay시킨 것이다.
* training error와 val error가 평평해지지 않고 끝난다면 더 학습시켜야 한다.
* train error와 val error의 차이가 커진다면 overfit이므로 정규화가 필요하다.
* train과 val이 큰 차이가 없다면 underfit이므로 더 큰 모델을 사용하는 것이 좋다.
* w의 업데이트 정도를 추적하며 디버깅할 수도 있다.

---

## Model Ensembles
---
* 여러 독립적인 모델을 만든 후 그 결과들의 평균을 사용한다.
* 2% 정도의 추가 성능을 얻을 수도 있다.
* 여러 모델을 학습 시키는 것이 아닌 하나의 모델을 학습하던 중의 스냅샷을 앙상블시킬 수도 있다.
* 실제 모델 파라미터를 사용하는 것이 아닌 모델 파라미터의 이동 평균을 사용할 수도 있다. (Polyak averaging)

---

## Transfer Learning
---
* 모델을 학습시키기 위해서는 매우 많은 데이터 셋이 필요하다.
* 이 문제를 해결하기 위한 것으로 마지막 일부 레이어를 제거한 후 그 앞의 레이어는 고정시키고 feature extaractor로 사용한다.
* 혹은 이미 학습된 모델에 추가 데이터로 더 학습시키는 fine tuning 시킬 수 있다.
* fine tuning 시 매우 작은 LR를 사용하고 때때로 저레벨 레이어는 고정시키기도 한다.
* 데이터 셋이 tretrain된 모델과 비슷하고 데이터 셋이 적다면 top layer만 Linear classifier하는 것이 좋으며 데이터 셋이 많다면 일부 레이어만 fine tuning하는 접근을 취한다.
* 데이터 셋이 tretrain된 모델과 다르고 데이터 셋이 많다면 더 많은 레이어를 fine tuning한다.
* 매우 적은 데이터가 있다면 fine tuning은 매우 효율적이다.
* 하지만 많은 데이터가 있고 성능이 매우 중요하다면 새로 학습하는 것이 더 성능이 좋을 수 있다.

---

## Distributed Training
---
* 모델을 레이어로 나눠 여러 기기에서 학습하면 일부 기기의 대기시간이 길어진다.
* 모델을 병렬로 학습한다면 기기를 동기화 시키는 비용이 크다.
* 데이터를 나눠 각 기기에서 학습시킨 후 gradients를 교환한 후 서로 업데이트한다.
* 데이터와 모델을 동시에 나눌 수 있으며 이 경우 매우 큰 미니배치가 필요하다.
* 하나의 기기에서 매우 잘 동작되어 여러 기기에서 학습시키고 싶다면 배치 사이즈를 크게하면 되며 배치 사이즈가 K배가 되면 LR도 K배가 되어야 한다.
* 이 경우 LR이 매우 커져 발산할 수도 있으므로 LR을 0부터 차차 증가시키기도 한다.

---

# Reference
---
- [Deep Learning for Computer Vision(EECS 498-007/598-005)](https://web.eecs.umich.edu/~justincj/teaching/eecs498/FA2020/)
- [Youtube](https://www.youtube.com/watch?v=dJYGatp4SvA&list=PL5-TkQAfAZFbzxjBHtzdVCWE0Zbhomg7r)
 