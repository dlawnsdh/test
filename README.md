# Deep Residual Learning for Image Recognition

## Abstract

- Deep neural networks일수록 더 학습하기 어렵다는 점을 보완
- residual(잔차)학습 개념을 도입
- 함수를 새로 만드는 방법 대신에 residual function, 잔차 함수를 learing에 사용하는 것으로 layer를 재구성
- empirical evidence showing 방법
- VGG보다 8배 깊은 152 layers
### 결과
- ILSVRC-2015에서 3.57%의 error rate을 보이며 우승
<br><br>

## Introduction

- 이 논문에서 다루는 문제는 Degradation Problem(network가 깊어질수록 accuracy가 떨어지는 문제, overfitting이 원인이 아니다)
- residual mapping을 통해 degradation의 문제를 해결 가능하다.

![KakaoTalk_20210528_144849074](https://user-images.githubusercontent.com/77203609/119936366-05660780-bfc4-11eb-80f3-17645786083c.png)

- 기존의 networks를 H(k)라 한다면, F(x) = H(x) - x, 즉 출력과 입력간의 차(residual mapping)에 대해 학습을하면 
  degradation의 문제를 해결 가능하다고 제시
- H(x) = F(x) + x이므로 입력값에 출력값을 더하는 identity mapping을 수행하며, Shortcut Connections라고 불리는 방법이다.

## Model

### 데이터 전처리
- 이미지들을 동일한 크기(256x256)으로 resize

### Architecture
#### ReLU
- activation function을 기존 CNN에서 쓰이던 f(x) = tanh(x) 또는 sigmoid가 아닌 f(x) = max(0,x)를 사용
- 기존 함수들은 vanishing gradient를 유발 할 수 있고 large datasets에서 ReLU와 다른 함수와의 속도 차이가 크다.  
![KakaoTalk_20210514_130212144](https://user-images.githubusercontent.com/77203609/118219595-c3fa3600-b4b4-11eb-812d-d41ca4cf925b.png)
-  ReLUs (solid line), tanh neurons(dashed line) CIFAR-10에 대하여 6배 더 빠른 ReLU 

#### Training on Multiple GPUs
- 이 논문에서는 GPU를 두 개로 나누어서 사용
- 결과적으로 CNN연산들을 GPU로 계산함을써 학습속도도 더 빨라지고 error rate 또한 줄어든다

#### Local Response Normalization(LRN)
- lateral inhibition (측면 억제) 개념 사용
- ReLU는 vanishing gradient를 방지하지만 주어진 양수값을 사용하기 때문에 conv나 pooling시 매우 높은 하나의 픽셀값이 
  주변의 픽셀에 영향을 끼치게 되므로 이를 방지하기 위해 Activation map의 같은 위치에 위치한 픽셀끼리 정규화를 한다.
  
#### Overlapping Pooling
- stride = s, pooling을 통해 얻어지는 feature size를 zxz라 하면 s < z 일떄 오버풀링을 하게된다.
- 논문에서는 Overlapping Pooling으로  top-1, top-5의 error rate을 0.4%, 0.3%까지 줄였다.

### Reducing Overfitting
####  Data Augmentation
- Overfitting을 줄이는 방법중 하나는 데이터를 늘리는 것인데 논문에서는 2가지 방법사용 : horizontal reflections, PCA on RGB pixel values
1. horizontal reflections
  - 256x256으로 resized된 이미지들을 224x224의 size로 random crop한다.
2. PCA on RGB pixel values
  - RGB값을 갖는 pixel값 들의 3x3 공분산 행렬을 통해 PCA분석을 하면 eigenvector와 eigenvalue를 얻을 수 있다.
  ![KakaoTalk_20210514_135038924](https://user-images.githubusercontent.com/77203609/118222919-7634fc00-b4bb-11eb-8738-d16dc22112a8.png)
  - 위와 같은 식으로 원본이미지 변경
  
  ![KakaoTalk_20210514_135250501](https://user-images.githubusercontent.com/77203609/118223205-e2affb00-b4bb-11eb-8604-2164e63475d5.png)
  - PCA 적용 예시
  
#### Dropout

   
