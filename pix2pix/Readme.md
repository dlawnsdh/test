# Pix2Pix(Image-to-Image Translation with Conditional Adversarial Networks)

## Abstract

- conditional GAN을 활용한 간단한 image to image translation 방식 제안
- 서로 다른 loss function을 쓰던 이미지에 대한 문제들을 pix2pix를 이용하여 해결

## Introduction

- 이미지 변환 문제들에 대한 일반적인 framework의 필요성
- blurry image가 아닌 realistic한 이미지를 얻기위해 CGAN을 사용하여 진짜같은 이미지를 구현한다.
<br>

## GAN(Generative Adversarial Networks)

- Generative model(생성 모델), unsupervised learning
- 실존하진 않지만 있을 법한 이미지를 생성하는 모델
![image](https://user-images.githubusercontent.com/77203609/136320785-900ade9e-43a1-472a-8430-e01ce8b83797.png)
![image](https://user-images.githubusercontent.com/77203609/136320818-4c362067-b866-4d99-b0e7-1e46e349fc87.png)

- 통계적인 분포 자체를 학습하여 원본 데이터의 분포를 근사할 수 있도록 한다.
### 생성자(generator)와 판별자(discriminator)
- GAN은 생성자와 판별자로 구성

objective function
![image](https://user-images.githubusercontent.com/77203609/136321204-d89fdd02-2693-4548-b97a-47c70230955a.png)

학습과정
![image](https://user-images.githubusercontent.com/77203609/136321272-0d542005-237a-4de6-abac-dd73e329f8fa.png)

### Conditional GAN

- 원하는 데이터를 만들어낼 수 있도록 조건을 추가하는 모델

objective function
![image](https://user-images.githubusercontent.com/77203609/136322021-e7d4143e-03a1-47f7-bbc8-dd92dac04d81.png)

학습과정
![image](https://user-images.githubusercontent.com/77203609/136322143-c33234ce-7a5d-4750-814a-563822860e83.png)
<br>

## pix2pix

- 이미지 자체를 조건으로 받는 cGAN의 한 유형

### Method

![image](https://user-images.githubusercontent.com/77203609/136323042-706bbaef-9bf0-4161-a869-6694cf680f9f.png)
- 판별자는 생성된 이미지와 조건을 한 쌍으로 묶은 데이터를 입력받아 진짜 가짜인지를 판별한다.

### Objective

- objective function은 GAN의 성능을 향상시키기 위해 L1 손실 함수를 cGAN과 함꼐 사용한다.
- GAN은 blurry한 결과가 나오지 않지만 정답과 유사하게 생성하기 위해서 L1을 추가한다.

1. 실제로 있을법한 이미지 생성
![image](https://user-images.githubusercontent.com/77203609/136337185-b9f4e77e-6492-45d6-b45e-fd5a33d6703e.png)

![image](https://user-images.githubusercontent.com/77203609/136337472-bdd29951-1460-4803-9e82-309bfc932099.png)
    - 실제 데이터와 비슷하게 만들기 위한 L1

![image](https://user-images.githubusercontent.com/77203609/136337788-bcc34cd0-a3b9-4dd8-8676-0103f664826e.png)
    - final objective

### Network architectures
