---
category: MLops
tags: [AI/ML]
usemath: [latex, ascii] 
---



# Common Feature Engineering Operations

In this section, we will discuss several of the most important operations that you might want to consider while engineering features from your data.

1. Handling missing values
2. Scaling
3. Discretization
4. Encoding Categorical Features



## Handling Missing Values

### 결측치의 세 가지 종류

![image-20231226154224730](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-26-Feature_Engineering/image-20231226154224730.png)

- Missing not at random (MNAR)
  - 결측값이 해당 데이터의 실제 값과 관련이 있을 때 발생한다. 예를 들어, 높은 소득을 가진 사람들이 소득 정보를 공개하지 않는 경우, 이는 '높은 소득'이라는 데이터의 특성 때문에 결측값이 생긴 것이다.
- Missing at random (MAR)
  - 결측값이 다른 관찰된 변수와 관련이 있지만, 결측 데이터 자체와는 직접적인 관련이 없을 때이다. 예를 들어, 특정 성별의 응답자가 자주 나이를 기입하지 않는 경우, 이는 '나이' 데이터 자체보다는 '성별'과 관련된 결측이다.
- Missiong completely at random (MCAR)
  - 결측값에 아무런 패턴이나 특정한 이유가 없을 때이다. 예를 들면, 응답자가 아무런 이유 없이 '직업'을 기입하지 않는 경우, 이는 직업이나 다른 변수와 관련없이 완전히 무작위로 결측된 것이다.



### 결측치를 처리하는 방법 2가지

1. Deletion

   - 작업이 쉽기 때문에 많은 사람들이 애용하고 있는 방법이다.

   - Column deletion:
     - 중요한 정보들을 삭제하고, 모델의 정확도를 감소시킬 수 있다는 단점을 가지고 있다.
   - Row deletion:
     - 예측을 할 때 필요한 중요한 데이터를 삭제시킬 수 있고 이로 인해 모델에 bias를 유발할 수도 있다. (특히 MAR에서)

​	

2. Imputation

   - 결측치에 어떤 값을 채울지를 정하는 것은 상당히 어려운 문제이다.
   - 대부분 결측치의 값을 그대로 두거나 mean, median, 또는 최빈값을 이용해 결측치를 채운다.

   - 값을 잘못 설정하게 되면 model의 학습을 저하시킬 수 있다.

---



## Scaling

20~40 사이에 분포되어 있는 나이 데이터와 10,000 ~ 15,000 사이에 분포되어 있는 연봉 데이터를 동시에 입력하게 되면 모델은 연봉 데이터와 나이 데이터를 구분할 수가 없다. 연봉이 대체적으로 나이 데이터보다 크므로, 연봉 데이터에 더 우선순위를 두어 모델은 작업을 수행하게 된다. 이와 같은 에러는 모델의 gibberish prediction을 유발할 수 있다. 



Scaling의 가장 대표적인 방법은 [0, 1] 사이로 scaling을 하는 것이다. 아래와 같이 scaling을 수행할 수 있다.

$$ x' = {{x - min(x)} \over  {max(x) - min(x)}} $$

임의의 숫자 범위 [a, b]로 scaling을 원한다면 아래와 같은 식을 사용하면 된다.

$$ x' = a + {{(x - min(x))(b-a)} \over {max(x) - min(x)}} $$

또한, 변수들이 normal distribution을 따른다면, 아래와 같은 식을 통해 *standardization*을 수행할 수 있다.

$$ x' = {{x - \bar{x}} \over \sigma}  $$



- Log scaling
  - 실제 production 환경에서 모델이 편향된 분포에 의해 어려움을 겪는 상황은 빈번하다. 이를 완화시키기 위해 log transformation 방법을 사용한다.
  - Log scaling은 많은 상황에서 성능 향상을 가져올 수 있지만, 모든 경우에 적용되지는 않는다. 



Scaling을 할 때엔 다음 두 가지를 주의해야 한다.

1. Scaling은 data leakage의 주된 원인이다.
2. 종종 global statistics(전체 데이터셋을 봐야 구할 수 있는 parameter)가 필요하기도 하다.

---



## Discretization

책의 저자는 책의 완성도를 위해 Discretization을 추가했고, discretization은 실제 production 환경에서 큰 도움이 되지 않는다고 한다.



연봉을 예로 들면 다음과 같이 3개의 카테고리로 나눌 수 있다.

- 낮은 연봉: > $35,000/year
- 중간 연봉: $35,000 > and <\$100,000

- 높은 연봉:  > $100,000 



히스토그램을 이용해 데이터를 나타낸 후 각 bucket의 경계값을 설정할 수 있다.

---



## Encoding Categorical Features

많은 사람들이 category가 변하지 않을 것 같다 예측한다. 실제로 나이 bracket이나 수입 bracket은 거의 변하지 않는다. 하지만 추천시스템과 같은 프로그램을 사용할 때에는 category가 변하게 된다. Amazon을 예로 들어보면 브랜드가 계속 생겨나기 때문에 category는 시시각각 변하기 마련이다.

이를 해결하기 위해 *hashing trick*을 사용한다. 카테고리 데이터를 일정한 크기의 해시된 값으로 변환하여 모델이 이를 다루기 쉽게 만드는 기술이다. 이렇게 하면 카테고리의 개수에 따라 동적으로 인코딩된 값의 수를 조절할 수 있으며, 모델이 새로운 카테고리를 처리하는데 유연성을 제공하게 된다.

하지만 같은 hash 인덱스에 두 개의 카테고리 변수가 들어가는 *collision*이 발생한다는 단점이 있다.

---



## Feature Crossing

두 개 혹은 그 이상의 feature들을 조합하여 새로운 feature를 생성하는 방법이다. 이 방법은 각 feature 간의 비선형적 relationship을 형성할 때 유용하다. 또한 학습이 잘 되지 않거나, nonlinear relationship을 잘 학습하지 못하는 linear regression, logistic regression, 그리고 tree-based model들에게 매우 유용하다.

하지만, Feature Crossing을 선택하게 되면, feature를 저장하는 공간이 부족해질 수 있다는 단점이 존재한다.

---



## Data Leakage

Data leakage는 모델을 만들 때, 모델이 실제로 알지 못해야 하는 정보를 사용하는 문제를 말한다. 이 정보는 모델을 훈련하는 동안에는 사용할 수 있지만, 모델이 실제 예측을 수행할 때는 사용할 수 없다.

예를 들어, 학생들의 수학 시험 성적을 예측하는 모델을 만든다고 가정하자. 모델을 훈련시키기 위해 과거의 시험 데이터를 사용한다. 이 데이터에는 학생의 성적과 함께 학생이 시험 전에 얼마나 열심히 공부했는지를 나타내는 정보도 포함되어 있다.

하지만 문제는 모델이 이 정보를 학습하여 예측에 사용한다면 데이터 누출이 발생할 수 있다. 실제 시험을 볼 때 학생들이 얼마나 열심히 공부했는지는 시험 당일에는 알 수 없는 정보이기 때문이다. 따라서 이 모델이 이 정보를 사용하면, 모델이 실제 시험 성적을 예측할 때는 사용할 수 없는 정보를 사용하게 되는 것이다.

Data Leakage를 피하려면 모델을 훈련할 때 사용되는 feature 중에 실제 예측에 사용할 수 있는 것만을 사용해야 한다.



### Common Causes for Data Leakage

- Splitting time-correlated data randomly instead of by time
  - 랜덤으로 data를 나누게 되면, 미래의 데이터가 현재의 학습에 영향을 미칠 수 있어 data leakage가 발생하게 된다.
  - 이를 방지하기 위해 시간대 별로 data를 나누어야 한다.



- Scaling before splitting

  - Scaling은 global statistics를 필요로 하게 된다.

  - Global statistics를 얻기 위해 전체 training data를 사용하게 되면 test sample의 평균과 분산을 유출할 수 있다.
  - Data leakagef를 줄이기 위해, scaling 전 데이터를 split 해야한다.



- Filling in missing data with statistics from the test split
  - 전체 데이터셋을 이용해 만든 mean이나 median 값등을 이용해 결측치를 채우게 되면, 마찬가지로 data leakage를 유발할 수 있다.
  - Training dataset으로만 구한 파라미터들을 이용해 결측치를 채우게 되면 data leakage를 방지할 수 있다.



- Poor handling of data duplication before splitting
  - 중복된 데이터가 분할되기 전에 제거되지 않으면, 학습 및 검증/테스트 데이터에서 동일한 샘플이 나타날 수 있음.
  - 데이터 중복은 산업 분야 및 연구 데이터셋에서 흔하게 발생함.
  - 예를 들어, CIFAR-10 및 CIFAR-100 데이터셋에서 중복 이미지가 발견됨

​	

​	데이터 중복의 원인:

 1.  데이터 수집 및 데이터 원본 병합 시 발생

 2.  데이터 처리 과정에서 오버샘플링과 같은 작업으로 중복 발생

     

     중복 방지를 위한 조치:

1. 데이터 분할 전후에 중복 여부 확인
2. 데이터 오버샘플링은 분할 이후에 수행



- Leakage from data generation process
  - 데이터 간에 강한 상관관계를 가지지만 다른 split으로 나뉠 수도 있음.
  - 예시: 환자가 한 주 간격으로 두 개의 폐 CT 스캔을 가질 수 있으며, 이 두 스캔은 폐암 징후를 나타내는 레이블이 유사할 수 있지만, 하나는 훈련 데이터 분할에, 다른 하나는 테스트 데이터 분할에 속함.
  - 주로 발생하는 분야: 동일한 객체의 사진을 밀리초 간격으로 찍은 객관적 탐지 작업에 일반적으로 발생하며, 일부 사진은 훈련 분할에, 다른 사진은 테스트 분할에 놓일 수 있음.
  - 방지 방법: 데이터가 생성된 방식을 이해하는 것이 중요함.

---

 

## Detecting Data Leakage

- 각 feature 또는 feature 집합의 predictive power를 target variable과 관련하여 측정해야 한다. Feature가 비정상적으로 높은 상관관계를 가질 경우, 이 feature가 어떻게 생성되었는지와 co-relation이 합리적인지 확인이 필요하다.
- Feature의 중요성을 측정하기 위해 ablation studies을 수행한다. Feature를 제거했을 때 모델의 성능이 크게 저하된다면, 해당 특성이 왜 중요한지 조사해야 한다. 이는 feature engineering에서 전문 지식이 유용하게 쓰이는 항목이다.
- 모델에 새로운 feature가 추가되는지 주의해야 한다. 새로운 feature가 추가되었을 때 모델의 성능이 크게 향상된다면, 해당 feature가 매우 유용하거나 레이블에 대한 leakage 정보를 포함할 가능성이 있을 수 있다.

-  Test split을 시행할 때 매우 주의해야 한다. Test split을 모델 평가 이외의 목적으로 사용할 때 미래의 정보를 누출할 수 있기 때문이다.

---



## Feature Importance

Feature Importance를 평가하는 방법은 다음과 같이 요약할 수 있다.

- 내장되어 있는 feature importance 함수 사용:
  - 전통적인 ML 알고리즘 중에는 feature importance를 제공하는 경우가 있다. 예를 들어, XGBoost와 같은 라이브러리는 내장된 feature importance 함수를 제공하며, 모델 학습 후에 간단하게 사용할 수 있다.
- Third party tool 사용
  - 모델에 독립적인 방법으로 feature importance를 측정하려면 SHAP와 같은 도구를 사용할 수 있다. SHAP는 모델의 예측에 기여하는 각 특성을 이해하고 설명하는데 도움을 준다.
- InterpretML 활용
  - InterpretML과 같은 오픈 소스 패키지를 사용하면 특성 중요성을 활용하여 모델의 작동 방식을 더 자세히 이해할 수 있다. 이 패키지는 모델의 예측을 해석하고 설명하는 데 도움을 준다.

- Feature Importance 측정 알고리즘 이해
  - Feature Importance 측정 알고리즘은 모델의 예측에 대한 영향력을 측정하는 방법을 나타낸다. 이 알고리즘들은 일반적으로 특성을 제거했을 때 모델의 성능 변화를 기반으로 특성의 중요성을 계산한다.
  - 중요한 feature는 모델의 예측을 설명하고 개선하는 데 핵심적이다.

---



## Feature Generalization

Feature generalization은 머신 러닝 모델이 보지 못한 데이터에 대해 올바른 예측을 하도록 하는 목표를 의미한다. 그러나 모든 특성이 동일하게 일반화되지는 않는다. 

Feature generalization을 측정하는 것은 feature importance를 측정하는 것보다 통계적 지식 외에 직관과 관련 주제 전문 지식이 필요하다. 특히 generalization에서는 두 가지 측면을 고려해야 한다. Feature의 커버리지와 feature 값의 분포이다.

- Coverage: 
  - 특정 feature가 데이터 집합 내에서 얼마나 자주 나타나는지를 나타내는 지표이다. 커버리지가 높을 수록 해당 특성은 데이터 내에서 일반화하기가 쉽고 유용한 특성이며, 커버리지가 낮을수록 특성을 활용하기 어려울 수 있다.
- Feature 값 분포
  - Train 데이터와 Test 데이터에서 feature의 값이 겹치지 않으면, 해당 feature는 모델의 성능을 저하시킬 수 있다. 예를 들어 월요일부터 토요일까지의 택시 탑승 데이터를 훈련 데이터로 사용하고, 테스트 데이터를 일요일로 사용한다면 모델의 성능이 저하된다.

따라서 Feature generalization을 고려할 때 Coverage와 Feature 값의 분포를 잘 살펴봐야 한다.