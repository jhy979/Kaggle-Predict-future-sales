# Kaggle-Predict-future-sales
___
> 데이터 마이닝 수업을 듣고 5가지 과제를 진행했습니다. 제 DataMining 레포지터리에 관련 과제들을 전부 올려놨습니다. 이렇게 어려운 과제들을 했는데 그냥 묻히긴 너무 아깝다는 생각이 들었습니다. 
> 
> 과제를 해낸 자신감을 바탕으로 Kaggle 대회를 나가보면 좋을 것 같다는 생각이 들었고, 파이썬의 슬라이싱도 잘 모르는 2021.3부터 시작하여 2021.6.16 오늘 현재, Kaggle 대회 Predict Future Sales까지 나갔습니다. 
> 
> 운이 좋게도 리더보드에서 상위 8%정도의 성적을 받을 수 있었습니다. 아무리 모르는 분야더라도 일단 해보자는 생각이 드는 대회였습니다. 안 되는게 어디있어 하면 되는거지! 아래는 제가 어떤 방식으로 feature 가공을 진행했고 어떤 모델을 사용하여 학습을 진행했는지에 대한 내용을 담아봤습니다. 
> 
> 코드를 그냥 보셔도 주석을 달아놔서 어쩌면 관련 분들은 그걸 보시는게 더 편하실 것 같다는 생각도 듭니다.


## Data Mining
## Predict Future Sales (Kaggle)

### 1. 서론
___
### 1-1) Predict Future Sales Target 
___

과거 판매량 데이터를 기반으로 향후 판매량을 예측하는 Competition이다. train data는 2013년 1월부터 2015년 10월까지의 물품 정보 및 판매량이며, test data는 2015년 11월 물품 정보이다. 이를 기반으로 11월 물품의 판매량을 예측하는 것이 목표이다. 

### 1-2) Dataset 파악 
___

· sales_train.csv - the training set. Daily historical data from January 2013 to October 2015.
· test.csv - the test set. You need to forecast the sales for these shops and products for November 2015.
· sample_submission.csv - a sample submission file in the correct format.
· items.csv - supplemental information about the items/products.
· item_categories.csv  - supplemental information about the items categories.
· shops.csv - supplemental information about the shops.


### 1-3) 가설
___

주어진 데이터에서 여러 유용하다고 판단되는 정보들을 가공하여 예측에 사용한다. 우리가 최종적으로 구하고자 하는 값인 `판매량`은 분명 도시, 가격, 아이템, 상점 등 다양한 원인으로 인해 판매량에 상이한 차이를 보이게 될 것이다. 이러한 가설을 바탕으로 데이터 가공을 하고자 한다.

1. 도시에 대한 분석
2. 아이템에 대한 분석
3. 매출과 상점에 대한 분석
4. 휴일에 대한 분석
5. 러시아 경제에 대한 분석
6. 가격에 대한 분석 


### 1-4) 기대효과나 연구의 의의
___

실제 존재하는 데이터에 대하여 구상한 predict model이 실질적으로 어느 정도의 accuracy를 보장하는지에 대해 알아볼 수 있을 것이라고 예상된다. 또한 판매량에 있어서 어떠한 feature가 가장 영향력이 있는지를 알아봄으로써 앞으로 이와 비슷한 예측 모델을 구현 해야할 필요가 있을 경우 가장 먼저 고려해야할 feature에 대해 판단할 수 있는 기준이 될 수 있는 프로젝트 연구가 될 것이다. 


### 2. 본론

## 2-1) Feature 추출
___

1. 도시 추출
 - 도시 이름을 숫자(label)로 매핑하였음.

 - 위의 문자열로 된 도시를 아래처럼 숫자(Label)로 매핑하였음.

2. 아이템 분석
 - 중복 제거

3. 공통 카테고리 Group화 작업 및 Sub-Category 추출
 - A-B에서 A가 main, B가 sub category임.
 - Sub Category가 없는 경우는 main category를 그대로 가져와서 사용하였음.


4. Training data에서 각 달마다 Shop + item Pair를 만듦


5. Downcast를 통해 데이터 사이즈를 줄여주기
- float64 -> float 16
- int64 -> int16


6. 월별 매출 집계 + target variables 자르기
 - Target variables : shop_id / item_id / date_block_num / item_cnt_month


7. Training set에 Test set 붙이기

8. Shops, items, categories city label, category_id, main category, sub-category feature들을 모두 붙여서 dataframe으로 만들기

9. Lag feautre 만들기, Mean Encoding하기
- Target Variable에 대한 Lag 1,2,3,4,5,6,12개월 만들기
- item-target Lag : 1,2,3,6,12개월 만들기
- Shop-category Lag : 1,2개월 만들기
- Main-Category : 1개월 만들기
- Sub-Category : 1개월 만들기

10. Holiday feature 만들기
- 1월부터 12월까지 각 달에 휴일이 몇 개 있는지 하드코딩하였음.

11. Price 전 기간에 걸쳐 변하는 아이템의 가격을 중간 값으로 바꾸기


12. Price Trend feature 만들기
- 1~6개월 간 평균 가격과 전 구간의 평균 가격을 비교하여 지난 1~6개월 간 가격 트렌드 파악
- (월 별 각 상점의 평균 가격- 전 기간 평균 가격) / (전 기간 평균가격)을 기준으로 판단
- 가장 최근 한 달 전의 총 가격을 선택 (없다면 가장 최근의 것으로 대체)


13. 각 상점의 평균 매출 trend feature 만들기
- (월 별 각 상점의 평균매출 - 전 기간 평균매출) / (전 기간 평균매출)을 기준으로 판단
- 가장 최근 한 달 전의 총 매출을선택 (없다면 가장 최근의 것으로 대체)

14. 해당 상점에서 해당 물품이 언제 마지막으로 팔렸는지를 파악 (item_last_sales)
- 만약 shop-item pair에서 item이 3달전에 마지막으로 팔렸다면 item_last_sales = 3
2-2) Train

- 처음에는 XGBoost를 사용하여 train을 진행하려고 하였으나, 학습시간이 4시간이 넘어가는 관계로 LightGBM을 사용하여 train을 진행하게 되었다.
- 수 십번의 parameter tuning 결과, 실험적으로 판단했을 때 가장 영향력 있는 parameter는 num_leaves 였다. num_leaves는 전체 tree의 leave 수이다. 보통 num_leaves = 2^(max_depth)는 depth-wise tree와 같은 수의 leaves를 가지게 하여, 이보다 작게 설정해야 오버피팅을 줄일 수 있다고 알려져있다. 이번 프로젝트에서는 이런 지식을 바탕으로 num_leaves를 조절해주었다.
### 2-3) 연구결과
___

![image](https://user-images.githubusercontent.com/32920566/122233361-f5708200-cef6-11eb-9397-c7b0eef1debe.png)
<br><br>
kaggle predict future sales에 참여한 팀들 중에서 2021.6.13 기준 상위 8.3% 해당하는 1052등를 기록하였다. <br>

추가) 2021.6.15. 화요일 기준 1067 / 12638 상위 8.4%

### 3. 결론
___

### 3-1) 프로젝트 요약 
___
본 프로젝트는 주어진 월간 세일즈 데이터를 바탕으로 다음달의 세일즈 판매량을 예측하는 regression 문제이다. 주어진 데이터만 사용하여 학습하여 예측하기에는 정보량의 한계가 있어 기존의 데이터를 바탕으로 새로운 training feature를 생성하고, 추가로 예측에 도움이 될 수 있는 외부 feature를 추가하였다. 예측모델로 최신 기법 중 하나인 lightGBM 모델을 사용하여 XGBoost 보다 빠르고 비교적 정확하게 판매량을 예측할 수 있었다. 

### 3-2) feature 중요도 파악
___
사용한 feature사이에서의 중요도 순위<br>
![image](https://user-images.githubusercontent.com/32920566/122233379-fa353600-cef6-11eb-9e76-2a704b4a1502.png)
<br><br>

예상했던 대로 feature 중에서도 바로 직전 달의 데이터들이 매우 중요했다. item_cnt_month_lag1,  item_month_mean_lag_1, shop_category_month_lag_1이 feature 중요도 상위를 차지하는 것을 알 수 있었다.<br>
또한, kaggle에서 제공하지 않은 외부 변수 holidays_in_month를 추가했는데 나름 괜찮게 사용되어 실제로 휴일이 판매량에 영향을 미치는 것이 사실임을 확인할 수 있었다. 이로 미루어 보아 물가를 반영할 수 있거나 cpi 지수를 추가 feature로 도입한다면 나름 괜찮은 성능을 가질 수 있을 것이라고 생각된다.<br><br>
