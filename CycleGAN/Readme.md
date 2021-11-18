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

- 프레임워크(X -> Y, Y -> X로 순환이 되어야 하므로 2개의 GAN을 사용한다.)
- adversarial loss term만으로는 X -> Y로의 유의미한 매핑이 보장되지 않기에 cycle consistent

![image](https://user-images.githubusercontent.com/77203609/142379927-c2718a94-3e76-4891-859e-625f7c8c7c9e.png)

(한 도메인에서 다른 도메인으로 변환 후 다시 역변환을 하면 원래의 도메인으로 돌아와야 함)을 만족시키도록
- cycle consistency loss term을 함께 사용한다.
<br>

### Adversarial Loss

![image](https://user-images.githubusercontent.com/77203609/142380977-88512406-c4af-4479-adc6-65ff53e4d61a.png)

- G : 타겟 도메인 Y에 가까운 이미지 G(x)를 생성하는 Generator
- Dy: 생성된 이미지 G(x)와 실제의 샘플 y를 식별하는 Discriminator
  (Dx에 대해서도 마찬가지)
  
### Cycle Consistency Loss

![image](https://user-images.githubusercontent.com/77203609/142381027-9241c6b5-3008-4b63-8742-2b9f9966d029.png)

- F(G(x)), G(F(y))(변환과 역변환)을 거친 이미지가 원본과 최대한 유사할 수 있도록 학습을 진행한다.

### Full Objective

![image](https://user-images.githubusercontent.com/77203609/142381083-b70102e4-2fb7-4e65-a29f-b986d06e7297.png)

- adversarial loss: target domain에 있을법한 이미지 생성한다.
- cycle consistency loss: 입력과 매칭되는 translation결과를 찾는다.
