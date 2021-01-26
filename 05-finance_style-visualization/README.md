# [DACON] KCB 금융스타일 시각화 경진대회

[상관관계_예측모델_분석(4등팀)](https://dacon.io/competitions/official/82407/codeshare/726?page=1&dtype=recent&ptype=pub) 코드 분석

해당 팀은 KCB가 제공하는 1번째 데이터셋인 *credit_card_data.csv* 에 대해서만 시각화를 진행

---

## 분석 및 시각화 과정

아래에는 분석 과정을 순차적으로 간단한 설명과 함께 정리한 것입니다.

---

### [1] 데이터 전처리

결측 데이터 처리 - ( city : '전국' / sex : '전체' )

### [2] 상관관계 분석

- **양**의 상관관계가 클수록 **비례
음**의 상관관계가 클수록 **반비례
0**에 가까울수록 **낮은 상관관계**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b29d7d42-0159-41b5-9b80-b9a315c8067c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b29d7d42-0159-41b5-9b80-b9a315c8067c/Untitled.png)

    - **양**의 상관관계가 높은 5쌍 ( 서로 **비례** 관계 )

        monthly_loan         monthly_lc            0.986415
        credit_card_payment  monthly_card_spend    0.985854
        mortgage_loan        monthly_loan          0.985699
        num_usecard          num_opencard          0.983744
        monthly_loan         monthly_bk_loan       0.980280

    - **음**의 상관관계가 높은 5쌍 ( 서로 **반비례** 관계 )

        ages              num_usecard        -0.679487
        num_opencard      ages               -0.688015
        avg_score         monthly_sbk_loan   -0.701963
        avg_rat           avg_score          -0.786341
        monthly_sbk_loan  ages               -0.841758

- **연령별 상관관계 분석**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/033b336d-1091-442f-b054-a283035b36c5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/033b336d-1091-442f-b054-a283035b36c5/Untitled.png)

- **지역별 상관관계 분석**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68ae02e7-45b0-4e5f-a372-5fcde90c43e4/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68ae02e7-45b0-4e5f-a372-5fcde90c43e4/Untitled.png)

연령대별로 feature는 서로 다른 색, 지역별로는 각 feature는 비슷한 색을 가지는 경향. 
즉, **신용점수**와의 상관관계는 **지역에 따라서는 편차가 크지 않지만**, **연령에 따라서는 편차가 크다는 것**을 알 수 있다.

### [3] 학습 : 신용점수 예측모델 학습

신용점수를 예측하는 모델을 학습하여 신용도에 영향을 많이 주는 요소를 분석한다.

- target ( 예측 목표 ) : 'avg_score' ( 신용 점수 )
- 학습에 사용되는 요소: 'num_opencard', 'num_usecard', 'monthly_card_spend', 'monthly_lc', 'monthly_loan', 'monthly_bk_loan', 'monthly_cd_loan', 'monthly_installments_loan', 'monthly_insurance_loan', 'monthly_sbk_loan', 'loan_commitment', 'inst_rep_loanb', 'ls_rep_loanb', 'credit_loan', 'mortgage_loan', 'credit_card_payment', 'credit_card_installments_payment'
- 학습에서 제외된 요소: 'pop_cd', 'city', 'ages', 'sex', 'year_month', 'population', 'avg_rat'

**변수 중요도 분석**

Random Forest와 같은 주요 **트리 기반 앙상블 모델**을 이용할 때, 모델 자체에는 **Feature Importance 속성**이 존재해서 특별한 과정 없이도 중요한 변수들을 한눈에 볼 수 있다.

- ***Feature Importance***: 해당 요소가 데이터를 **학습 목적에 맞게 얼마나 잘 나누는지**
- ***Permutation Importance***: 해당 요소의 값만 다른 샘플과 임의로 바꾸었을 때 예측 성능이 감소하는 정도. 즉, 특정 feature를 안 썼을 때, 이것이 성능 손실에 얼마만큼의 영향을 주는지를 통해 그 feature의 중요도를 파악하는 방법
1. lightGBM

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e424055-da40-4a40-8fd0-fc654b2f20e3/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e424055-da40-4a40-8fd0-fc654b2f20e3/Untitled.png)

2. XGBRegressor

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9702ffd2-8235-4a5c-81bf-7805f6617ce9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9702ffd2-8235-4a5c-81bf-7805f6617ce9/Untitled.png)

### [4] 분석 : SHAP 중요도 분석

위의 중요도로는 **변수가 얼마나 영향을 주는지만** 알 수 있을 뿐, **신용도를 높이거나 낮추는 데에는 어떤 영향이 있는지 알 수 없다**.

**SHAP**를 사용해 변수의 값이 높을/낮을 때 신용도가 올라가는지 내려가는지를 보인다.

1. lightGBM

    하단 그림인 lightBGM 모델에 대한 SHAP value 해석

    - num_usecard : 변수값이 낮을수록, 신용 등급이 높은 경향

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35173d22-5fd3-49a5-9409-34fa6ad8cc21/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35173d22-5fd3-49a5-9409-34fa6ad8cc21/Untitled.png)

2. XGBRegressor

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10105552-35f8-4f55-b6ab-ae7982b71941/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10105552-35f8-4f55-b6ab-ae7982b71941/Untitled.png)