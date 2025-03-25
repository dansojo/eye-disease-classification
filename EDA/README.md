# 📊 1단계: EDA 및 증강 전략 설계

본 단계에서는 Eye Disease Image Dataset의 구조와 특성을 분석하고,  
모델 학습에 최적화된 증강 및 전처리 전략을 수립하는 것을 목표로 합니다.

---

## 📁 데이터셋 개요

- **데이터셋 이름**: Eye Disease Image Dataset (Augmented Version)
- **총 이미지 수**: 21,577장
  - 원본 이미지: 5,335장
  - 증강 이미지: 16,242장
- **클래스 수**: 10개 (9개 질환 + Healthy)
- **데이터 출처**:  
  [Mendeley Data - Eye Disease Image Dataset](https://data.mendeley.com/datasets/s9bfhswzjb/1)  
  - 수집 기관: Anwara Hamida Eye Hospital, BNS Zahrul Haque Eye Hospital (Bangladesh)
  - 제공 기관: Daffodil International University, Jahangirnagar University

---

## 🧪 증강 방식 상세

- 증강된 16,242장의 이미지는 다음의 기법을 통해 생성됨:

> **Rotation**, **Width Shifting**, **Height Shifting**,  
> **Translation**, **Flipping**, **Zooming**

- 해당 기법들은 원본 이미지 5,335장을 기반으로 증강하여 데이터의 다양성과 균형 확보를 목표로 함
- 향후 추가 증강 시, **중복 증강 적용을 피하도록 설계할 예정**

---

## ✅ EDA 분석 항목 요약

### 1. 클래스별 이미지 분포
- 클래스 간 데이터 편차 심함
  - 가장 적은 클래스: **Pterygium (102장, 0.63%)**
  - 가장 많은 클래스: **Diabetic Retinopathy (3444장, 21.2%)**

📌 **데이터 불균형 심각 → 불균형 해소 기법 필요**

#### 📊 시각화: 클래스별 이미지 개수
<img src="./images/class_ratios.png" width="600">

---

### 2. 클래스별 평균 밝기 분석
- 평균 밝기 범위: `71.4 ~ 99.5`
  - 어두운 클래스: **Disc Edema**, **Retinal Detachment**
  - 밝은 클래스: **Retinitis Pigmentosa**

📌 CNN이 밝기 보정 가능 → 별도 정규화 X

#### 📊 시각화: 클래스별 평균 밝기
<img src="./images/class_brightness.png" width="600">

---

### 3. 클래스별 대비(Contrast) 분석
- 대비 낮은 클래스: **Disc Edema**, **CSC**, **Retinal Detachment**
- 대비 높은 클래스: **Pterygium**

📌 Histogram Equalization 검토했으나,  
CNN이 자동 조정하므로 **적용하지 않기로 결정**

#### 📊 시각화: 클래스별 대비 값
<img src="./images/class_calculate_contrast.png" width="600">

---

### 4. 중복 이미지 확인
- 총 **911장(5.6%)** 중복 확인됨
  - Myopia, Healthy, Macular Scar 클래스에서 주로 발견됨
- 전체 비율 낮고, 증강 과정 포함된 데이터이므로 **삭제하지 않고 유지**

---

### 5. RGB 채널 분석
- Red 채널 비중이 가장 높음
- 클래스 간 극단적인 편향은 아님

📌 **Grayscale 변환 및 RGB 정규화 생략**

#### 📊 시각화: 클래스별 RGB 평균값
<img src="./images/class_RGB.png" width="600">

---

## 🚫 제외된 전처리

| 기법 | 적용 여부 | 이유 |
|------|-----------|------|
| Grayscale 변환 | ❌ | 정보 손실 우려 |
| Histogram Equalization | ❌ | CNN 내부에서 대비 보정 가능 |
| 중복 이미지 제거 | ❌ | 비율 낮고, 실제 학습에 큰 영향 없음 |
| RGB 채널 정규화 | ❌ | 모델이 자동 학습 가능 |

---

## 🔧 향후 증강 전략 (설계 방향)

- 증강 데이터에 이미 적용된 기법은 재적용 금지
- 추가적으로 고려할 수 있는 기법:
  - **Brightness / Contrast 조정** (클래스 특성 기반)
  - **Gaussian Noise 추가**
  - **MixUp / CutMix**
  - **클래스별 증강 강도 차등 적용**

---

## ✅ EDA 결론 요약

- **클래스 불균형 심각 → Focal Loss, Oversampling 등 필요**
- **밝기 / 대비 조정은 생략**, CNN이 자동 처리 가능
- **불필요한 전처리 생략**
- **기존 증강 기법 중복 적용 금지**
- 향후 실험에 필요한 **기반 통계자료 확보 완료**

---

## 🔜 다음 단계

[👉 2단계 - 모델 실험 및 불균형 해소 기법 비교](../2_Model_Experiments/README.md)

