# [DACON] 신용카드 거래 데이터 시각화

[TEAM-EDA 1등](https://dacon.io/competitions/official/42473/codeshare/427?page=1&dtype=recent&ptype=pub) 코드 분석

업로드 진행 중

> 본 코드 분석을 통해 Feature 간 분석 및 관계 파악이 용이해질 것

## 대회 설명

소상공인 신용카드 거래 내역 데이터를 활용하여 새로운 인사이트 시각화하기

- Data Instruction
    - store_id : 상점 고유 번호
    - date : 거래 일자
    - time : 거래 시간
    - card_id : 카드 번호의 hash
    - amount : 매출액. 음수는 취소된 매출을 의미
    - installments : 할부 개월수. 결측데이터는 일시불을 의미
    - days_of_week : 요일, 월요일(0) - 일요일(6)
    - holiday : 공휴일