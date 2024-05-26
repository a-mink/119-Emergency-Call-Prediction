# 119 신고접수량 예측 / 이어드림 실전경진대회 / @a-mink
<br>

## 문제 정의
부산 지역의 과거 119 신고 접수 데이터의 전일 야간 시간대(18 ~ 9시) 신고 접수 데이터를 이용하여  
주간 시간대(9 ~ 18시) 신고 접수량 예측  
  
시계열 예측(time series prediction) | 평가지표 : MAPE(Mean Absolute Percentage Error)  
  
  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/call.png' width='200'>
  
  <br>
  
## 데이터 상세
Input  
- 119 신고 접수 데이터(전일 오후 6시 00분~당일 오전 8시 59분)
Target
- 주간 근무 시간대(9~18시) 119 신고 접수량
  
Public&Private Test Data
- Public/Private 데이터 비율 : 약 30%(32일)/70%(75일)
- 대회 중 리더보드에서는 Public 점수만 확인 가능
- 대회 종료 후 Private 점수도 반영한 Final 점수로 순위를 정함. 
  
  <br>
  
  
----------------------------------------------------------------------------
  
  <br>
  
  
## 데이터 관찰 및 가설
  
<img src="https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202024-05-17%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%208.03.39.png" width="500">
  <br>
  
### (나의 추측) '접수분류'의 '안내/출동'별 신고접수경향이 다를 것이다.
- 안내 = 단순 문의, 출동 = 긴급 구조
- 근거 : 모든 긴급 구조 값이 출동에 속함 + 출동이면 모두 긴급 구조 분류의 값이 존재.  
-> 안내는 사람들의 상황에 따라 선택 가능, 출동은 사람들의 상황과 별개로 피치못해 발생하는 문제.
  
#### * 가설  
'안내' : 학교/회사 등의 일정이 없는 휴일에 단순 문의가 증가해 '안내'의 수치도 증가.  
'출동' : 인위적인 힘이 개입하기 어려운 긴급한 문제들 -> 큰 시기적(계절적)변화는 있을지라도 평균적인 수치 보일 것.  
  <br>
  
#### * 실제 검증   
'안내:general', '출동:urgent'로 표시  
[전체기간 접수분류 별 신고접수 건수]  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/%E1%84%82%E1%85%A1%E1%86%AF%E1%84%8D%E1%85%A1%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%80%E1%85%B5%E1%86%AB%E1%84%80%E1%85%B3%E1%86%B8%3A%E1%84%8B%E1%85%B5%E1%86%AF%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%80%E1%85%B3%E1%84%85%E1%85%A2%E1%84%91%E1%85%B3.png' width ='600'>
  
전체 기간의 접수 분류 별 신고접수 건수를 <seaborn> 라이브러리를 사용해 lineplot으로 시각화해본 결과,  
'출동' 접수(urgent)의 경우, 가설 대로 일자별 쏠림 현상 등이 없이 일정한 추세를 보이는 것을 확인할 수 있었다.
  <br>
  
[2013년 접수분류 별 신고접수 건수]  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/2013_%E1%84%82%E1%85%A1%E1%86%AF%E1%84%8D%E1%85%A1%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%80%E1%85%B5%E1%86%AB%E1%84%80%E1%85%B3%E1%86%B8%3A%E1%84%8B%E1%85%B5%E1%86%AF%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%80%E1%85%B3%E1%84%85%E1%85%A2%E1%84%91%E1%85%B3.png' width='600'>
  
[2013년도 1분기 접수분류 별 신고접수 건수]  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/2013_1%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5_%E1%84%8C%E1%85%A5%E1%86%B8%E1%84%89%E1%85%AE%E1%84%87%E1%85%AE%E1%86%AB%E1%84%85%E1%85%B2%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%89%E1%85%B5%E1%86%AB%E1%84%80%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%B8%E1%84%89%E1%85%AE%20%E1%84%80%E1%85%A5%E1%86%AB%E1%84%89%E1%85%AE.png' width='600'>  
  
'안내' 접수의 가설도 검증해 보기 위해 2013년/2013년 1분기의 데이터를 lineplot으로 구현했다.  
주말을 기점으로 안내 문의(general)가 증가한다는 것 뿐만 아니라, 연휴(설날)를 기점으로 안내 문의가 증가한다는 것을 알 수 있었다.  
  
이를 통해 '공휴일'에 '안내'접수가 증가한다는 가설과 '출동'접수의 경우 일정한 수치를 보인다는 가설이 모두 사실이었음을 알 수 있었다.  
#### -> 2가지 가설 모두 '참'
  
  <br>
  
## 데이터 ML 구축 실험 계획  
#### <실험해 볼 특성>  
- 접수 분류 여부
- 공휴일 여부
  <br>
#### <실험해 볼 예측 방법>  
- '야간'데이터로 '주간' 데이터 예측(회귀 분석)
- '야간'데이터로 '전체' 데이터 예측 한 후, '전체'-'야간'으로 '주간' 계산(회귀 분석)
- '전체' 데이터 예측(시계열 분석) (-> 앞 두가지 방법으로 8점 대 이하 점수가 나오지 않을 경우)  
  <br>
[전체/주간/야간 신고접수건수 공분산(상관관계)]
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/main/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202024-05-18%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%208.29.24.png' width='400'>  
<br>
전체/주간/야간 신고접수건수의 상관관계 표를 보면, '전체-야간'의 상관계수가 가장 높고, '야간-주간'의 상관계수가 그 다음으로 높았다.<br>
<br>
따라서, 문제에서 제시한 '야간'으로 '주간'데이터를 예측하는 방법 외에  <br>
'야간'데이터로 '전체' 데이터 예측 한 후, '전체'-'야간'으로 '주간' 계산하는 방법도 실험해볼 예정.  <br> 
또한, 회귀 분석 방법으로 예측 성능이 높게 나오지 않을 경우 시계열 분석 방법을 적용해볼 예정.<br>
<br>
#### <실험 시나리오>

<img src='' width='600'>  

