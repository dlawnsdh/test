# Efficient Estimation of Word Representations in Vector Space

## Abstract

- 단어의 벡터화
- 단어를 하나의 작은 단위로 취급하고, one-hot encoding 방식을 사용하는 초기 NLP와는 다르게
  단어의 유사도를 판별할 수 있는 distributed representation 방식을 사용하여 Embedding
- 단어를 벡터화 하여 단어의 유사도를 측정하고, 수치적인 계산이 가능함
- 기존모델 보다 더 진화된 CBoW, skip-gram 방식 채용
- 구문과 의미론적인 측면에서 모두 높은 정확도를 보일 수 있도록 설계
###  결과
- word2vec의 기존모델인 NNLM보다 속도와 정확도면에서 증가함 
<br><br>

## Introduction

- 단어를 벡터화 함으로써 “King” - “Man” + “Woman” = “Queen” 와 같은 벡터 연산이 가능하다.
- 이 논문에서 다루는 문제는 NNLM, RNNLM의 계산 복잡도(O)가 크다는 것
- CBoW, skip-gram을 통해 계산 복잡도의 문제를 해결 가능하다.

![image](https://user-images.githubusercontent.com/77203609/123912308-d58d9380-d9b7-11eb-8aa1-2977bda30d37.png)

- word2vec의 수치적 연산 예시
- word2vec.kr에서 여러가지 연산을 해볼 수 있다.
<br>

## 학습 복잡도
- 여러 모델을 비교하기위해 학습 복잡도를 정의한다.
- O = E x T x Q
- E = training epochs, T = words in the training set, Q = defined further for each model architecture
- 추가로 모든 모델은 SGD와 backpropagation을 사용하여 학습한다.
<br>

##  Model Architectures

### Feedforward Neural Net Language Model (NNLM)

- input layer, projection layer, hidden layer, output layer로 구성
![nnlm1](https://user-images.githubusercontent.com/77203609/123915588-a1b46d00-d9bb-11eb-835c-6a6fcaa18ce2.png)

- N: input word 개수
- V: vocabulary size
- D: word representation dimenstion
- H: hidden layer size

- input layer에서 중심 단어의 이전 N개의 단어들의 one-hot encodeing을 수행하여 벡터화
- 각각의 input layer의 벡터들은 N x D 차원의 projection layer로 projection(embedding word vector를 만드는 과정)
- projection layer에 가중치 행렬(D x H)를 곱핞 후 activation function에 넣어 hidden layer 생성(하나의 벡터로 축약)
- hidden layer에서는 softmax와 CELoss사용하여 ouput에 대한 one-hot vector 생성

- 전체 과정의 계산 복잡도
- Q = N x D + N x D x H + H X V
- 위 수식에서 가장 부하가 큰 연산은 hidden layer에서 output을 만드는 H x V 이지만 
  hierarchical softmax를 사용하여 H × log2V 로 연산량 감축
- 따라서 가장 부하가 큰 연산은 projection layer에서 hidden layer를 만드는 연산인 N x D x H 이다.

- NNLM의 단점
 1. 파라미터 N이 고정되어 있고, 반드시 지정해야 한다.
 2. 중심 단어의 이전 단어만 고려하고 그 이후의 단어는 고려하지 못한다.
 3. 계산 복잡도가 높고 속도가 느리다.

###  Recurrent Neural Net Language Model (RNNLM)

- input layer, hidden layer, output layer로 구성
![image](https://user-images.githubusercontent.com/77203609/123919682-230dfe80-d9c0-11eb-8797-0df22b036ead.png)

- RNN을 사용한 Language Model
- RNN 구조이므로 projection layer를 제거한다.
- 고정된 갯수의 word를 input으로 받는 NNLM과 다르게 이전의 모든 word의 정보를 반복적으로 hidden layer에 담게 된다. 

- 전체 과정의 계산 복잡도
- Q = H x H + H X V
- NNLM과 동일하게 hierarchical softmax를 사용하여 H × log2V 로 연산량 감축할 수 있으므로
  가장 부하가 큰 연산은 H x H 이다.
  
## New Log-linear Models
---------------------------------
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

   
