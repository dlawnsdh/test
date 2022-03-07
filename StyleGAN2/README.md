# StyleGAN2(Analyzing and Improving the Image Quality of StyleGAN)

## Introduction

- 기존 StyleGAN의 문제점을 분석하여 모델의 구조와 학습방법을 변경
- 생성자의 정규화방식의 변경
- progressive growing방식의 변경
- 이미지 품질을 PPL을 줄이고 잠재 공간을 매끄럽게 하여 개선

## Frechet Inception Distance(FID)

- 생성된 이미지의 분포가 원본 이미지의 분포에 얼마나 가까운지 측정하는 방법

![image](https://user-images.githubusercontent.com/77203609/156970731-e56ac14e-c782-41d8-9c98-3fbd21c25ddd.png)

- FID의 
