# (Paper Review) MOTR: End-to-End Multiple-Object Tracking with TRansformer

> [MOTR: End-to-End Multiple-Object Tracking with TRansformer (paper)](https://arxiv.org/abs/2105.03247)   

> Official Code -> https://github.com/megvii-model/MOTR


## 용어 해설

+ **MOT**(**M**ultiple **O**bject **T**racking)   
    : **다중 객체 추적**은 VOD(Visual Object Detection, 시각적 객체 탐지(혹은 인식))의 일종으로, 각 프레임의 모든 타겟(객체)의 위치를 찾을 뿐만 아니라 전체 비디오 흐름에서 해당 객체들의 동선을 예측하는 것
    + 대부분 tracking-by-detection 패러다임을 따라 접근해왔음   
        + Object Localization & Temporal Association 파트로 나뉨
        + Object Localization - 프레임마다 객체를 인식
        + Temporal Association - _spatial similarty_ 기법(IOU기반) / _appearance similarity_ 기법(Re-ID기반) 등을 이용해 객체 이동 경로를 예측
        + Temporal Association Methods가 heuristic하고 간단해 복잡한 객체는 모델링하지 못함 => **한계점**    

+ **MOTR**(**MOT** with T**R**ansformer)   
    : thr first fully E2E MOT framework

+ **Transformer**   
    : 

    > [**_Attention Is All You Need_**](https://arxiv.org/abs/1706.03762)

    >    

    + 

+ **DETR**(**DE**tection **TR**ansformer)   
    : **Object query**라는 개념을 제안함   
    + Object query: explicit하고 decoupled된 객체 표현 방식   
    + ~~CNN과 Transformer, Hungary matching을 결합?~~

+ **"track query"**   
    : **DETR**의 **Object query** 에서부터 확장된 개념   

    > Each track query models the entire track of an object. It is transferred and updated frame-by-frame to perform object detection and tracking, in a seamless manner.   

    > Motivated by DETR’s great success, this work extends the “object query” concept to model object tracking, called track query in this work. Each track query is responsible to predict an entire track of an object.
    + 하나의 track query가 객체 하나의 전체 track을 모델링(예측)함
    + track query set이 `decoder_network`에 input으로 들어감   
    -> 현재 프레임에 대한 추적을 예측함   
    -> `decoder_network`의 output으로 나옴으로써 track query를 업데이트   
    -> 업데이트된 track query는 다음 프레임의 `decoder_network`를 통과함   
    -> 전체 영상에 대해 각 프레임마다 이러한 **query passing & prediction**이 반복됨   
    = **_Continuous Query Passing_**
    + 일단 쿼리 하나가 객체 하나와 매칭되면 그 쿼리는 동일한 객체를 계속 따름   
    -> temporal association이나 NMS같은 수작업을 하지 않아도 됨
    + 이러한 long-range temporal relation을 모델링하기 위해 다음과 같은 개념을 소개함   
        + Multi-frame Training
        + **TAN**(**T**emporal **A**ggregation **N**etwork)   

+ **TAN**(**T**emporal **A**ggregation **N**etwork)   
    : 비디오 영상의 객체를 추적한 각 qurey들의 내역으로부터 **query memory bank**를 구성함   
    + 현재 프레임의 track query는 **multi-head attention**을 통해 memory bank의 각 query와 상호작용함

## MOT 관련 연구
1. **Tracking-by-Detection paradigm**   

2. **SORT**   
    + Kalman Filter와 Hungarian algorithm을 결합시켜 IOU distance로 bounding boxes를 추적함   

3. **DeepSORT** and Tracker
    + Extra cosine distance를 소개함
    + Extra cosine distance: temporal association의 extracted appearance features에 기반하여 계산   

4. **Track-RCNN**, **JDE**, and **FairMOT**
    + Joint training framework의 객체 detector의 top에 Re-ID 브랜치를 추가함   

5. **TransTrack** and **TrackFormer**   
    + MOT를 위한 Transformer 기반 프레임워크인건 동일함
    + 그러나 **TransTrack**은 그저 detection과 tracking을 결합시킨 형태(?)
    + **TrackFormer**도 fully E2E 프레임워크가 아니며, NMS같은 작업이 필요함


## 