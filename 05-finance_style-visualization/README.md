# [DACON] KCB ������Ÿ�� �ð�ȭ ������ȸ

[�������_������_�м�(4����)](https://dacon.io/competitions/official/82407/codeshare/726?page=1&dtype=recent&ptype=pub) �ڵ� �м�

�ش� ���� KCB�� �����ϴ� 1��° �����ͼ��� *credit_card_data.csv* �� ���ؼ��� �ð�ȭ�� ����

---

## �м� �� �ð�ȭ ����

�Ʒ����� �м� ������ ���������� ������ ����� �Բ� ������ ���Դϴ�.

---

### [1] ������ ��ó��

���� ������ ó�� - ( city : '����' / sex : '��ü' )

### [2] ������� �м�

- **��**�� ������谡 Ŭ���� **���
��**�� ������谡 Ŭ���� **�ݺ��
0**�� �������� **���� �������**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b29d7d42-0159-41b5-9b80-b9a315c8067c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b29d7d42-0159-41b5-9b80-b9a315c8067c/Untitled.png)

    - **��**�� ������谡 ���� 5�� ( ���� **���** ���� )

        monthly_loan         monthly_lc            0.986415
        credit_card_payment  monthly_card_spend    0.985854
        mortgage_loan        monthly_loan          0.985699
        num_usecard          num_opencard          0.983744
        monthly_loan         monthly_bk_loan       0.980280

    - **��**�� ������谡 ���� 5�� ( ���� **�ݺ��** ���� )

        ages              num_usecard        -0.679487
        num_opencard      ages               -0.688015
        avg_score         monthly_sbk_loan   -0.701963
        avg_rat           avg_score          -0.786341
        monthly_sbk_loan  ages               -0.841758

- **���ɺ� ������� �м�**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/033b336d-1091-442f-b054-a283035b36c5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/033b336d-1091-442f-b054-a283035b36c5/Untitled.png)

- **������ ������� �м�**

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68ae02e7-45b0-4e5f-a372-5fcde90c43e4/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68ae02e7-45b0-4e5f-a372-5fcde90c43e4/Untitled.png)

���ɴ뺰�� feature�� ���� �ٸ� ��, �������δ� �� feature�� ����� ���� ������ ����. 
��, **�ſ�����**���� �������� **������ ���󼭴� ������ ũ�� ������**, **���ɿ� ���󼭴� ������ ũ�ٴ� ��**�� �� �� �ִ�.

### [3] �н� : �ſ����� ������ �н�

�ſ������� �����ϴ� ���� �н��Ͽ� �ſ뵵�� ������ ���� �ִ� ��Ҹ� �м��Ѵ�.

- target ( ���� ��ǥ ) : 'avg_score' ( �ſ� ���� )
- �н��� ���Ǵ� ���: 'num_opencard', 'num_usecard', 'monthly_card_spend', 'monthly_lc', 'monthly_loan', 'monthly_bk_loan', 'monthly_cd_loan', 'monthly_installments_loan', 'monthly_insurance_loan', 'monthly_sbk_loan', 'loan_commitment', 'inst_rep_loanb', 'ls_rep_loanb', 'credit_loan', 'mortgage_loan', 'credit_card_payment', 'credit_card_installments_payment'
- �н����� ���ܵ� ���: 'pop_cd', 'city', 'ages', 'sex', 'year_month', 'population', 'avg_rat'

**���� �߿䵵 �м�**

Random Forest�� ���� �ֿ� **Ʈ�� ��� �ӻ�� ��**�� �̿��� ��, �� ��ü���� **Feature Importance �Ӽ�**�� �����ؼ� Ư���� ���� ���̵� �߿��� �������� �Ѵ��� �� �� �ִ�.

- ***Feature Importance***: �ش� ��Ұ� �����͸� **�н� ������ �°� �󸶳� �� ��������**
- ***Permutation Importance***: �ش� ����� ���� �ٸ� ���ð� ���Ƿ� �ٲپ��� �� ���� ������ �����ϴ� ����. ��, Ư�� feature�� �� ���� ��, �̰��� ���� �սǿ� �󸶸�ŭ�� ������ �ִ����� ���� �� feature�� �߿䵵�� �ľ��ϴ� ���
1. lightGBM

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e424055-da40-4a40-8fd0-fc654b2f20e3/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e424055-da40-4a40-8fd0-fc654b2f20e3/Untitled.png)

2. XGBRegressor

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9702ffd2-8235-4a5c-81bf-7805f6617ce9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9702ffd2-8235-4a5c-81bf-7805f6617ce9/Untitled.png)

### [4] �м� : SHAP �߿䵵 �м�

���� �߿䵵�δ� **������ �󸶳� ������ �ִ�����** �� �� ���� ��, **�ſ뵵�� ���̰ų� ���ߴ� ������ � ������ �ִ��� �� �� ����**.

**SHAP**�� ����� ������ ���� ����/���� �� �ſ뵵�� �ö󰡴��� ������������ ���δ�.

1. lightGBM

    �ϴ� �׸��� lightBGM �𵨿� ���� SHAP value �ؼ�

    - num_usecard : �������� ��������, �ſ� ����� ���� ����

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35173d22-5fd3-49a5-9409-34fa6ad8cc21/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35173d22-5fd3-49a5-9409-34fa6ad8cc21/Untitled.png)

2. XGBRegressor

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10105552-35f8-4f55-b6ab-ae7982b71941/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10105552-35f8-4f55-b6ab-ae7982b71941/Untitled.png)