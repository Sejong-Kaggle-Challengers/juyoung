# [DACON] 퇴근시간 버스승차인원 예측 경진대회
### ① 직접 구현한 [코드](06-jeju_bus/06_jeju_bus.ipynb)

📍 중요 포인트
* 날짜 데이터를 통해 주말/평일 정보 추가하기
* 승·하차 시간대를 예측해야하는 시간 단위에 맞춰 2시간 단위로 결합

<br>

---

### ② DACON 예측 경진 대회 2등 [Dining 팀](https://dacon.io/competitions/official/229255/codeshare/594?page=1&dtype=recent&ptype=pub) 코드 분석

<br>

📍 중요 포인트
* 날짜 데이터 day, week, weekday로 세분화
* 승·하차 시간대를 다양하게 조합 → *important feature*
* 특히 *day* 의 경우 다른 유의미한 feature와 *mean* 값으로 조합
* *frequency*(빈도수)에 대한 feature 추가

<br>

## [1] 데이터 전처리

2등 팀은 기존에 있던 feature들을 조합하여 새로운 feature를 만드는 "Data Pre-Processing"을 진행

| ![train](https://user-images.githubusercontent.com/59993108/106462459-747d9480-64d9-11eb-9cb3-b361d809f8cc.PNG) | 
|:--:| 
| *original train data* (415423 × 21)|

1. base_preprocessing
   * bus_route_id : 뒷자리 0000 제거 후 정수형 변환 (ex. 4270000 -> 427)
   * station_name2 : station_name의 앞 두 글자만 사용한 열 추가 (ex. 제주, 한라)
   * station_name : 공백 제거
   * station_name2 & station_name -> LabelEncoder
   * data -> day, week, weekday 열 추가하여 세분화
   * 6-8, 6-9, 6-10 : 각 시간대를 더하여 해당 시간대의 탑승·하차 인원 열 추가
2. lat_long_create
   * station_lat_long : 위도-경도 각각을 소수점 둘째자리에서 반올림하여 결합한 새로운 열
   * station_lat_long2 : 위도-경도 각각을 소수점 셋째자리에서 반올림하여 결합한 새로운 열
   * station_lat_long & station_lat_long2 -> LabelEncoder
3. feature_combine
   * bus_route_id_station_code : bus_route_id와 station_code 결합한 새로운 열
   * bus_route_id_station_lat_long : bus_route_id와 station_lat_long 결합한 새로운 열
4. day_agg
   * 각 시간대 별 탑승·하차와 day 간의 mean에 대한 열 추가 (ex. day_mean_6~7_ride)
    ```python
    ride_take = ['6~7_ride', '7~8_ride', '8~9_ride', '9~10_ride', '10~11_ride', '11~12_ride', '6~7_takeoff', '7~8_takeoff', '8~9_takeoff', '9~10_takeoff', '10~11_takeoff', '11~12_takeoff']

    ride_take += ['6~8_ride', '6~9_ride', '6~10_ride', '6~8_takeoff', '6~9_takeoff', '6~10_takeoff']

    train, test = day_agg(tr, te, merge_columns=['day'], columns=ride_take, aggs=['mean'])
    ```
5. sub_day_agg (ex. bus_route_id_day_6~7_ride_mean)
   * 각 시간대 별 탑승·하차와 (merge_column + day) 간의 mean에 대한 열 추가
   ```python
   train, test = sub_day_agg(tr, te, merge_columns=['bus_route_id', 'station_code', 'station_lat_long'], date_columns=['day'], columns=ride_take, aggs=['mean'])
   # bus_route_id_day : 427_1 과 6~7_ride 간의 mean
   #       bus_route_id_day    6~7_ride
   # 0          427_1            0.0
   # 1          427_1            1.0
   # 2          427_1            1.0
   ```   
6. frequency_encoding : 빈도수에 대해 encoding한 column 추가
   ```python
   category_features = ['bus_route_id', 'station_code', 'station_name', 'station_name2', 'station_lat_long', 'station_lat_long2', 'bus_route_id_station_code', 'bus_route_id_station_lat_long']
   ```  
   * 각 category_feature의끼리 빈도수에 대해 새로 mapping한 column의 이름을 ~_freq_enc로 추가


<br>

### 전처리 과정을 거치면 21 columns 에서 188 columns로 증가된 것을 확인할 수 있음

<Br>

---

## [2] 변수 선택

   * *remove_outlier()* : category_features에 결측데이터가 있다면 0으로 바꾸기
   * *category_transform()* : category_features에 해당하는 column을 category 형식으로 변환
   * *id*, *date*, *in_out*, *18~20_ride* : 행 제거
 
      ```python
      tr, te = remove_outlier(tr, te, category_features)
      tr, te = category_transform(tr, te, category_features)

      remove_features = ['id', 'date', 'in_out', TARGET]
      
      features_columns = [col for col in tr.columns if col not in remove_features]
      ```
<br> 

---

## [3] 모델 학습
*Light GBM* 을 통한 학습

Light GBM은 Gradient Boosting 프레임워크로 Tree 기반 학습 알고리즘.  

Parameter Setting [Document](https://lightgbm.readthedocs.io/en/latest/)
```python
lgb_params = {
        'objective':'regression',
        'boosting_type':'gbdt',
        'metric':'rmse',
        'n_jobs':-1,
        'learning_rate':0.003,
        'num_leaves': 700,
        'max_depth':-1,
        'min_child_weight':5,
        'colsample_bytree': 0.3,
        'subsample':0.7,
        'n_estimators':50000,
        'gamma':0,
        'reg_lambda':0.05,
        'reg_alpha':0.05,
        'verbose':-1,
        'seed': SEED,
        'early_stopping_rounds':50
    }
```
---

## [4] 성능 2.1609
