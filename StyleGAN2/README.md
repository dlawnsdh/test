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

### AdaIN 

- style transfer의 정규화(normalization) 방법
- content input인 x와 style input인 y를 평균과 표준편차로 정규화 한다
- 오직 content와 style 이미지의 통계로만 수행되며 원하는 데이터로부터 style을 가져오기 때문에 학습 파라미터는 필요치않다

![image](https://user-images.githubusercontent.com/77203609/156984062-5e0fdf3e-9fed-4776-bc3a-9d4d00161e86.png)

- AdaIN 수식, style의 표준편차와 평균 대신 style 벡터 w의 선형변환인 y_s, y_b가 사용된다

### Network architecture

![image](https://user-images.githubusercontent.com/77203609/156984578-f1ebed7c-0388-488f-bee6-4669c510e0c0.png)

- 일반적인 GAN처럼 확률적으로 생성된 잠재 변수(z)를 통해 이미지를 생성하는 것이 아닌 고정된 값의 텐서(초기값이 상수)를 통해 이미지를 생성
- 잠재 변수인 z를 mapping network를 통해 선형변환을 한 w생성
- AdaIN layer를 각 layer에 2개씩 두어 더욱 다양한 스타일의 변화를 가능하게 한다
- random noise를 각 layer에 추가해 stochastic variation(확률적 다양성)을 만든다

### Mixing Regularization

- style에 사용된 두 잠재 변수를 학습하는 동안 혼합하는 것 
- 각각의 layer에서 사용된 w벡터를 혼합함으로서 여러 이미지를 만들수 있다

<br>

## StyleGAN2에서 보완한 StyleGAN의 문제점

### Droplet artifacts

![image](https://user-images.githubusercontent.com/77203609/156996095-eb515a19-b37f-4146-ba4c-271b1470c188.png)

![image](https://user-images.githubusercontent.com/77203609/156996125-33ce907d-74fa-4a48-aa1d-a4202b43bbda.png)

- 물방울 모양의 artifact가 feature map에 항상 존재하며 64x64부터 점점 강하게 나타난다 
- 모든 StyleGAN 이미지에 존재하였고, 만일 artifact가 생기지 않는다면 아예 잘못된 이미지가 생성
- 저자는 AdaIN 떄문에 artifact가 발생한다고 추정(각 feature map의 mean/std를 개별적으로 정규화하기 때문에 서로 연관된 feature들의 정보가 소실됨)
<br>

- 따라서 AdaIN 방식을 사용하지 않는 정규화를 제안

![image](https://user-images.githubusercontent.com/77203609/156997920-3bddf324-938d-4aeb-8b22-c3d87c630cec.png)

-디테일한 StyleGAN의 구조

![image](https://user-images.githubusercontent.com/77203609/156997953-b99e7d86-eaf0-4e59-8246-1b1162ba0f4a.png)최종 방식은 d

- b -> c
1. 저자들은 표준편차만으로도 충분하다고 평가했고 이에따라 평균값으로는 modulation을 진행하지 않았다
2. style block의 외부에서 feature map들의 값을 변경하기 위해 정규화를 진행하고 noise를 넣어주는 형태로 바꾸었다
- c -> d
1. convolution을 거친 feature map에 바로 modulation을 진행하지 않고 weight값에 대해 modulation을 진행한다
   따라서 직접적으로 정규화가아닌 feaure map의 statistics를 예측하는 추정 통계(estimated statistics)로 정규화를 진행한다

![image](https://user-images.githubusercontent.com/77203609/157001654-5afa276c-04c1-4a31-b2c6-2d14a64754bb.png)

2. weight값에 scailng fator를 곱해주어 modulation을 해주고 
3. 정규화까지 진행하기위해 모든 input activation이 unit std를 따르는 i.i.d random variable이라고 가정하면 

![image](https://user-images.githubusercontent.com/77203609/157001920-4c0b992a-1ce1-41b0-9bc4-1d8947c3720d.png)

- output activation의 std는 위와 같다

![image](https://user-images.githubusercontent.com/77203609/157002815-830448c0-c304-4ff6-917d-830c2814ddb3.png)

- 위의 식에서 demodulation으로 얻어지는 w''값으로 convolution을 하면 AdaIN을 대체하면서 droplet artifacts를 제거할 수 있다
 
### Phase artifacts  

![image](https://user-images.githubusercontent.com/77203609/157003883-619a1290-1637-425b-9bc0-78f5f962f9fc.png)

- 치아나 눈같은 요소들이 부드럽게 이동하지 않는 경우가 많다(고정되어 있으려는 성향이 강하다)
- Progressive Growing에 의해 발생하는 현상이므로 저자는 다른 방법을 사용하여 고해상도 이미지를 만드는 방법을 제시했다

![image](https://user-images.githubusercontent.com/77203609/157004795-66ec22e3-a76d-4ca6-b706-ee3e3a9e63e9.png)

- 모든 조합을 실험해본 결과 b타입 생성자와 c타입 판별자를 사용했을 때 PPL과 FID를 개선하여 이 조합을 선택했다 
