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
 
 ![image](https://user-images.githubusercontent.com/77203609/131079544-ed38c7f6-412e-4f65-b74b-67d76f6926ee.png)
 
 - 5행 23열
 - dataset.info()

![image](https://user-images.githubusercontent.com/77203609/131078892-97e406bc-ddc7-4cae-a4bd-f5b3131d28e8.png)

- Dtype이 object형 7개 float형 16개
- dataset.describe()

![image](https://user-images.githubusercontent.com/77203609/131079656-00dafe8b-2fd0-48eb-82ce-127722077db7.png)

![image](https://user-images.githubusercontent.com/77203609/131079701-ea3a5dbf-23ea-42af-bc3c-97b2dced7fdd.png)

- float형들의 통계

<br>

## 학습에 사용될 수 있도록 데이터 셋 조정

- dataset["RainTomorrow"].replace({"Yes" : "1", "No" : "0"}, inplace = True) 
1. #inplace=True 로 설정하면 원본이 변환된다
- dataset.dropna(subset = ["RainTomorrow"], inplace = True) 
1. #RainTomorrow열에 NaN data가 있는 경우 삭제 
- dataset = dataset.astype({"RainTomorrow": int}) 
1. #RainTomorrow열을 숫자로 변환
- dataset["RainTomorrow"]

![image](https://user-images.githubusercontent.com/77203609/131077545-3ea49f11-96f2-4ecb-8d2c-a366b8219d05.png)

- 숫자 데이터들의 상관관계 파악을 위한 시각화 플롯
- plt.style.use("ggplot")
- f,ax=plt.subplots(figsize = (10,8))
- sns.heatmap(dataset.corr(), robust=True, fmt='.2f',linewidths=1.3, linecolor = 'gold', annot=True,); 
1. corr(): 상관계수 구하는 함수
2. 구해진 상관계수

![image](https://user-images.githubusercontent.com/77203609/131081653-eba15052-a3c3-4693-84df-2d8202b97649.png)

- 시각화 플롯

![image](https://user-images.githubusercontent.com/77203609/131081748-aa83397e-3119-43a8-bb0d-b191fa25a6cf.png)

- MinTemp, MaxTemp 말고는 다른 변수들과의 상관관계가 별로 없는 Temp9am, Temp3pm 열 삭제
- dataset = dataset.drop(["Temp9am"], axis = 1)
- dataset = dataset.drop(["Temp3pm"], axis = 1)

### 컬럼을 카테고리별로 분류
- num_col = dataset.select_dtypes(include='float64').columns 
- obj_col = dataset.select_dtypes(include='object').columns

### 날짜 분류

- 원래 형태

![image](https://user-images.githubusercontent.com/77203609/131085969-9b9f45ba-eb85-486d-8af6-c3075a546c27.png)

- 년, 월, 일로 분리
- dataset['Date'] = pd.to_datetime(dataset['Date'])
- dataset['Year'] = dataset['Date'].dt.year
- dataset['Month'] = dataset['Date'].dt.month
- dataset['Day'] = dataset['Date'].dt.day
- dataset.drop('Date',axis=1,inplace=True)
- print(dataset['Year'], dataset['Month'], dataset['Day'], sep='\n')

![image](https://user-images.githubusercontent.com/77203609/131086243-b1fdea17-2276-4692-884f-9803b149126a.png)

### 결측치 보완

- dataset[num_col].isnull().sum() #결측지 갯수 확인

![image](https://user-images.githubusercontent.com/77203609/131086891-4beb9fe1-a982-4963-bd23-547ca0f6d7d4.png)

- for i in num_col:
-   dataset[i] = dataset[i].interpolate() #보간법으로 결측치 보완
- 주어진 데이터를 이용하여 비어있는 부분을 채우는 방법

![image](https://user-images.githubusercontent.com/77203609/131087159-556c0185-f343-44d9-81d2-0f6e7a2383c7.png)

- Evaporation, Sunshine, Cloud3pm은 fillna()를 사용하여 보완
- dataset['Evaporation'] = dataset['Evaporation'].fillna(method='bfill')  
- dataset['Sunshine'] = dataset['Sunshine'].fillna(method='bfill')
- dataset['Cloud3pm'] = dataset['Cloud3pm'].fillna(method='bfill')
- dataset[num_col].isnull().sum()
1. 앞 데이터를 사용하는 ffill method로는 결측지 제거가 안되고? bfill을 사용해야 제거된다.

![image](https://user-images.githubusercontent.com/77203609/131087505-624af6e1-d433-4848-b83a-e01deb236436.png)

- 오브젝트 카테고리의 데이터에도 결측치가 있지만 바람의 방향이나 비가 온다, 안 온다 등의 카테고리가 있어서 보간법으로의 결측치 보완이 어렵다.

### num_col. obj_col 병합

- dummy_col = obj_col
- dataset = pd.get_dummies(dataset, columns = dummy_col)
- dataset.columns

![image](https://user-images.githubusercontent.com/77203609/131088266-d0e47960-561a-400a-ad55-5eca98f5b7ae.png)

### 평가를 위한 데이터 분리

- X = dataset.drop('RainTomorrow', axis=1)
- Y = dataset['RainTomorrow']
- X = StandardScaler().fit_transform(X) #정규화


