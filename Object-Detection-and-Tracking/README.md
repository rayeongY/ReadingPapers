# Enhancing Automatic Martitme Surveillance Systems with Visual Information
\- Scientific Figure on ResearchGate.

+ [Original Paper Link](https://www.researchgate.net/figure/Horizon-line-detection-a-Source-image-b-Extracted-Hough-lines-c-Sample-points-used_fig3_306128987) \[accessed 30 Jul, 2021]   

***

## 1. INTRODUCTION   

세계의 선박 트래픽이 증가함에 따라 해안 지역에 대한 자동 감시가 중요해지고 있다. 유조선, 컨테이너선, 그리고 벌크선은 최근 가장 중요한 교통 수단 중 하나이기 때문이다. ~~\~대충 선박 관제 시스템의 필요성 언급\~~~   

강력한 해상 관제 시스템을 개발하기 위해선 다양한 센서로 얻은 데이터를 모으고 합해야 한다.   

+ AIS(Automatic Identification System) data   
+ SAR(Synthetic Aperture Radar) data  
+ buoy-mounted sensors with land radars
+ visual-with radar-based surveillance
+ multiple ship-based sensors

등...   
본 논문에서는 radar&AIS 데이터로 구성되며 촬영 거리가 긴? EO(Electro-optical) 카메라와 IR(Infra-red) 카메라를 장비로 사용하는 VTS(Vessel Traffic Services) 시스템에 초점을 두었다.   

(중략)   

본 논문에서 제안하는 시스템 구조의 디자인은 다음과 같다:

1. EO와 IR 이동 카메라에서도 동작하는 **classifier-based method**로 보트 감지
2. 복수의 선박을 추적(겹쳐있을 때도)
3. 현재 존재하는 VTS 시스템에서 얻은 데이터 혼합(?)
4. 객체가 밀집된 지역에도 배포될 수 있음

이하로 간략하게 목차를 설명

+ Ⅱ - Discuss Related work
+ Ⅲ - System Overview
+ Ⅳ - Detail Vessel Detection with Moving and Zooming cameras
+ Ⅴ - Description of Tracking and Data fusion
+ Ⅵ - the Experimental results
+ Ⅶ - Conclusions

___

## 2. RELATED WORK

해상 영역 관제 시스템에는 다음과 같은 도전과제가 있다:

+ 매우 넓은 모니터링 영역
+ 날씨 이슈(폭우, 안개)
+ 적절한 센서가 필요한 심야 모니터링
+ 객체마다 천차만별인 크기
+ 부분 노출, 겹침을 고려한 다중 객체 추적

이런 과제를 극복하고 해상 영역을 정확하고 효과적으로 모니터링하기 위해, 최대한 많은-다양한 정보 출처에서 데이터를 수집해야 한다. 현재 해상 관제에 대한 정보 혼합 솔루션을 해결하고자 하는 접근은 각 환경에서 사용되는 주 센서의 특성에 따라 분류할 수 있다.

+ ⅰ) radar
+ ⅱ) satellite
+ ⅲ) sonar
+ ⅳ) camera

본 논문에서는 시각 정보를 사용할 수 있는 시스템에 초점을 맞추므로, 여기서는 카메라-기반 솔루션에 대해 논한다.

### Camera-based systems.

> I. Bechar, F. Bouchara, T. Lelore, V. Guis, and M. Grimaldi, “Uncertainty fusion based object recognition and tracking in maritime scenes using spatiotemporal active contours,” in International Conference on Computer Vision Theory and Applications, vol. 1, 2014, pp. 682–689.

-> Addressed the problem of near real-time video analysis of a 해상 영상

> S. Fefilatyev, D. Goldgof, and C. Lembke, “Tracking ships from fast moving camera through image registration,” in International Conference on Pattern Recognition, 2010, pp. 3500–3503.

-> Used HOG(Histogram of Oriented Gradients). 

> F. Bousetouane and B. Morris, Off-the-Shelf CNN Features for Fine-Grained Classification of Vessels in a Maritime Environment. Springer, 2015, pp. 379–388.

-> Bousetouane and Morris investigated the use of CNNs for vessel classification.

> B. Qi, T. Wu, B. Dai, and H. He, “Fast detection of small infrared objects in maritime scenes using local minimum patterns,” in International Conference on Image Processing, 2011, pp. 3553–3556.

-> Proposed an approach-based on the LMP(Local Minimum Patterns)- for fast detecting small maritime objects in IR images.

___

## 3. SYSTEM OVERVIEW

### Modules of the proposed architecture

+ EO/IR 카메라
+ **카메라 컨트롤** 모듈
+ **Video Processing Unit**(VPU)
    + Visual Detection
    + Visual Tracking
+ **데이터 혼합** 모듈

___

## 4. VISUAL DETECTION

본 프로젝트의 **VPU**(영상 처리 유닛)은 다음 세 가지 요소로 구성되어있다.

+ Haar Classifier
+ the Horizon Line Detector
+ the Noise Filter

각 요소에 대해 알아보도록 한다.   

### Haar Classifier

저자들은 카메라가 사용자에 의해 자유롭게 움직일 수 있다고 가정했으므로, **foreground/background modelling**(e.g., background subtraction) 접근은 효과적이지 못하다고 판단했다.   
존재하는 다른 모델들과 다르게, Haar-like 특징을 기반으로 접근하는 모델[21]은 real-time에서도 동작한다.

> N. Dalal and B. Triggs, “Histograms of oriented gradients for human detection,” in International Conference on Computer Vision and Pattern Recognition, vol. 1, 2005, pp. 886–893.

> O. Tuzel, F. Porikli, and P. Meer, “Pedestrian detection via classification on riemannian manifolds,” Transactions on Pattern Analysis and Machine Intelligence, vol. 30, no. 10, pp. 1713–1727, 2008.

사용된 Haar-like 특징은 총 세 가지로, 다음과 같다.

+ Edge
+ Line
+ Center-surround features

단, Haar classifier는 원래 얼굴 인식에 맞게 디자인되었으므로 저자들은 선박 인식에 적당하도록 모델을 수정하였다.

+ 정사각 대신 60 x 30 직사각 window를 사용
+ 배가 지나간 흔적을 줄이기 위해 median filtering을 사용하여 입력 이미지를 smoothing함
+ thresholds 값 (생략)

unique binary classifier를 만들겠다고 생각함.   
마지막에는 OpenCV 함수인 `opencv_traincascade`를 이용해서 classifiers를 계단식으로 훈련시킴.   

학습 단계는 24레벨에서 종료되었고, **크기가 다른 보트 인식**, **선박이 지나간 흔적(물결)**과 **수면에 반사되는 그림자** 등의 noise를 blurring할 수 있었음.
   
### Horizon Line Detector

해안 영역을 인식하는 것은 false positive를 걸러내는 데에 매우 유용하다.   
이미지에서 edge가 추출되면, Hough transform을 적용함으로써 line 후보를 뽑을 수 있다.   

(...후보line의 valid/invalid를 판별하는 알고리즘...)   
(아마 후보line의 위쪽과 아래쪽 영역에 대해 grayscale 밀도값을 계산해서 그 차가?값이? 90% 이상? 다르다면 valid한-바다경계-선이라고 판단)

카메라의 방향에 따라, horizon line detector는 **바다-하늘 line**과 **바다-해안가 line**을 구분해야 한다. 실제로, 프레임 안에 해안가가 등장할 경우 false positive를 찾아낼 가능성이 증가한다. 배경(해안가)의 높은 텍스쳐 때문이다. 따라서 **바다-해안가 line**보다 위에서 인식된 선들은 필터링하는게 편하다.


### Noise filter

파도와 선박의 궤적 등으로 발생하는 false positive를 걸러내기 위해, classifier에 추가 level을 만들었다. 이 level은 특수한 4000개의 negative 이미지 셋(기존 24층 classifier가 만들어낸 포말 및 기타 false positive가 인식된 이미지)을 이용했다.   

또한, 수면의 반사로 인한 false observation을 피하기 위해 **SURF의 개수**를 계산해 필터링을 진행했다.   
사실 SURF detection에서 항상 거울 반사(전반사?)가 발생하지는 않기 때문에, 수면 반사로 형성된 key points의 개수는 일반적으로 제한된다(?)


### IR data

EO(광학) 이미지에 사용된 classifier로 IR 데이터 역시 테스트했다. 이때 해안선 감지 결과를 향상시키기 위해 IR data만의 고유한 전처리 단계를 거쳤다.

+ 이미지 scale 및 shift를 위해 **normalization step**을 거침
+ Classifier는 오직 EO 이미지로 학습되었지만, IR 이미지에도 성공적으로 적용됨
    + 이는 Haar-like 특징 기반 접근이 선박 감지에 효과적이라는 가설에 힘을 실어줌
+ Classifier 기반 탐지 접근 방식을 취함으로써 열영상에서 발생하는 미보정 극성 및 후광(?) 문제를 피할 수 있음
    +  미보정 극성 및 후광 문제는 thresholding 기반 방법에서 문제를 일으킴


## 5. TRACKING AND DATA FUSION

본 논문은 기존 VTS 함수를 향상시키는 것을 목표로 두고 접근한다.   
이에 아래와 같은 접근 방식을 제안한다.

+ 분산 추적 접근 방식(distributed tracking approach)
    + 다중, 다형의, 획일되지 않은 sources의 데이터를 혼합할 수 있음
    + Input observations = VD(Visual Detection) 모듈의 Output

    + 

+ 다중 센서 시나리오 PTracking 기법으로 확장함
    + [PTracking](https://github.com/fabioprev)
    + PTracking: Distributed Multi-Clustered Particle Filtering에 기반한 오픈소스 추적 알고리즘
    + 
    + 각 포지션 x_s,t는 Gaussian Mixture Method(GMM)의 벡터로 주어짐
        + 
    + 계산 과정은 크게 3단계로 이루어짐
        1. Prediction step
        2. Clustering step
        3. Data association step