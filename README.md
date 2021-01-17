## COMPAS 공공자전거 스테이션 최적위치 선정 프로젝트 

### 프로젝트 개요

해당 프로젝트는 COMPAS에서 주최한 고양시 공공자전거 스테이션 최적위치 선정 과제(https://compas.lh.or.kr/subj/past/info?subjNo=SBJ_2007_001)를 Succez팀이 수행한 결과입니다. 

#####  배경

- 고양시는 2010년부터 공공자전거 서비스(피프틴, https://www.fifteenlife.com)를 도입하여
  약 161개 스테이션(자전거 보관소)과 1,700여대의 공공자전거로 시민들에게 서비스를 제공 중에 있습니다.
- 현재 고양시는 신규 택지개발 등으로 도시화 지역이 늘어나고
  인구 증가 등으로 인하여 기존 스테이션 위치에 대한 조정이 필요합니다.

#####  목적

- 공공자전거 운영이력 데이터 및 공간 데이터를 활용하여 자전거 스테이션의 최적 위치를 선정하여
  향후 시민들의 공공자전거(공유자전거) 사용에 대한 접근성을 개선하고자 합니다.

#####  해결 과제

- 고양시에 대하여 공공자전거 스테이션의 최적위치를 제시하여 주시기 바랍니다.
- 분석 조건
  \- 스테이션의 개수는 최대 300개소
  \- 스테이션별 자전거 수용 용량 최대 30대

### Data

- COMPAS 측에서 제공한 35개의 공공자전거 운영관련 데이터를 활용하였습니다.
- https://compas.lh.or.kr/subj/past/data?subjNo=SBJ_2007_001



### 분석 

##### 1. 분석 배경

   : 기존 자전거 스테이션 위치 및 거치대의 수에 문제가 존재

   (1) 제공된 데이터로 확인한 기존 스테이션의 문제

    - 스테이션 별 대여/반납량의 편차가 심함. 즉, 특정 스테이션에 대여/반납량이 몰려있는 반면, 또 다른 스테이션의 대여/반납량은 적은 문제

    - 스테이션으로 반납되지 않는 자전거가 매우 많음을 알 수 있음

      

   (2) 피프틴 홈페이지에서 확인한 컴플레인

   - 최근 입주한(할) 대규모 주거 단지에 거주하는 시민들의 스테이션 신설 요청이 많은 것을 확인

   - 기존의 스테이션 위치는 이러한 앞으로의 계획 및 변화 등을 반영하지 못했다는 문제 존재 

     


##### 2. 분석 계획(시나리오)

   : 앞서 밝힌 문제들을 해결하기 위해 고양시 내 최적의 스테이션 위치 및 거치대 개수 선정

   (1) 스테이션의 영향권 설정

   - 스테이션의 수요를 파악하기 위해선 스테이션의 영향권을 설정(가정)할 필요가 있음

     (논문 참고, 다중 회귀 모델을 통한 영향권 확인)

   - 셋 커버링 입지 (Location Set Covering Problem: LSCP) 모델을 활용하여 모든 서비스 수요를 커버할 수 있는 최소한의 시설물을 입지시키는 방향으로 스테이션 배치를 진행

   

   (2) 현재 자전거 수요 모델링

   - 현존하는 스테이션의 월별 이용 횟수 평균을 기반으로 각 스테이션의 주거 인구, 유동 인구, 지형, 주변 시설물 등을 학습시켜 스테이션이 없는 지역들의 스테이션 월 이용 횟수 예측
   - 분석 단위: 고양시 내 모든 지역의 100m*100m 단위 격자
   - 모델: XGBoost 활용

   

   (3) 도시 계획 반영 모델링

   - 앞의 자전거 수요 모델링은 신도시 개발 중인 지역이나 차후에 건설될 시설물들을 반영하지 못하는 문제 존재. 이에 대한 보완책으로서 도시계획 데이터를 반영하여 분석

   - 분석 단위: 고양시 내 모든 지역의 100m*100m 단위 격자

   - 모델: XGBoost 활용

     

##### 3. 소요 시간

   : 1일 평균 6시간 씩 31일 소요(총 186시간 소요)

   

##### 4. 결과

   - 현재 자전거 수요 기반 모델링 결과 

     - 총 189개의 변수 중 RFE(Recursive feature elimination)를 활용하여 50개의 변수 선택
     - test R^2값이 0.937정도로 준수
     - 그래프 상으로도 수요경향성을 충분히 설명하는 것을 볼 수 있음 
       ![image](https://user-images.githubusercontent.com/67999107/104842960-925cce00-590b-11eb-8367-a8d22dd36143.png)     

     - 수요 예측 결과 

       ![image](https://user-images.githubusercontent.com/67999107/104842982-ba4c3180-590b-11eb-9dac-5d057aa7e534.png)

     

   - 도시계획 반영 수요 예측 모델링

     - test R^2 score 가 88% 정도로 상당 수준 스테이션 월별 수요를 예측할 수 있음

     - 다만, 과적합 양상이 보이고, 단일 모델로 설명하기엔 충분하지 않음.

       ![image](https://user-images.githubusercontent.com/67999107/104843003-d7810000-590b-11eb-8642-bcb54d801f63.png)

     - 수요 예측 결과

       ![image](https://user-images.githubusercontent.com/67999107/104843033-f1224780-590b-11eb-83b0-be5964d808b8.png)

   - 자전거 스테이션 배치

     1. 스테이션 간 직선 거리 확인

     2. 동단위로 합하여 스테이션 총 이용량 추산

        - 각 스테이션의 월별 총 평균 이용량을 구하고 이를 동 별로 소팅
        - 현재 스테이션 수요 기반 모델과 도시계획반영 모델의 예측 수요값 역시 동별로 추산

        - 동 별 적정 스테이션 개수 및 적정 거치대 개수를 정하기 위함 

     3. 동별 적정 스테이션 수 산출

     4. 최적 스테이션 선정

        - 100m 격자로 스테이션 좌표를 찍을 경우, 적절치 못한 위치에 자전거 스테이션이 놓일 수도 있고 도로변에서 벗어나 있는 등의 위험 존재 
        - 때문에 가중치를 두어 도로변에 근사하게 하고자 하였음. (0.75 : 0.25)
        - 그 결과, 대부분의 좌표들이 비교적 도로변에 위치하게 만들 수 있었음.


   - 기존 스테이션과 비교

     - 고양시 전체로 보면 추천 스테이션들이 더 넓고 고르게 분포된 것을 확인할 수 있음.
     - 새로운 최적 스테이션 개수가 현행보다 102개소 더 많지만 각 지역에 고르게 분포되었다는 것은 주목할 만한 점.
     - 기존 스테이션 - 파란 마커 (164개), 추천 스테이션 - 빨간 마커 (266개)
      ![image](https://user-images.githubusercontent.com/67999107/104843066-1f078c00-590c-11eb-9b00-ce969d8ad1be.png)
  
 
 - 보다 자세한 분석결과는 결과 보고서를 참고
