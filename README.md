# ImageNet Classification with Deep Convolutional Neural Networks

## Abstract

- 1000개의 클래스의 1.2 million high-resilution image를 분류
- 5개의 convolutional layers와 maxpooling layers, 3개의 fully connected layer 그리고 다클래스 분류를 위한 softmax function
- AlexNet의 차별화된 기법으로 dropout, GPU 사용
### 결과
- ILSVRC-2012에서 top-5 test error rate이 2위와 10.9%차이를 보였다.

<br><br>

## Model

### 데이터 전처리
- 이미지들을 동일한 크기(256x256)으로 resize
- resized image의 중앙부분을 crop(227x227x3)

### Architecture
#### RelU
- activation function을 기존 CNN에서 쓰이던 f(x) = tanh(x) 또는 sigmoid가 아닌 f(x) = max(0,x)를 사용
- 기존 함수들은 vanishing gradient를 유발 할 수 있고 large datasets에서 RelU와 다른 함수와의 속도 차이가 크다.  
