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
  RNN은 long-term dependency를 잘 학습하지 못하므로 LSTM을 사용하여 학습한다.
<br>

- LSTM은 입력(x1,x2,...,xT)와 출력(y1,y2,...,yT')에 대해 조건부 확률을 예측하는 것을 목표로 한다. 
  여기서 T와 T'은 다를 수 있다.
- 입력에 대한 마지막 hidden state(encoder LSTM의 large fixed-dimensional vector representation)를 v라 하면 
  (y1,y2,...,yT')의 확률은 다음과 같다.
  
![image](https://user-images.githubusercontent.com/77203609/125729830-675bcfd4-6688-4b7c-865e-62c8ac0953dd.png)

- T길이의 입력이 주어질때 T'길이의 출력을 생성하고자 하는 것인데 이를 구하기 위해서는 encoder LSTM에서 나온 vector representation인 v와 
  (y1,y2,...,yt-1)까지의 Input이 주어졌을때 yt일 확률을 모두 곱한다.
- 추가적인 구조로는 입력과 출력에 서로 다른 LSTM을 사용하고, 4 layer LSTM을 사용한다. 또한 입력의 순서를 뒤집어서 제공한다.
  입력 문장을 뒤집어도 source와 target 사이 거리의 평균은 동일하게 유지되지만 minimal time lag가 줄어들기 때문에
  성능이 향상된다고 논문에서는 예측한다.
<br>

## Experiment

### Decoding and Rescoring
- training objective

![image](https://user-images.githubusercontent.com/77203609/125730471-10d50056-5766-4c98-876f-536fb463e56f.png)

- correct translation T, source sentence S, training set |S|
- source sentence가 주어질때의 correct translation의 확률을 모두 더한 후 평균을 낸 값을 최대화 한다.

![image](https://user-images.githubusercontent.com/77203609/125730855-d908f217-2e79-4794-9a15-f78c01932baf.png)

- 학습이 끝나면 위 식을 기반으로 translation결과를 생성한다.
- translation과정에서는 left-to-right beam search decoder를 사용한다. 

### Result


