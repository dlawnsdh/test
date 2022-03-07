# StyleGAN2(Analyzing and Improving the Image Quality of StyleGAN)

## Introduction

- 기존 StyleGAN의 문제점을 분석하여 모델의 구조와 학습방법을 변경
- 생성자의 정규화방식의 변경
- progressive growing방식의 변경
- 이미지 품질을 PPL을 줄이고 잠재 공간을 매끄럽게 하여 개선

## StyleGAN의 평가방법

### Frechet Inception Distance(FID)

- 생성된 이미지의 분포가 원본 이미지의 분포에 얼마나 가까운지 측정하는 방법
- 저차원 공간에 이미지를 embedded하고 그 공간에서 분포의 거리를 측정

![image](https://user-images.githubusercontent.com/77203609/156970731-e56ac14e-c782-41d8-9c98-3fbd21c25ddd.png)
 FID의 정의 

### Perceptual Path Length(PPL)

- 이미지가 지각적으로 부드럽게 바뀌었는지 나타내는 지표
- embedded된 이미지의 거리를 이용하여 seed of fake images의 잠재 공간이 가장 짧은 지각적 경로로 바뀌는지 가리킨다

![image](https://user-images.githubusercontent.com/77203609/156971494-7c6ce20a-8959-4571-965c-da0a536b5956.png)
PPL 공식

![image](https://user-images.githubusercontent.com/77203609/156971865-e2e62a92-6d07-435e-bc9f-c1de55e2bfb6.png)
PPL 예시

## StyleGAN

### Progressive Growing

- PG-GAN에서 제안된 방법으로 저해상도의 이미지부터 시작해 점진적으로 고해상도 생성자와 판별자를 추가해 고해상도 이미지를 만들어낸다

![image](https://user-images.githubusercontent.com/77203609/156973235-b9c80451-1642-45f3-a1e7-d525aacc92e7.png)

- 4x4를 시작으로 1024x1024이미지를 생성해내기까지 점진적으로 네트워크를 붙여나가며 학습한다

### AdaIN layer
