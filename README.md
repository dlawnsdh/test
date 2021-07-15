# Sequence to Sequence Learning with Neural Networks

## Abstract

- DNN을 보완
- 크기가 고정된 입력에 대해서만 데이터를 처리할 수 있다는 것이 DNN의 단점인데
  두개의 LSTM을 사용하여 해결
- 두개의 LSTM을 encoder와 decoder로 분류
- 입력을 역순으로 배치하여 더 좋은 성능 발휘 
<br><br>

## Introduction

- 가변 차원의 입력 데이터를 다루기 위해 DNN대신 두개의 LSTM사용
- 문장의 끝을 구분하기 위해 EOS 토큰 사용, EOS가 나올 때까지 입력을 받는다.
- 첫 번째 LSTM은 입력을 순차적으로 받아서 마지막에 large fixed-dimensional vector representation 생성
- 생성된 vector representation를 두 번재 LSTM의 입력으로 사용하여 output생성, 
  decoder LSTM에서 나오는 결과는, input sequence에 의존적이다.
- LSTM이 가변 길이의 입력에 대해서 고정된 길이의 vector representation을 생성할 수 있고,
  Translation을 수행하기 위해서 어느정도의 paraphrasing(다른 말로 바꾸어 표현)을 수행하게 되는데, 
  vector representation은 입력 문장의 의미를 paraphrased한 vector representation을 생성한다고 볼 수 있다. 
  따라서 유사한 의미의 문장에 대해서 유사한 vector representation을 생성하게 된다. 

![image](https://user-images.githubusercontent.com/77203609/125725545-347c14a3-f0ce-4756-a75c-f82b70813c12.png)

- ABC가 encoder LSTM에 입력되고 decoder LSTM에 의해 WXYZ라는 결과가 나옴, 각 문장의 끝은 EOS로 구분
<br>

## Model
- 기존 RNN의 hidden state와 output을 구하는 식

![image](https://user-images.githubusercontent.com/77203609/125728489-293f2647-291f-48fb-9a87-c63a38b50f39.png)
 
- 하지만 입력 길이와 출력 길이의 연관성을 알 수 없을 때 RNN의 적용은 힘들다. 이를 해결하기 위해
  첫 번째 RNN으로 고정된 차원의 vector를 생성하고 두 번째 RNN에 입력으로 주어서 다른 sequence를 결과로 얻을 수 있는데
  RNN은 long-term dependency를 잘 학습하지 못한다. LSTM을 사용하여 학습한다.
<br>

- LSTM은 입력(x1,x2...xT)와 출력(y1,y2...yT')에 대해 조건부 확률을 예측하는 것을 목표로 한다. 
  여기서 T와 T'은 다를 수 있다.
- 입력에 대한 마지막 hidden state(encoder LSTM의 large fixed-dimensional vector representation)를 v라 하면 
  (y1,y2...yT')의 확률은 다음과 같다.
  
![image](https://user-images.githubusercontent.com/77203609/125729830-675bcfd4-6688-4b7c-865e-62c8ac0953dd.png)
<br>

##  Model Architectures

### Feedforward Neural Net Language Model (NNLM)

- input layer, projection layer, hidden layer, output layer로 구성

![nnlm1](https://user-images.githubusercontent.com/77203609/123915588-a1b46d00-d9bb-11eb-835c-6a6fcaa18ce2.png)

- N: input word 개수
- V: vocabulary size
- D: word representation dimenstion
- H: hidden layer size
<br>

- input layer에서 중심 단어의 이전 N개의 단어들의 one-hot encodeing을 수행하여 벡터화
- 각각의 input layer의 벡터들은 N x D 차원의 projection layer로 projection(embedding word vector를 만드는 과정)
- projection layer에 가중치 행렬(D x H)를 곱핞 후 activation function에 넣어 hidden layer 생성(하나의 벡터로 축약)
- hidden layer에서는 softmax와 CELoss사용하여 ouput에 대한 one-hot vector 생성
<br>

- 전체 과정의 계산 복잡도
- Q = N x D + N x D x H + H X V
- 위 수식에서 가장 부하가 큰 연산은 hidden layer에서 output을 만드는 H x V 이지만 
  hierarchical softmax를 사용하여 H × log2V 로 연산량 감축
- 따라서 가장 부하가 큰 연산은 projection layer에서 hidden layer를 만드는 연산인 N x D x H 이다.
<br>

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
<br>

- 전체 과정의 계산 복잡도
- Q = H x H + H X V
- NNLM과 동일하게 hierarchical softmax를 사용하여 H × log2V 로 연산량 감축할 수 있으므로
  가장 부하가 큰 연산은 H x H 이다.
<br>

## New Log-linear Models

###  Continuous Bag-of-Words Model (CBoW)
<br>

###  Continuous Skip-gram Model (skip-gram)
<br>

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

   
