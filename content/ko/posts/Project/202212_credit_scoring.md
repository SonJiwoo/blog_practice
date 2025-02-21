---
date: "2022-12-25T10:08:56+09:00"
description: null
draft: false
title: 2030 개인신용평가모형
weight: 1
---

## 2030 개인신용평가모형 개발
- 분석기간: 2022 December ~ 2023 January
- 2022연세데이터사이언스경진대회에 참여하여 진행한 프로젝트이다.
- 팀원 간 정보 공유 및 기록을 위하여 [노션](https://www.notion.so/65eab74ebe4b424b80b03e6da4acdf09) 페이지를 만들어두었다.

## 1. Domain Knowledge
### 1-1. 금융리스크
{{<expand "리스크 관리">}}
1. 리스크 인식
    * 계량 가능: 신용리스크, 운영리스크, 시장리스크, (금리 리스크)
    * 계량 애매: 신용편중 리스크
    * 계량 불가: 유동성 리스크, 전략 리스크

2. 리스크 측정
    * (아래 '신용리스크' 참고)

3. 리스크 대비
    * 리스크 회피 Risk Avoidance
    * 리스크 전가 또는 경감 Risk Transfer & Mitigation
    * 리스크 보유 Risk Retention

4. 실행

5. 평가 (모형적합성 검증)
    * 사전 검증: 모형의 성능
    * 사후 검증: 예측 vs. 실제 결과
{{</expand>}}

{{< expand "신용리스크" >}}
1. EL (Expected Loss)
EL = PD X EAD X LGD
    * PD(Probability of Default): 부도율 (1년내)
    * EAD(Exposure At Default): 부도시 총 부채
    * LGD(Loss Given Default): 부도시 손실율

2. UL (Unexpected Loss)
손실분포의 99.9% quantile 값이다. 여기서 분포함수식은 Basel 위원회에서 제시한다.
{{< /expand >}}

{{<expand "Point 정리">}}
1. 리스크 대비 수익을 최대화 하는 모델

2. 은행들마다 신용평가 모형이 다르다는 것은 신용등급체계가 다르다는 것이고, 신용등급별 부도율 (PD) 도 다르다는 뜻이다.

3. 신용등급 체계의 기본 원칙
    - 보통 5년치로 만듦
    - 최소 7개 이상의 정상 등급, 최소 1개 이상의 부도 등급
    - TTC 모형: 경기 효과를 반영하여 경기변동에 robust한 모형 
    - PIT 모형: 경기와 상관없이 절대적인 값으로 그때마다 다르게 측정
    - 대부분의 모형은 TTC와 PIT의 적절한 조합을 이룬 모형. 보통 transition matrix로 평가

4. 그 외
    - 재무 비율의 네 그룹
        - 수익성 비율(얼마나 잘 버는지)
        - 안전성 비율 → 부채비율(부채 / 자산)
        - 현금상환능력비율(현금으로 상환할 수 있는 능력이 되는지 → 유동성)
        - 활동성 비율
    - 부도란? : 1) 상당한 정도의 채무에 대해 2) 90일 이상 연체
    - 신용 등급 부여 시 유의점 : 1) 최소 7개 이상의 정상 등급, 최소 1개 이상의 부도 등급 2) 신용 등급마다 차별화가 되어야 함 3) 신용 등급 별 역전 현상이 없어야 함(등급 낮은데 PD 낮고 그런거 있으면 안됨) 4) 등급별 차주의 구성비가 **분포를 따라야 됨**
{{</expand>}}

### 1-2. 개인신용평가모형
[NICE평가정보 신용등급체계공시](https://www.niceinfo.co.kr/creditrating/cb_info_4_3_1.nice)를 참고하였다.

{{<expand "주요평가요소">}}
1. 상환이력
    - 현재 연체 및 과거 연체 이력
    (1) 장기연체 발생 (90일 이상 연체)
    (2) 단기연체 발생 (5영업일 10만원 이상)
    (3) 연체 진행 일수 경과
    (4) 연체 해제
    (5) 연체 해제 일수 경과

2. 부채수준
    - 채무 부담 정보 (개인대출 및 보증채무)
    (1) 고위험 대출 발생
    (2) 고위험 외 대출 발생
    (3) 대출 잔액 증가
    (4) 대출 부분 상환
    (5) 대출 전액 상환
    (6) 보증 발생
    (7) 보증 해소
    
3. 신용거래기간
    - 최초 개설기간, 최근 개설로부터 기간
    (1) 신용거래 기간 없음
    (2) 신용거래 기간 경과

4. 신용형태
    - 신용거래 패턴 (체크/신용카드 이용 정보)
    (1) 신용/체크카드 사용 개월
    (2) 신용/체크카드 사용 금액 적정
    (3) 과다 할부 사용
    (4) 현금서비스 사용
    
5. 기타
    (1) 증빙소득
    (2) 비금융거래 성실납부실적 등록
{{</expand>}}

# ---

## 2. What I Have Learned

### 1. GLMM
Generalized Linear Mixed Model
본 데이터는 36개월동안 매월말 반복측정된 패널데이터였다. 또한, 주제와 관련하여 연령대별 특징을 잘 보정해줄 필요가 있었기에 GLMM이 가장 적절한 모델이라고 판단하였다. `R`에는 `nlme`, `lme4`, `brms` 등 glmm을 구현하는 다양한 패키지들이 있다. 해당 패키지들을 모두 사용해보았는데, 이중에서 `glmmTMB`라는 패키지를 최종적으로 선택하게 되었다. 이 패키지를 사용한 이유는, temporal autocorrelation와 crossed random effect를 동시에 고려하면서 계산할 수 있는 패키지였기 때문이다. GLMM과 관련하여 공부한 내용은 [노션 개인블로그 페이지](https://www.notion.so/668ea143e2c140dbaae3299ed5883936?v=77a6fcb0d889454f8572a625ac78ac38&p=4b273ec4dde94ee3a4f1317a5144624a&pm=s)에 정리해두었고, 참고한 자료는 [해당 노션 페이지](https://www.notion.so/6a60a83713b94540a94480d12e370d88?v=b9a472ba358340ec923f866113361ff4&p=6e6d0f0aa5f84ab0b6395d2a5831034c&pm=s)에 정리해두었다.

### 2. 빅데이터 활용 경험
3400만 행 x 153개 열의 데이터는 17GB가 넘는 큰 데이터.
만 명 내외의 샘플로 모델을 학습하더라도, 복잡한 모델의 경우에는 RAM 64GB의 데스크톱으로도 모델 학습 어려웠다.
이를 해소하기 위해서 sparse Matrix를 활용하는 방법을 고민해보고, 유의미한 sampling을 하는 방식에 대해서도 고민하는 등, 현실적인 측면에서의 어려움을 해소하는 방법들에 대해서도 고민해볼 수 있는 유의미한 프로젝트였다.

### 3. 불균형데이터의 중요성
훈련용데이터에 연령대도 모두 2000명씩, 그리고 그 세부적인 구성도 Y=0과 Y=1를 각각 1000명씩 균형잡히게 넣어주었더니 성능이 향상되었다. 훈련용데이터의 불균형도를 해소하기 전에는 f1-score가 0.3~0.4 정도였는데, 불균형도를 해소한 뒤 적합하였을 때에는 f1-score를 0.65(신규), 0.91(기존)까지 향상시킬 수 있었다.

### 4. 상단코딩
분석데이터는 수치형변수가 1% 값으로 상단코딩되어 있었다. 이에 따라 변수 간의 관계도 훼손되어있었고 그에 따른 파생되는 다양한 문제들이 있었다. 이를 되돌리기 위해서 다양한 노력을 했지만 쉽지 않았다. Domain Knowledge에 따라 유의미할 것으로 판단되는 변수들을 그래서 사용하지 못한 경우들이 있었는데, 추후 상단코딩 데이터를 분석하게 되거나 내가 직접 상단코딩 등으로 데이터의 보안을 수행하게 된다면 주의해야 할 점들을 배울 수 있는 프로젝트였다.

### 5. So WHAT?
**미래장기연체가능성**을 예측하고, 그것을 바탕으로 최종적으로 어떻게 기존 모형에 활용할지 고민해보았다. 단순히 예측하는 데에서 그치는 것이 아니라, 이를 활용하여 현업에 적용하는 방안까지 고민해본 것이 좋은 경험이었다. 즉, 수동적인 데이터 분석이 아니라 분석결과를 어떻게 활용할 수 있고 그것의 긍정적인 가치(기대효과)까지 고민하고 기존 모델의 개선된 버전을 제안하는 데까지 이어질 수 있었다.

# ---

## 3. Presentation
![Slide01](images/posts/project/202212_credit_scoring/슬라이드1.PNG)
![Slide02](images/posts/project/202212_credit_scoring/슬라이드2.PNG)
![Slide03](images/posts/project/202212_credit_scoring/슬라이드3.PNG)
![Slide04](images/posts/project/202212_credit_scoring/슬라이드4.PNG)
![Slide05](images/posts/project/202212_credit_scoring/슬라이드5.PNG)
![Slide06](images/posts/project/202212_credit_scoring/슬라이드6.PNG)
![Slide07](images/posts/project/202212_credit_scoring/슬라이드7.PNG)
![Slide08](images/posts/project/202212_credit_scoring/슬라이드8.PNG)
![Slide09](images/posts/project/202212_credit_scoring/슬라이드9.PNG)
![Slide10](images/posts/project/202212_credit_scoring/슬라이드10.PNG)
![Slide11](images/posts/project/202212_credit_scoring/슬라이드11.PNG)
![Slide12](images/posts/project/202212_credit_scoring/슬라이드12.PNG)
![Slide13](images/posts/project/202212_credit_scoring/슬라이드13.PNG)
![Slide14](images/posts/project/202212_credit_scoring/슬라이드14.PNG)
![Slide15](images/posts/project/202212_credit_scoring/슬라이드15.PNG)
![Slide16](images/posts/project/202212_credit_scoring/슬라이드16.PNG)
![Slide17](images/posts/project/202212_credit_scoring/슬라이드17.PNG)
![Slide18](images/posts/project/202212_credit_scoring/슬라이드18.PNG)
![Slide19](images/posts/project/202212_credit_scoring/슬라이드19.PNG)
![Slide20](images/posts/project/202212_credit_scoring/슬라이드20.PNG)
![Slide21](images/posts/project/202212_credit_scoring/슬라이드21.PNG)
![Slide22](images/posts/project/202212_credit_scoring/슬라이드22.PNG)
![Slide23](images/posts/project/202212_credit_scoring/슬라이드23.PNG)
![Slide24](images/posts/project/202212_credit_scoring/슬라이드24.PNG)
![Slide25](images/posts/project/202212_credit_scoring/슬라이드25.PNG)
![Slide26](images/posts/project/202212_credit_scoring/슬라이드26.PNG)
![Slide27](images/posts/project/202212_credit_scoring/슬라이드27.PNG)
![Slide28](images/posts/project/202212_credit_scoring/슬라이드28.PNG)
![Slide29](images/posts/project/202212_credit_scoring/슬라이드29.PNG)
![Slide30](images/posts/project/202212_credit_scoring/슬라이드30.PNG)
![Slide31](images/posts/project/202212_credit_scoring/슬라이드31.PNG)
![Slide32](images/posts/project/202212_credit_scoring/슬라이드32.PNG)
![Slide33](images/posts/project/202212_credit_scoring/슬라이드33.PNG)
![Slide34](images/posts/project/202212_credit_scoring/슬라이드34.PNG)
![Slide35](images/posts/project/202212_credit_scoring/슬라이드35.PNG)
