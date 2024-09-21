# **[배송기간예측을 통한 서비스개선 및 물류센터위치 최적화]**  
---**참여자: 김동윤, 박솔비, 신새봄**
> **목차(Context)**

* 문제상황 및 데이터 살펴보기
* 문제해결 프로세스 정의
* 🥉Session 1 - 「Data Info check & Data Merge」 : https://colab.research.google.com/drive/1DtUau8CROXtPVGkV34dsJ7QHjvw1KD0Z#scrollTo=2o25gJiIeWoT
* 🥈Session 2 - 「EDA」 : https://colab.research.google.com/drive/1DtUau8CROXtPVGkV34dsJ7QHjvw1KD0Z#scrollTo=RUALhbTOobow
* 🥇Session 3 - 「Modeling Process」: github upload

> **데이터출처**

* [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

## **기획의도 및 데이터 살펴보기**
---
> **주제/목표**

```
1. 배송 시간 예측을 통한 서비스 개선
2. 배송 서비스의 개선을 위한 물류센터 위치 제안
```  

> **주제선정이유**

```
현재 많은 이커머스 기업들이 글로벌시장진출에 초점을 맞추고 있다.

그 중 브라질은 중남미에서 가장 큰 이커머스 시장이고,
브라질 마켓은 중남미 이커머스 시장에서 약 30%에 가까운 점유율을 차지하고 있으며,
세게예서 일곱 번째로 많은 인구를 보유한 시장으로 무한한 가능성을 가지고 있다.
브라질의 이커머스 시장 수익 역시 2023년부터 2027년까지 꾸준히 증가할 것으로 예상된다.

하여 국내 이커머스 기업이 브라질시장 진출 시 유저에게 제공할 서비스 중 배송시간예측,
그리고 배송서비스 개선을 위한 물류센터위치를 제안하고자 한다.
```
- [참고링크](https://shopee.kr/blog/detail.php?seq=496)

  > **데이터 살펴보기**

<img width="558" alt="스크린샷 2024-03-29 150047" src="https://github.com/sxlbl/ML_Project/assets/154489441/e36cc774-106a-421f-b42f-bad103818339">


|Table|Description|Number of Columns|
|:---|:---|:---|
|olist_customers_dataset|고객과 고객의 위치에 대한 정보|5 columns|
|olist_geolocation_dataset|브라질 우편번호(판매자 및 고객) 및 해당 지역과 좌표 |5 columns|
|olist_order_items_dataset|각 주문 내에서 구매한 품목에 대한 정보 |7 columns|
|olist_order_payments_dataset|주문 결제 옵션에 대한 정보|5 columns|
|olist_order_reviews_dataset|고객이 작성한 리뷰에 대한 정보|7 columns|
|olist_orders_dataset|주문 정보 및 배송정보|8 columns|
|olist_products_dataset|Olist에서 판매하는 제품에 대한 정보|9 columns|
|olist_sellers_dataset|판매자와 판매자의 위치에 대한 정보|4 columns|
|product_category_name_translation|제품 카테고리 영어이름|2 columns|

---

* customers 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|customer_unique_id|고객unique_id|
|customer_id|다른데이터셋과 연결되는 unique_id |
|customer_zip_code_prefix|고객우편번호 |
|customer_city|고객이 사는 도시|
|customer_state|고객이 사는 주|

* sellers 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|seller_id|셀러 id|
|seller_zip_code_prefix|셀러우편번호 |
|seller_city|셀러가 사는 도시 |
|seller_state|셀러가 사는 주|

* geolocation 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|geolocation_zip_code_prefix|우편번호|
|geolocation_lat|우편번호에 따른 위도|
|geolocation_lng|우편번호에 따른 경도|
|geolocation_city|우편번호에 따른 시 이름|
|geolocation_state|우편번호에 따른 시 주 이름|

* order_items 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|order_id|주문id|
|order_item_id|동일 주문에 포함된 품목 수를 식별하는 순차 번호|
|product_id|제품id|
|seller_id|셀러 유니크 id|
|shipping_limit_date|배송제한날짜|
|price|상품가격|
|freight_value|운임비용|

* order_payments 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|order_id|주문의 고유 식별자|
|payment_sequencial|분할결제 시퀀스|
|payment_type|고객이 선택한 지불 방법.|
|payment_installments|고객이 선택한 할부 횟수.|
|payment_value|결제비용|

* order_reviews 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|review_id|고유한 review 식별자|
|order_id|고유한 주문 식별자|
|review_score|고객이 만족도 조사에서 제시한 1~5 범위|
|review_comment_title|고객이 남긴 리뷰의 코멘트 제목(포르투갈어)|
|review_comment_message|고객이 남긴 리뷰의 코멘트 메시지(포르투갈어)|
|review_creation_date|고객에게 만족도 조사를 보낸 날짜|
|review_anse_timestamp|만족도 조사 답변 타임스탬프|

* orders 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|order_id| 주문 unique 값|
|customer_id|주문에 대한 고객id|
|order_status|주문현황|
|order_purchase_timestamp|주문시간|
|order_approved_at|주문승인시간|
|order_delivered_carrier_date|배달시작일자|
|order_delivered_customer_date|배달완료일자|
|order_estimated_delivery_date|배달도착예측일자|

* category_name 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|product_category_name| 카테고리이름(포르투갈어) |
|product_category_name_english|카테고리 이름(영어)|

* products 테이블 데이터 명세 ⬇

|Column|Description|
|:---|:---|
|product_id| 상품id |
|product_category_name|카테고리이름(포르투갈어)|
|product_name_lenght|상품이름길이|
|product_description_lenght|상품설명 자릿수|
|product_photos_qty|상품사진 수|
|product_weight_g|상품무게g|
|product_length_cm|상품 길이 cm|
|product_height_cm|상품 높이 cm|
|product_width_cm|상품 가로 cm|


## **문제정의 및 기대효과**
---
> **문제정의**

```
▶ 현재 유저에게 제공되는 배송완료예측시간과 실제배송완료시간의 차이가 커서 서비스 개선이 필요함.
▶ 평균 배송일이 12일로 매우 긴편에 속함.
```  

> **기대효과**

```
▶ 정확한 배송예측시간제공을 통한 서비스 개선
▶ 물류센터위치 최적화로 배송기간단축
```
