# Scene-Text-Recognition

## **Introduction**

[사이트로 이동](http://dmqm.korea.ac.kr/activity/seminar/320)

<br>

### **STR이란?**

일상적인 풍경 이미지에서 **글자가 있는 영역을 탐지**하고 이를 **컴퓨터 문자로 변환**하는 문제를 말한다.   
이미지 인식 연구의 응용 분야 중 하나로,

+ Object Detection
+ Semantic Segmentation
+ Sequential Image Classification

등 여러 알고리즘이 복합적으로 사용된다.

이미지 번역(스캔 번역 아님), 차량 번호판/이정표/명함 인식, 이미지 검색 등에 사용된다.

<br><br>

### **OCR과의 차이는?**

OCR(Optical Character Recognition)은 종이 문서 등에 인쇄된 문자를 읽어내는 문제를 주로 다룬다. 즉, STR보다 정형화된 환경이 조성되어있다는 것이다.

STR은 OCR보다 **배경도 복잡**하고, **글씨체도 다양**하며, **배열된 글자의 각도 및 구도가 천차만별**이고, **색상이 다채로운** 이미지를 처리하는 것이 도전과제이다.

<br><br>

### **STR의 주요 도전과제**

STR은 다음과 같은 도전과제를 가진다.

+ Arbitrary-oriented/Multi-oriented text : 비스듬하거나 회전된 문자
+ Occusion : 서로 겹쳐있는 문자
+ Curved text : 곡선형으로 나열된 문자

이를 처리하기 위해 사용하는 Detection Format은 아래와 같다.

+ Rectangle
    - The degree of freedom = 4 : (x, y, w, h)
+ Rotated rectangle
    - The degree of freedom = 5 : (x, y, w, h, θ)
+ Quadrangle
    - The degree of freedom = 8 : (x1, y1, w1, h1, x2, y2, w2, h2)
+ Polygon
    - Lots of points
+ etc.
    - Segmantation

<br><br>

### **STR의 세부 분류**

STR은 크게 세 종류로 나눌 수 있다.

1. 이미지에서 글자가 위치한 **영역을 탐지**하는 `Scene Text Detection`
2. 원본 이미지에서 글자가 존재하는 **영역을 잘라내 글자를 읽어내는** `Scene Text Recognition`
3. 1번과 2번을 동시에 수행하는 `End-to-end scene text recognition` (`Text Spotting`이라고 하기도)

<br><br><br>


## **#1. Detection**

주로 사용되는 방식은 CNN 기반 Detection이다. 이미지에서 Feature map을 추출한 뒤 디코더를 통해 단어 영역을 생성한다.

ROI(Region of Interest)를 추려내는 고전적인 Object Detection 방식도 사용된다. 단어 영역이 될 만한 Anchor Box(후보)를 만드는게 YOLO와 닮았다.

또는 단어 정합성을 높이기 위해 글자와 글자 사이 여백을 각각 탐지하고 하나의 단어 영역으로 합치는 방식의 알고리즘도 제안되었다.

<br>

#### **EAST: An Efficient and Accurte Scene Text Detector(Zhou. et al., 2017)**

#### Contribution

+ 빠른 속도
    - 기존 Text Detection 모델들이 3~5차례 Convolution 블록을 거치게 한 것과 달리 하나의 Convolution 블록만 거치도록 수정해 연산 시간을 대폭 단축함
+ 이미지 분할을 위해 고안된 Fully Convolution Network(FCN) 알고리즘을 활용해, 단어가 포함된 Rotated rectangle(직사각형) 또는 Quadrilateral box(사각형)를 예측함

<br><br>

## **#2. Recognition**

Recognition에서는 각 단어 영역에 어떤 문자가 쓰여있는지 판단하는 **Classification**을 수행한다. 이를 위해, 주로 **RNN**을 이용하여 단어 영역에 해당하는 이미지로부터 특징을 추출하고 **Sequential**하게 각 글자를 찾는 방식을 택한다.

<br>

#### **RNN을 사용하는 이유?**

STR에서 주로 다루는 이미지는 글자의 크기, 배치 등이 전부 다르다. 따라서 단어를 글자 단위로 정확하게 나누기 어렵다. 또한 Feature map의 부분적인 정보만을 이용해 글자-token을 에측하려면 앞뒤 token의 정보를 종합적으로 고려해야 하기 때문에 RNN을 이용해 Image-to-text translation을 수행한다.

물론 RNN의 성능에 한계가 있기 때문에... 최근에는 Attention 기반 Text recognition 모델도 주목받고 있는 듯 하다.

<br>

#### **An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition(Shi. et al., 2015)**

#### Contribution

+ CNN과 RNN을 결합한 초기 C-RNN 모델을 제안함

#### Feature
+ Convolution -> Feature map
+ Translate feature maps to column-vectorized sequence
+ By using bi-LSTM model, print the prediction word value of each vectors
    + bi-LSTM model: 길이가 긴 시퀀스 입력을 gradient vanishing 문제 없이 처리할 수 있음
+ CTC 알고리즘을 이용해 중복 문자와 공백을 제거하고 최종 단어 예측값을 출력함
    + Connectionist Temporal Classfication(CTC): 음성인식 모델에서 사용되는 알고리즘으로, 시퀀스 분류 모델 맨 마지막에 Loss 및 Gradient 계산 레이어 역할을 함
    + 어떤 출력값을 선택/제거할지에 대한 정보가 없는 상황에서 true label을 만드는 모든 case에 대해 분류 확률을 최대화(Maximum Likelihood)하도록 모델을 학습함

<br><br>

## **3. End-to-End**

<br>

#### **FOTS: Fast Oriented Text Spotting with a Unified Network(Liu. et al., 2018)**

#### Contribution

+ EAST와  C-RNN 모델을 하나로 합침.
+ 짧은 연산 시간
    + EAST와 C-RNN을 단순히 이어붙이지 않고, 한 번의 Feature extraction으로 Detection과 Recognition을 수행하기 때문

#### Feature

+ RoI Rotate Block
    + Text Detection Branch에서 출력된 RoI를 Recognition을 수행하기 쉽도록 수평으로 회전하는 등 전처리를 해줌
+ Shared Convolutions