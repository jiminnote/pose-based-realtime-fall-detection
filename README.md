# pose-based-realtime-fall-detection

Pose Detection 기반 실시간 낙상 감지 시스템
AI Hub 낙상사고 동작 데이터셋을 활용한 시계열 기반 딥러닝 모델링


## 프로젝트 개요
시계열 기반 Pose 변화를 분석하여 낙상 위험을 조기 감지
▶︎ 고령자 돌봄 자동화 및 의료·복지 시설의 안전사고 대응 효율화


### 목적
- 고장 시점을 사전에 예측하여 정비 시점 최적화
- 불필요한 조기 교체 방지 및 비용 절감
- 안전성 확보 및 데이터 기반 의사결정 지원

## 데이터셋 정보

- 출처: 낙상사고 위험동작 영상-센서 쌍 데이터 (AI hub)
- 형식: 비디오,센서,이미지 데이터
- 라벨링 형식 : JSON
- 데이터 구축량 : 영상 22,672 클립 / 이미지 226,720 장 (약 494GB)
- 구성


## 모델링 전략

1. LSTM 기반 Baseline 구축
2. ST-GCN 기반 고도화


## 전처리 및 특징

| 모델 유형 | 주요 전처리 방식 |
|-----------|-----------------|
| 딥러닝 (LSTM) | baseline 낙상 패턴 학습 |
| Skeleton 기반 (ST-GCN) | 속도·각도·중심 이동 등 핵심 Feature 생성 |


## 성능 평가

- Sensitivity(민감도) - 실제 낙상 사고 발생했을때 이를 얼마나 잘 감지했는가
- Specificity(특이도) - 낙상이 아닌 일상 활동을 낙상이라고 오판단 하지는 않았는가
- Accuracy(정확도) - 전체 데이터 중에서 시스템이 올바르게 판단했는가


## 팀 정보

- 제로베이스 AI 부트캠프 4인 팀 프로젝트
- 기간: 2025년 12월 (총 4주)

## 참고 문헌
[Deep Learning Based Systems Developed for Fall Detection: A Review](https://ieeexplore.ieee.org/abstract/document/9186685)
[AI hub 낙상사고 위험동작 영상-센서 쌍 데이터](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=71641)
