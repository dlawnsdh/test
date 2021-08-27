## 주제 및 목표

- kaggle의 데이터 셋을 활용하여 분류기 학습
- Rain in Australia 라는 데이터 셋 활용
- 비가 오나 안오나(yes or no)의 두 가지 상황으로 분류
- 머신러닝 라이브러리인 sklearn 활용
- 데이터의 20%를 평가 데이터로 삼아 실제 데이터와의 정확도를 지표로 삼는다.

## Rain in Australia

-  RainTomorrow 라는 target variable을 설정하여 다음날 비가 올 것 인지를 예측
-  이 데이터 셋은 10년간의 호주 전역의 날씨 데이터를 가지고 있다.

## Rain in Australia 데이터 셋

- 데이터 셋의 구성
- dataset = pd.read_csv('/content/drive/MyDrive/weatherAUS.csv')
- dataset.head()
 
 ![image](https://user-images.githubusercontent.com/77203609/131076170-7ed1b501-23ef-45f3-ade9-1429a67c6bbe.png)
 
 ![image](https://user-images.githubusercontent.com/77203609/131076275-0db4838c-866c-4d22-980a-7bacec56f455.png)
 
### 학습에 사용될 수 있도록 데이터 셋 조정

- dataset["RainTomorrow"].replace({"Yes" : "1", "No" : "0"}, inplace = True) #inplace=True 로 설정하면 원본이 변환된다
- dataset.dropna(subset = ["RainTomorrow"], inplace = True) #RainTomorrow열에 NaN data 있는 경우 삭제 
- dataset = dataset.astype({"RainTomorrow": int}) #RainTomorrow열을 숫자로 변환
<br>
- 
