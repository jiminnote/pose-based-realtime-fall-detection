# pose-based-realtime-fall-detection

Pose Detection 기반 실시간 낙상 감지 시스템  
AI Hub 낙상사고 동작 데이터셋을 활용한 시계열 기반 딥러닝 모델링

---

## 프로젝트 개요
시계열 기반 Pose 변화를 분석하여 낙상 위험을 조기 감지  
▶︎ 고령자 돌봄 자동화 및 의료·복지 시설의 안전사고 대응 효율화

### 목적
- 낙상 이벤트를 빠르게 감지/대응
- 불필요한 오탐(일상 행동을 낙상으로 오판) 최소화
- 데이터 기반 안전 모니터링 파이프라인 구축

---

## 데이터셋 정보
- 출처: 낙상사고 위험동작 영상-센서 쌍 데이터 (AI Hub)
- 형식: 비디오 / 센서 / 이미지
- 라벨링: JSON (낙상 구간 프레임 정보 포함)
- 규모: 영상 22,672 클립 / 이미지 226,720 장 (약 494GB)

---

## EDA 핵심 인사이트 (Sensor / Pelvis Acc)
`EDA/07_EDA_SY_label_verification.ipynb` 기준

### 1) 정상(N/N) 시퀀스에는 near-fall 패턴 포함
- **순간적인 큰 spike 이후 빠르게 안정화**
- 낙상 여부를 “스파이크 유무”만으로 판단하면 오탐 가능

### 2) 낙상(Y/SY)은 이벤트 이후 변동이 지속되는 경향
- 이벤트(최대 |가속도|) 시점을 0초로 정렬(alignment)했을 때,
- **낙상은 이후 약 2초 구간에서 변동(variance)이 더 오래 유지**되는 패턴이 관찰됨

### 3) PPT용 시각화 산출물
- 골반 가속도를 이벤트 기준 정렬 + 0~2초 강조
- 저장 파일: `EDA/exports/pelvis_acc_nearfall_vs_fall.png`

### 4) Sensor2 단일 채널 베이스라인(정규화 + 길이통일 후)
- z-score 정규화 + 리샘플링(고정 길이) 후 간단 통계/분포 비교
- Logistic Regression 기반 교차검증으로 “Sensor2만으로도 분류 신호가 존재” 여부 확인

---

## 모델링 전략
1. **LSTM 기반 Baseline 구축 (Pose sequence)**
2. **ST-GCN 기반 고도화 (Skeleton graph)**

---

## LSTM Baseline 전처리/시퀀스 생성 (Pose)
`LSTM/01_make_sequence.ipynb` 기준

### 입력 데이터
- `data/pose/{scene_id}.npy`
- pose shape: **(NUM_FRAMES=100, NUM_KEYPOINTS=17, 3)**  
  - (x, y, confidence)

### 프레임 길이 통일
- `fix_frame_length()`로 100 프레임 기준
  - 길면 균등 샘플링, 짧으면 zero-padding

### Feature 구성 (D = 85)
프레임별로 아래를 concat하여 85차원 벡터 생성:
- keypoint (x, y): 34
- confidence: 17
- velocity (dx, dy): 34  
추가 정규화:
- hip center 기준 중심 정렬
- shoulder-hip 거리 기반 scale 정규화

### 시퀀스 생성 (Sliding Window)
- FPS = 10, NUM_FRAMES = 100
- **T = 40 (4초), STRIDE = 3**
- 라벨: scene-level (`class == "Y/SY"`)을 시퀀스에 부여
- 저장:
  - `data/sequence/X.npy`  (shape: N x T x 85)
  - `data/sequence/y.npy`  (shape: N)

---

## 성능 평가 지표
- Sensitivity(민감도): 실제 낙상을 얼마나 잘 감지했는가
- Specificity(특이도): 비낙상을 낙상으로 오판하지 않는가
- Accuracy(정확도): 전체 데이터에서 올바르게 판단했는가
- (선택) ROC-AUC: 임계값 변화에 따른 분류 성능 종합 지표

---

## 폴더 구조(요약)
- `EDA/` : 라벨/센서/패턴 검증 및 시각화, PPT용 figure export
- `LSTM/` : Pose 기반 시퀀스 생성 및 LSTM 베이스라인
- `data/pose/` : Pose npy 파일
- `data/sequence/` : 학습용 시퀀스(npys) 출력

---

## 팀 정보
- 제로베이스 AI 부트캠프 4인 팀 프로젝트
- 기간: 2025년 12월 (총 4주)

---

## 참고 문헌
- [Deep Learning Based Systems Developed for Fall Detection: A Review](https://ieeexplore.ieee.org/abstract/document/9186685)
- [AI hub 낙상사고 위험동작 영상-센서 쌍 데이터](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=71641)