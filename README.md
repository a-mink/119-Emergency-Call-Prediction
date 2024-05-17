# 119 신고접수량 예측 / 이어드림 실전경진대회

## 문제 정의
부산 지역의 과거 119 신고 접수 데이터의 전일 야간 시간대(18 ~ 9시) 신고 접수 데이터를 이용하여  
주간 시간대(9 ~ 18시) 신고 접수량 예측  
시계열 예측(time series prediction) | 평가지표 : MAPE(Mean Absolute Percentage Error)  


<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/call.png' width='200'>


## 데이터 상세
Input  
- 119 신고 접수 데이터(전일 오후 6시 00분~당일 오전 8시 59분)
Target
- 주간 근무 시간대(9~18시) 119 신고 접수량

Public&Private Test Data
- Public과 Private 데이터 비율은 약 30%(32일)과 70%(75일)입니다.
- 대회 중 리더보드에서는 Public 점수만 확인할 수 있으며, 대회 종료 후 Private 점수도 반영한 Final 점수로 순위를 정함. 

----------------------------------------------------------------------------

## 데이터 관찰 및 가설
<img src="https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202024-05-17%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%208.03.39.png" width="500">

### (나의 추측) '접수분류'의 종류별로 신고접수경향이 다를 것이다.
- 접수 분류 : 안내/출동으로 나뉨. 출동=긴급 구조  
- 긴급구조(접수 분류 중 '출동') 분류 : 구급, 기타, 구조, 화재  
-> 안내는 단순 문의, 출동은 피치못해 발생하는 문제 해결.
  
#### * 나의 가설  
'안내' 접수의 경우, 학교/회사 등의 일정이 없는 휴일에 단순 문의가 증가해 '안내'의 수치도 증가할 것이다.  
'출동' 접수의 경우, 인위적인 힘이 개입하기 어려운 긴급한 문제들이기 때문에 인위적인 개입이 어렵다. -> 큰 시기적(계절적)영향은 있을지라도 일자별 쏠림 현상 없이 평균적인 수치가 지속될 것이다.  

#### * 실제 검증   
'안내:general', '출동:urgent'로 표시  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/%E1%84%82%E1%85%A1%E1%86%AF%E1%84%8D%E1%85%A1%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%80%E1%85%B5%E1%86%AB%E1%84%80%E1%85%B3%E1%86%B8%3A%E1%84%8B%E1%85%B5%E1%86%AF%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%80%E1%85%B3%E1%84%85%E1%85%A2%E1%84%91%E1%85%B3.png' width ='600'>
<전체기간 접수분류 별 신고접수 건수>  

전체 기간의 접수 분류 별 신고접수 건수를 <seaborn> 라이브러리를 사용해 lineplot으로 시각화해본 결과,  
'출동' 접수(urgent)의 경우, 가설 대로 일자별 쏠림 현상 등이 없이 일정한 추세를 보이는 것을 확인할 수 있었다.

<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/2013_%E1%84%82%E1%85%A1%E1%86%AF%E1%84%8D%E1%85%A1%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%80%E1%85%B5%E1%86%AB%E1%84%80%E1%85%B3%E1%86%B8%3A%E1%84%8B%E1%85%B5%E1%86%AF%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%80%E1%85%B3%E1%84%85%E1%85%A2%E1%84%91%E1%85%B3.png' width='600'>
<2013년 접수분류 별 신고접수 건수>  
  
<img src='https://github.com/a-mink/119-Emergency-Call-Prediction/blob/source/2013_1%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5_%E1%84%8C%E1%85%A5%E1%86%B8%E1%84%89%E1%85%AE%E1%84%87%E1%85%AE%E1%86%AB%E1%84%85%E1%85%B2%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%89%E1%85%B5%E1%86%AB%E1%84%80%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%B8%E1%84%89%E1%85%AE%20%E1%84%80%E1%85%A5%E1%86%AB%E1%84%89%E1%85%AE.png' width='600'>
<2013년도 1분기 접수분류 별 신고접수 건수>  

'안내' 접수의 가설도 검증해 보기 위해 2013년/2013년 1분기의 데이터를 lineplot으로 구현했다.  
주말을 기점으로 안내 문의(general)가 증가한다는 것 뿐만 아니라, 연휴를 기점으로 안내 문의가 증가한다는 것을 알 수 있었다.  

이를 통해 '공휴일'에 '안내'접수가 증가한다는 가설과 '출동'접수의 경우 일정한 수치를 보인다는 가설이 모두 사실이었음을 알 수 있었다.  
#### -> 2가지 가설 모두 '참'

## 데이터 ML 구축 계획

