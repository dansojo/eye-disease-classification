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

### 1. 클래스별 이미지 분포 (원본 + 증강 데이터)
- 데이터셋은 원본 데이터와 증강 데이터를 합쳐 총 **21,577장**으로 구성됨.
- 클래스 간 데이터 편차가 심하게 나타남.

#### 📊 클래스별 이미지 개수

- 가장 적은 클래스: **Pterygium (119장, 0.55%)**
- 가장 많은 클래스: **Diabetic Retinopathy (4,953장, 22.95%)**

📌 **데이터 불균형이 심각하게 나타남 → 불균형 해소 기법 필요 (Class Weighting, Oversampling, Focal Loss 등)**

#### 📊 시각화: 클래스별 이미지 개수 (Combined Dataset)
![Image](https://github.com/user-attachments/assets/44af4fb8-5c35-4795-af3c-d92db750a8d9)

---

#### 📊 시각화: 클래스별 비율 (Combined Dataset)
![Image](https://github.com/user-attachments/assets/5d06382e-cf60-4252-8a24-5ce989ec5bfe)

---

### 2. 클래스별 증강 비율 분석 (Augmentation Analysis)**

- **목적:** 원본 데이터 대비 증강 데이터가 각 클래스별로 얼마나 추가되었는지 확인.  
- **분석 결과:** 증강 비율이 클래스별로 크게 다름.
  - **증강 비율이 높은 클래스:** Pterygium, Retinitis Pigmentosa 등 (600% 증가)
  - **증강 비율이 낮은 클래스:** Diabetic Retinopathy, Glaucoma 등 (200~400% 증가)
- **시각화 방식:** 원형 그래프 (Pie Chart) & 막대 그래프 (Bar Plot)

📌 **증강 기법이 클래스 불균형 해소에 충분히 기여했는지 확인하기 위한 분석**

#### 📊 시각화: 클래스별 증강 비율 (Bar Plot)
![Image](https://github.com/user-attachments/assets/cbf5b70b-a39b-4443-8b1b-2eca86f54456)

#### 📊 시각화: 전체 데이터 구성 (Pie Chart)
<img src="https://github.com/user-attachments/assets/8f2b5d7d-dc9b-4832-a92a-41ccedc6c38f" style="width: 25%;">

---

### 3. 클래스별 평균 밝기 분석
- 평균 밝기 범위: `77.9 ~ 112.2`
  - 가장 어두운 클래스: **Retinal Detachment (77.9)**, **Disc Edema (78.3)**
  - 가장 밝은 클래스: **Macular Scar (112.2)**, **Healthy (108.2)**

📌 **밝기 값의 차이가 클래스마다 뚜렷하게 존재**하며, 모델 학습에 영향을 줄 가능성이 큼.  
📌 특히, **밝기가 낮은 클래스 (Retinal Detachment, Disc Edema)** 에 대한 추가적인 밝기 정규화 고려 필요.  
📌 CNN 모델이 어느 정도 밝기를 자동 보정할 수 있지만, **명확하게 차이가 나는 경우 보정 작업이 유효할 수 있음.**  

📌 **따라서, 밝기 정규화를 추가하는 것이 바람직할 수 있음.**  

#### 📊 시각화: 클래스별 평균 밝기
![Image](https://github.com/user-attachments/assets/23aa5751-3f6f-48c7-9319-86b64ae46a7a)

---

### 4. 클래스별 대비(Contrast) 분석
- 대비 값의 범위: `53.3 ~ 74.0`
  - 가장 낮은 대비: **Retinitis Pigmentosa (54.0)**, **CSC (54.0)**, **Disc Edema (53.3)**
  - 가장 높은 대비: **Pterygium (74.0)**

📌 **대부분의 클래스는 대비 값이 비슷하게 나타나므로 특별히 대비를 조정할 필요는 없음.**  
📌 일부 클래스에서 대비가 높게 나타나지만, 학습에 큰 영향을 줄 가능성은 낮음.  
📌 따라서, **Histogram Equalization을 적용하지 않기로 결정.**  

#### 📊 시각화: 클래스별 대비 값
![Image](https://github.com/user-attachments/assets/54df18dd-a510-4810-993f-6fbd2bc1ae04)

---

### 5. 중복 이미지 확인 (Original + Augmented Dataset)
- 총 **1,395장 (6.47%)** 중복 이미지 확인됨  
- 클래스별로 중복 이미지가 심각하게 발견된 경우:
  - **Healthy:** 477장 (17.83%)
  - **Macular Scar:** 354장 (18.28%)
  - **Glaucoma:** 420장 (14.58%)
- 반면, 일부 클래스는 중복 비율이 현저히 낮음 (예: Myopia, Retinal Detachment)
- **중복 이미지가 특정 클래스에 집중되는 경향을 보임.**  
  - 데이터 수집 또는 증강 과정에서 발생했을 가능성이 높음.
  - 모델 학습 과정에서 문제를 일으킬 수 있는 잠재적 위험 요소로 판단됨.

---

### 6. RGB 채널 분석
- **Red 채널 비중이 가장 높음.**
  - 픽셀 값이 높은 영역 (약 100~130)에 집중되는 경향이 있음.
- **Green 채널과 Blue 채널은 상대적으로 낮은 값에 분포.**
  - Green 채널: 값이 고르게 분포되지만, 주로 낮은 값 (~100 이하)에서 많이 나타남.
  - Blue 채널: 전반적으로 낮은 값 (0~50)에 집중되어 있음.
- **전체적으로 RGB 채널 간 분포가 균형 있게 나타나지 않음.**
  - Red 채널의 값이 다른 두 채널보다 상대적으로 높고 편향되어 있음.

| 기법                   | 적용 여부 | 이유|
|------------------------|-----------|----------------------------------------------------------------------------------------|
| Grayscale 변환         | ❌ | Red 채널의 중요한 정보가 손실될 가능성이 있으므로 RGB 상태로 유지.|
| RGB 정규화             | ✅| RGB 채널 간 분포 차이를 줄이기 위해 Normalize 적용. |

#### 📊 시각화: 클래스별 RGB 평균값
![Image](https://github.com/user-attachments/assets/11f37930-1ef7-472e-9278-8740f85ab197)

---

## 🚫 제외된 전처리

| 기법 | 적용 여부 | 이유 |
|------|-----------|------|
| Grayscale 변환 | ❌ | 정보 손실 우려 |
| Histogram Equalization | ❌ | CNN 내부에서 대비 보정 가능 |
| 중복 이미지 제거 |🔍 검토 필요| 중복 이미지 비율이 높으며, 일부 클래스에서 심각하게 중복 발생|
| RGB 채널 정규화 | ✅ | RGB 채널 간 값의 분포 차이를 줄이기 위해 Normalize 적용 필요 |

### 🔍 중복 이미지 제거에 대한 검토 필요
- 특히, **Healthy, Macular Scar, Glaucoma** 클래스에서 심각하게 나타남.  
- 따라서, **다음 단계에서 중복 이미지 제거 전/후 비교 실험을 추가로 진행**할 필요가 있음.

---

## 🔧 향후 증강 전략 (설계 방향)

- 증강 데이터에 이미 적용된 기법은 재적용 금지
- 추가적으로 고려할 수 있는 기법:
  - **Brightness / Contrast 조정** (클래스 특성 기반)
  - **Gaussian Noise 추가**
  - **MixUp / CutMix**
  - **클래스별 증강 강도 차등 적용**
- **중복 이미지 제거 실험 추가:**  
  - 중복 제거 전후의 모델 학습 결과를 비교하여 효과를 검증할 필요가 있음.

---

## ✅ EDA 결론 요약

- **클래스 불균형 발견 → Focal Loss, Class Weight, Oversampling 등 적용 고려**
- **밝기 / 대비 조정은 생략**, CNN이 자동 처리 가능
- **불필요한 전처리 생략 (Grayscale 변환, Histogram Equalization 등)**
- **중복 이미지 문제 확인 (6.47%) → 중복 이미지 제거 실험 필요**
- **기존 증강 기법 중복 적용 금지**

---

## 🔜 다음 단계

[👉 2단계 - 모델 실험 및 불균형 해소 기법 비교](../2_Model_Experiments/README.md)

