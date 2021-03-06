# Pix2Pix(Image-to-Image Translation with Conditional Adversarial Networks)

## Abstract

- conditional GAN을 활용한 간단한 image to image translation 방식 제안
- 서로 다른 loss function을 쓰던 이미지에 대한 문제들을 pix2pix를 이용하여 해결

## Introduction

- 이미지 변환 문제들에 대한 일반적인 framework의 필요성
- blurry image가 아닌 realistic한 이미지를 얻기위해 cGAN을 사용하여 진짜같은 이미지를 구현한다.
<br>

## GAN(Generative Adversarial Networks)

- Generative model(생성 모델), unsupervised learning
- 실존하진 않지만 있을 법한 이미지를 생성하는 모델
![image](https://user-images.githubusercontent.com/77203609/136320785-900ade9e-43a1-472a-8430-e01ce8b83797.png)
![image](https://user-images.githubusercontent.com/77203609/136320818-4c362067-b866-4d99-b0e7-1e46e349fc87.png)

- 통계적인 분포 자체를 학습하여 원본 데이터의 분포를 근사할 수 있도록 한다.
### 생성자(generator)와 판별자(discriminator)
- GAN은 생성자와 판별자로 구성

1. objective function

![image](https://user-images.githubusercontent.com/77203609/136321204-d89fdd02-2693-4548-b97a-47c70230955a.png)

2. 학습과정

![image](https://user-images.githubusercontent.com/77203609/136321272-0d542005-237a-4de6-abac-dd73e329f8fa.png)

### Conditional GAN

- 원하는 데이터를 만들어낼 수 있도록 조건을 추가하는 모델

1. objective function

![image](https://user-images.githubusercontent.com/77203609/136322021-e7d4143e-03a1-47f7-bbc8-dd92dac04d81.png)

2. 학습과정

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

2. 실제 데이터와 비슷하게 만들기 위한 L1

![image](https://user-images.githubusercontent.com/77203609/136337472-bdd29951-1460-4803-9e82-309bfc932099.png)

3. final objective

![image](https://user-images.githubusercontent.com/77203609/136337788-bcc34cd0-a3b9-4dd8-8676-0103f664826e.png)
   

### Network architectures

![image](https://user-images.githubusercontent.com/77203609/136339669-222e80fe-0346-4d26-b4d1-8f03069d4dfe.png)

- U-Net 구조를 이용한 skip connection 기법 사용
- discriminator에서는 PatchGAN 분류 모델을 사용

#### U-Net

- patch

![image](https://user-images.githubusercontent.com/77203609/136340096-6c74e41c-8e6f-4b4a-bae8-3e7e17945cc3.png)

- U-Net 구조

![image](https://user-images.githubusercontent.com/77203609/136340377-74739b6e-abd7-474c-8ec2-e00f87f69d84.png)

- 회색 화살표가 skip connection(decoding 영역의 feature map이 encoding 영역의 feature map과 대응된다.)
- PatchGAN은 이미지 전체에 대하여 진위여부를 판별하지 않고 패치 단위로 진위여부를 판별하는 모델

### 성능 평가

![image](https://user-images.githubusercontent.com/77203609/136342218-8fe65486-8f77-4fc3-bf0d-a3c90ffcf1e8.png)

- L1 + cGAN의 결과가 가장 자연스럽다.

![image](https://user-images.githubusercontent.com/77203609/136342384-84915129-8ad0-49b6-aff3-ec5aaf505f22.png)

- skip connecttion을 사용했을 때와 사용하지 않았을 때의 비교
- FCN-scores

