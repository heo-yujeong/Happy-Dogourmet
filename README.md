## 💻 개요
- 진행기간 : 2020. 08. 28 ~ 2020. 10. 21 (8주)
- 서비스명 : 행복한 멍식가
- 서비스 소개 : 건강한 반려견을 위한 최적의 사료 제안

<br />

## 💡 기획 배경
점차 증가하는 국내 반려동물 시장 규모에 따라 반려견이 먹는 사료에 대한 관심 지속적으로 증가
이에 반려견의 특성에 맞는 사료 추천 서비스를 구현하고자 함

<br />

## 🗂️ 주요 기능
### 견종별, 구성 원료별, 생애주기별 맞춤 사료 추천
- 견종별 추천 : 견종을 옵션에서 선택하거나 사진을 찍어 반려견의 견종을 확인한 뒤, 견종별 유전질환을 기반으로 사료 추천
- 원료별 추천 : 반려견에게 알러지를 일으킬 수 있는 특정 원료를 제외한 사료나 효과를 보고 싶은 부위에 적합한 원료를 포함한 사료 추천
- 생애주기별 추천 : 생애주기별에 따른 필요 영양소에 따라 사료 추천

<br />

## 📊 데이터 분석
### 0. 분석 기획 및 문제정의
<table>
  <tr>
    <th style="width: 20%">서비스</th>
    <th style="width: 40%">견종별 사료 추천</th>
    <th style="width: 40%">견종 예측</th>
  </tr>
  <tr>
    <td>문제 정의</td>
    <td>견종마다 유전적인 질병이나 자주 겪게 되는 질병 존재</td>
    <td>견종 확인이 어려운 경우 존재<br>(ex. 유기견, 믹스견)</td>
  </tr>
  <tr>
    <td>분석 주제</td>
    <td>사료의 성분을 분석하여 질병을 예방할 수 있도록 도움</td>
    <td>사진으로 견종을 분류하여 견종 파악에 도움</td>
  </tr>
  <tr>
    <td>분석 방법</td>
    <td>h-cluster와 k-means를 통한 군집화</td>
    <td>CNN 모델링을 통한 이미지 학습</td>
  </tr>
  <tr>
    <td>독립변수 / 종속변수</td>
    <td>사료의 성분 포함 여부 / X(비지도학습)</td>
    <td>견종 이미지 / 견종</td>
  </tr>
</table>

<br />

### 1. 견종별 사료 군집화
#### 데이터 수집
- 쇼핑 사이트 사료 인기순위에 따라 16개의 사료 제조사 선정
- 제품의 사료성분 크롤링
  - 이미지인 경우 PIL라이브러리의 `Image`, `pytesseract` 사용
  - 텍스트인 경우 `BeautifulSoup` 사용

#### 데이터 전처리
- 오타 수정
- 원료명 통일(ex. 리보플라민 = 비타민 B2, 라이신/리신 = L-라이신,...)
- 수식어 삭제(영양성분에 영향을 미치지 않는 수식어 삭제)
- 원료 삭제(분석 주제와 상관없거나 영양성분에 영향을 미치지 않는 원료 삭제)
- 사료의 포함 유무에 따라 0과 1로 수치화

#### 가설 설정 및 분석
- 가설 설정 : 사료의 특정 원료 포함 유무에 따라 사료를 군집으로 나눌 수 있고, 같은 군집에 속하면 동일한 질병을 예방하거나 동일한 강화효과를 보일 것이다.
- 분석 방법 : `h-cluster`의 elbow기법을 통한 적합한 군집 개수 도출과 `k-means`를 통한 군집화

<br />

### 2. 견종 예측을 위한 CNN 이미지 학습
#### 데이터 수집
- 견종
  - Tensorflow Standard dog dataset을 바탕으로 120개 견종, 20,580장의 이미지 데이터셋 구축
  - 한국 인기 견종 리스트 Top100을 바탕으로 추가 8종 크롤링
- 128개 견종을 중심으로 유전적 질병 및 견종 정보 수집

#### 데이터 전처리
- 이미지 정제(견종의 모습에서 많이 벗어나거나 관련 없는 사진 삭제, 수정)
- 배열화(이미지 데이터를 배열로 만들어 수치화)
- 정규화(최적값에 도달하기 위해 255로 나누어 수치를 표준화)
- 카테고리화(One hot vector로 변환하여 각 label 학습을 위한 자료 준비)

#### 가설 설정 및 분석
- 가설 설정 : 견종별 이미지를 학습시킨 모델을 만들고, 그 모델에 새로운 강아지 이미지를 넣으면 견종을 예측할 수 있을 것이다.
- 분석 방법 : `CNN 모델` 6가지(기본 CNN, AlexNet, GoogleNet, VGGNet, ResNet, Teachable machine)를 바탕으로 epoch, batch size, learning rate, layer를 조정하여 최적의 모델 구축

<br />

## 🏷️ 기술 스택 및 개발 환경
#### Language
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=fff)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=JavaScript&logoColor=white)

#### Framework
![Jupyter](https://img.shields.io/bedge/Jupyter-F37626?style=for-the-badge&logo=Jupyter&logoColor=fff)
![Expo](https://img.shields.io/badge/Expo-000020?style=for-the-badge&logo=Expo&logoColor=fff)

#### DB
![Firebase](https://img.shields.io/badge/Firebase-DD2C00?style=for-the-badge&logo=Firebase&logoColor=fff)
![MySQL](https://img.shields.io/badge/MySQL-00000F?style=for-the-badge&logo=mysql&logoColor=white)

#### Environment
![Xcode](https://img.shields.io/badge/Xcode-007ACC?style=for-the-badge&logo=Xcode&logoColor=white)
![VSCode](https://img.shields.io/badge/Visual_Studio_Code-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white)
![Anaconda](https://img.shields.io/badge/Anaconda-44A833?style=for-the-badge&logo=anaconda&logoColor=fff)

#### etc
![Excel](https://img.shields.io/badge/Excel-1D6F42?style=for-the-badge&logo=Excel&logoColor=#FFF)
![Adobe XD](https://img.shields.io/badge/Adobe%20XD-470137?style=for-the-badge&logo=Adobe%20XD&logoColor=#FF61F6)
![Adobe Photoshop](https://img.shields.io/badge/Adobe%20Photoshop-31A8FF?style=for-the-badge&logo=Adobe%20Photoshop&logoColor=black)
![Adobe Illustrator](https://img.shields.io/badge/Adobe%20Illustrator-FF9A00?style=for-the-badge&logo=adobe%20illustrator&logoColor=white)
![Zeplin](https://img.shields.io/badge/Zeplin-E34F26?style=for-the-badge&logo=Zeplin&logoColor=#FFF)

<br />

## 👨‍👧‍👧 팀원 소개
<table style="width: 60%;">
  <tr>
    <th style="width: 30%; text-align: center;">이름</th>
    <th style="width: 70%; text-align: center;">역할</th>
  </tr>
  <tr>
    <td style="text-align: center;">허유정</td>
    <td>
      <ul>
        <li>팀장</li>
        <li>DB구축 및 분석</li>
        <li>개발(서브)</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td style="text-align: center;">윤 강</td>
    <td>
      <ul>
        <li>개발</li>
        <li>DB구축(서브)</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td style="text-align: center;">김산홍</td>
    <td>
      <ul>
        <li>기획</li>
        <li>디자인</li>
        <li>DB구축 및 분석(서브)</li>
      </ul>
    </td>
  </tr>
</table>
