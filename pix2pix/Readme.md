# Pix2Pix(Image-to-Image Translation with Conditional Adversarial Networks)

## Abstract

- conditional GAN을 활용한 간단한 image to image translation 방식 제안
- 서로 다른 loss function을 쓰던 이미지에 대한 문제들을 pix2pix를 이용하여 해결

## Introduction

- 이미지 변환 문제들에 대한 일반적인 framework의 필요성
- blurry image가 아닌 realistic한 이미지를 얻기위해 CGAN을 사용하여 진짜같은 이미지를 구현한다.

## GAN(Generative Adversarial Networks)

- Generative model(생성 모델), unsupervised learning
- 실존하진 않지만 있을 법한 이미지를 생성하는 모델
![image](https://user-images.githubusercontent.com/77203609/136320785-900ade9e-43a1-472a-8430-e01ce8b83797.png)
![image](https://user-images.githubusercontent.com/77203609/136320818-4c362067-b866-4d99-b0e7-1e46e349fc87.png)

- 통계적인 분포 자체를 학습하여 이미지를 만들어 낸다.
