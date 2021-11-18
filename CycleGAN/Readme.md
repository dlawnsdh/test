# CycleGAN(Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks)

## Abstract

- pix2pix는 paired data가 있어야 한다는 한계점이 존재
- unpaired data로 image-to-image translation기법을 제시

## Introduction

![image](https://user-images.githubusercontent.com/77203609/142378036-7a6adee6-653b-4eb8-9b08-201b78118cc1.png)

- pix2pix(paired)와 cycleGAN(unpaired)의 차이점
- paired data가 없는 상황에서 두 도메인 간의 translation 학습이 가능하도록 관계성을 찾는다.
- adversarial loss term과 cycle consistency loss term을 결합하여 사용한다.
<br>

## Model

- conditional GAN을 활용
![image](https://user-images.githubusercontent.com/77203609/142379585-c0f1f3fe-5725-468c-886b-8e4ba70c5e41.png)

- 프레임워크
- adversarial loss term만으로는 X -> Y로의 유의미한 매핑이 보장되지 않기에 cycle consistent

![image](https://user-images.githubusercontent.com/77203609/142379927-c2718a94-3e76-4891-859e-625f7c8c7c9e.png)

(한 도메인에서 다른 도메인으로 변환 후 다시 역변환을 하면 원래의 도메인으로 돌아와랴 함)을 만족시키도록
- cycle consistency loss term을 함께 사용
