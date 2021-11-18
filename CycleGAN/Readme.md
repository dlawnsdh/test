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

## Formulation

### Adversarial Loss

![image](https://user-images.githubusercontent.com/77203609/142380977-88512406-c4af-4479-adc6-65ff53e4d61a.png)

- G : 타겟 도메인 Y에 가까운 이미지 G(x)를 생성하는 Generator
- Dy: 생성된 이미지 G(x)와 실제의 샘플 y를 식별하는 Discriminator
  (Dx에 대해서도 마찬가지)
<br>
  
### Cycle Consistency Loss

![image](https://user-images.githubusercontent.com/77203609/142381027-9241c6b5-3008-4b63-8742-2b9f9966d029.png)

- F(G(x)), G(F(y))(변환과 역변환)을 거친 이미지가 원본과 최대한 유사할 수 있도록 학습을 진행한다.
<br>

### Full Objective

![image](https://user-images.githubusercontent.com/77203609/142381083-b70102e4-2fb7-4e65-a29f-b986d06e7297.png)

- adversarial loss: target domain에 있을법한 이미지 생성한다.
- cycle consistency loss: 입력과 매칭되는 translation결과를 찾는다.
<br>

### architectures

- 잔여 블록(residual block)을 활용하는 아키텍쳐 및 instance normalization을 사용한다.
- 이미지를 패치 단위로 진위 여부를 판별하는 discriminator를 사용한다.

### Training details

- Least-squares loss: cross-entropy 기반의 loss 대신 MSE 기반의 loss 사용
- Replay buffer: 생성자(G)가 만든 이전의 50개의 데이터를 저장해 두고, 이를 이용해 판별자(D)를 학습시킨다.
<br>

## Evaluation

![image](https://user-images.githubusercontent.com/77203609/142386808-afdf8ef3-6e88-4194-a8c1-c84be61d69ce.png)
![image](https://user-images.githubusercontent.com/77203609/142386937-71890a4b-3efc-4b48-bf70-303f4060906f.png)

- paired dataset으로 학습을 진행하는 pix2pix와 비교할 만한 성능을 보인다.

- 색상 정보를 유지해야 할 때는 추가적으로 Identity Loss를 사용한다. 

![image](https://user-images.githubusercontent.com/77203609/142387861-655c7997-9e03-4d52-b954-9135bba4572e.png)

![image](https://user-images.githubusercontent.com/77203609/142387951-f7710f57-dce5-4c5d-9cec-053f84f4f54f.png)
(Identity Loss를 함께 사용했을 때)
