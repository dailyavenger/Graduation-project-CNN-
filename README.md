본 연구의 목표는 그림과 구성을 고려해 보고서를 평가하는 모델을 제작하는 것이다. 보고서의 점수는 그림과 구성에 의해 좌우될 수 있다. 예를 들어 아래의 두 논문 중, 시각적으로 우수한 좌측 논문이 더 높은 점수를 받을 가능성이 높음을 추측할 수 있다

(논문1)

<img src="https://user-images.githubusercontent.com/65271296/191182803-91f5b5c7-4840-4780-94a6-318add807acd.JPG" width=40%>

(논문2)

<img src="https://user-images.githubusercontent.com/65271296/191182818-b6d11e6c-6489-45ee-a6b4-7ad5381c8c3e.JPG" width=40%>

CNN 모델이 학습할 데이터셋을 제작하기 위해 OpenReview 사이트를 크롤링하였고, 11만개의 이미지로 구 성된 데이터셋을 제작하였다. 본 연구의 주제가 보고서 점수 예측이지만 보고서 데이터는 공개된 자료가 적어 논문 데이터를 사용하였다. OpenReview는 연구자들이 논문을 컨퍼런스에 제출하기 전 피어 리뷰를 받는 사이 트로, 모든 피어 리뷰와 논문 데이터가 공개되어있다

우리는 저명한 머신러닝 학회 중 하나인 ICLR(International Conference on Learning Representations)의2019∼2021 년 컨퍼런스에 제출된 논문 데이터를 사용하였다. 논문의 각 페이지를 이미지로 변환하여 데이터셋을 구성하 였다. 총 논문은 6,227개이며, 이미지는 111,128개이다.

데이터셋은 이미지와 피어 리뷰 점수로 구성된다. 논문은 3∼5개의 피어 리뷰를 받으며, 평균 점수를 계산하여  사용하였다.  점수의  범위는  1∼10점이며,  5점  근처의  점수를  받은  논문이  가장  많았다.  아래  사진은  논문의점수  분포이다. 점수의 정수 부분을 사용해 그래프를 구성하였다.

<img src="https://user-images.githubusercontent.com/65271296/191184443-d77c20a4-a58b-454f-9ce1-d31afd1464bb.JPG" width=40%>

데이터셋을 분석한 결과도 첨부한다.

<img src="https://user-images.githubusercontent.com/65271296/191188416-89df8bc2-e058-4767-aabf-9f8dc0e7a263.png" width=40%><img src="https://user-images.githubusercontent.com/65271296/191188536-8545c22c-06f3-4423-a76b-5bf97866502a.png" width=40%>

<img src="https://user-images.githubusercontent.com/65271296/191188700-dda2676e-3f82-452b-97ee-206850b2d1df.png" width=40%>

본 연구에서는 ResNet50을  기반으로 CNN  모델을 제작하였다
Layer 수가 늘어나면 신경망이 깊을 때, 여러번 곱해져서 작은 미분값은 작아지고, 큰 미분값은 커져 성능이 떨어진다.
이 문제를 해결하기 위해서 ResNet은 Skip Connection 기법을 쓴다. 

<img src="https://user-images.githubusercontent.com/65271296/191184971-a9f7bf56-606a-4079-b7bb-ab3fa960ea7c.JPG" width=90%>

<img src="https://user-images.githubusercontent.com/65271296/191184731-899d7eaa-84e5-4ee1-9295-f8876c1cfde1.JPG" width=40%>

그리고 본 연구에서는 논문별로 앞부분의 9페이지만 이용한다. 그 이유는 ICLR이 내용을 9페이지로 작성하도록 하기 때문이다. 10페이지부터의 references, appendices는 고려하지 않기로 하였다. 그리고 논문 pdf를 각 페이지마다 224X224 이미지로 변환하였다.

<대조군 설정>

본 연구 모델의 성능 예측을 위해서 모든 논문들의 평점의 평균을 계산한 후, 모든 논문의 평점 예측을 모두 평균값으로 동일하게 하였고 이 때 발생한 loss값을 대조군으로 설정하였다.
이 때 발생한 loss는 아래와 같다.

Loss(Mean Squared Error): 1.0114
Difference(|Predicted Score– Actual Score|): 0.8118

<처음 데이터셋에서 CNN을 돌린 결과>
Loss: 0.5479, Difference: 0.5643

<img src="https://user-images.githubusercontent.com/65271296/191185984-7f6c07de-9430-415e-b317-af25f81cc34e.png" width=40%>

<Attention Map에서 나타난 문제점>

Attention Map은 모델이 이미지 데이터들의 어떤 영역을 중요하게 보는지를 보여준다.
우리는 모델의 Attention Map을 시각화하기 위해 Grad-CAM을 사용하였다.
그 결과 모델이 페이지 상단에 해당 논문이 Published됬는지 안됬는지 여부를 알려주는 글자를 집중적으로 보고있다는 결과가 나왔다.

![image](https://user-images.githubusercontent.com/65271296/191186530-d764b035-c08a-48a7-a26f-a826a41d1e88.png)

<이미지 마스킹>
페이지마다 상단을 하얀 여백으로 바꿔서 published 여부에서 논문의 평점에 대한 힌트를 얻을 수 없도록 하였다. 

![캡처11](https://user-images.githubusercontent.com/65271296/191186817-677b91ed-f7fd-45c9-9d6f-a594aad4d2dc.JPG)

학회 저널에 해당 논문이 수록됬으면 저자를 논문에 나타내고 기각되면 저자를 Anonymous Author로 적혀있었다.
이를 통해 힌트를 얻는걸 방지하기 위해 이 부분도 하얀색 여백으로 바꾸었다.

![캡처22](https://user-images.githubusercontent.com/65271296/191186937-1b0f8e2d-74cf-45f8-9603-3528b38dd44b.JPG)

<다시 CNN을 돌린 결과>

Loss: 0.9563, Difference: 0.7876

<img src="https://user-images.githubusercontent.com/65271296/191187263-b03d5ece-14a5-4ac3-a9cf-c1fc2b190b48.png" width=40%>

<성능 높이려고 한 작업들>

1. 페이지 하나당 224X224로 보기에는 너무 해상도가 낮은거 같아, 논문의 페이지별로 12개 구역으로 나누고 각각의 구역을 224X224로 만들어 input에 넣고 점수 예측을 하였다.
2. Under/Over Sampling : 실제 평점의 분포가 4~6점대는 많은데 양쪽 끝은 적은 편이다. 이에 3점,7점인 논문은 이미지를 뒤집어서 input에 추가하는 등 데이터 수를 늘리고, 5점인 논문은 이미지 중 절반을 빼내어 데이터 수를 줄였다.
3. 논문별로 2페이지~9페이지를 하나의 텐서로 묶어, 24개 채널로 이루어진 텐서를 만들고(각 페이지별로 RGB 정보가 따로 있다.) 그걸 input으로 넣어 평점을 예측하는 모델을 만들었다.

이 중 3번째 시도가 제일 주효했고, 성능은 다음과 같았다.

Loss: 0.6989, Difference: 0.6552

<img src="https://user-images.githubusercontent.com/65271296/191187995-be502391-7852-4f8b-800c-9c12bd655c74.png" width=40%>
