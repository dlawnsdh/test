# Efficient Estimation of Word Representations in Vector Space

## Abstract

- 단어의 벡터화
- 단어를 하나의 작은 단위로 취급하고, one-hot encoding 방식을 사용하는 초기 NLP와는 다르게
  단어의 유사도를 판별할 수 있는 distributed representation 방식을 사용  
- 단어를 벡터화 하여 단어의 유사도를 측정하고, 수치적인 계산이 가능함
- NNLM 보다 더 진화된 CBoW, skip-gram 방식 채용
- VGG보다 8배 깊은 152 layers
###  결과
- ILSVRC-2015에서 3.57%의 error rate을 보이며 우승
<br><br>

## Introduction

- 이 논문에서 다루는 문제는 Degradation Problem(network가 깊어질수록 accuracy가 떨어지는 문제, overfitting이 원인이 아니다.)
- residual mapping을 통해 degradation의 문제를 해결 가능하다.

![KakaoTalk_20210528_144849074](https://user-images.githubusercontent.com/77203609/119936366-05660780-bfc4-11eb-80f3-17645786083c.png)

- 기존의 networks를 H(k)라 한다면, F(x) = H(x) - x, 즉 출력과 입력간의 차(residual mapping)에 대해 학습을하면 
  degradation의 문제를 해결 가능하다고 제시
- H(x) = F(x) + x이므로 identity mapping을 수행하며, Shortcut Connections라고 불리는 방법이다.

## Deep Residual Learning

### Residual Learning
- H(x) : underlying mapping to be fit by a few stacked layers(기존의 networks)
- x : inputs
- F(x) : H(x) - x
- Stacked layer들이 H(x) 대신 residual function인 F(x)를 approximate 하는 것이 학습을 더 수월하게 한다.

### Identity Mapping by Shortcuts
- building block은 y = F(x, {Wi}) + x
- x, y: input and output vectors of the layers
- F(x, {Wi}): residual mapping to be learned
- F + x 연산은 shortcut connection과 element-wise addition에 의해 수행된다.
- 이때 F와 x의 차원이 같지 않으면 linear projection(Ws)을 수행해 차원을 맞춰준다.
- 따라서 y = F(x, {Wi}) + Wsx

### Network Architectures(Plain Network , Residual Network)
![KakaoTalk_20210528_152214069](https://user-images.githubusercontent.com/77203609/119939416-d0a87f00-bfc8-11eb-9ac0-0545b0f55a83.png)

- 가운데는 VGG기반 Plain Network, 오른쪽은 Plain Network에 기반한 Residual Network
1. Plain Network
  - Conv layers는 대부분 3X3 filters를 가지고 있다.
  - 각 layers는 같은 크기의 output feature map을 가지고, 같은 수의 filters를 갖는다.
  - feature map size가 반으로 줄어들었다면, time-complexity를 유지하기 위해 filters의 수는 두 배가 된다.
  - downsampling을 하기 위해서 stride가 2인 conv layers를 통과시킨다. 
  - 마지막엔 global average pooling과 1000-way-fully-connected layer with softmax
  - VGG에 비해  filter의 수가 적고 lower complexity를 가진다는 것이 장점
 
2. Residual Network
  - Plain network에 기반하여, shortcut connections를 추가한 residual version ResNet
  - Identity Shortcut은 input과 output을 같은 차원으로 맞춰줘야 하는데 차원이 증가하면 두 가지 방법을 사용한다.
    1. zero padding, 추가 파라미터는 없음
    2. linear projection
  - 위 두 가지 방법은 shortcut이 feature map을 2씩 건너뛰기 때문에 stride를 2로 사용한다.
 
### Implementation
-  ImageNet data에 적용
-  image resized 224 * 224
-  Batch normalization 사용
-  SGD, mini batch 256
-  learning rate는 0.1에서 시작해서 error가 local minimum에 빠질 때마다 1/10씩 나눠준다.
-  Iteration 60 * 10^4, weight decay 0.0001, momentum 0.9, no dropout
-  test에는 10-crop testing 적용

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

   
