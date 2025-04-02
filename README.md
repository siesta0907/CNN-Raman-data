# CNN 기반 라만 분광 데이터 분석

## 1. 연구 목적

본 연구는 이산화탄소(CO₂)의 광환원 반응 후 생성된 물질들의 라만(Raman) 분광 데이터를 기반으로,  
구리 황화물(CuS)과 구리 기반 촉매 표면 위의 반응 특성과 생성물을 딥러닝을 통해 분석하는 것을 목표로 하였습니다.  
특히, Microalgae(미세조류)의 존재 유무가 Cu 및 CuS 촉매에서의 반응 특성에 어떤 영향을 주는지를  
CNN(Convolutional Neural Network)을 통해 분류하였습니다.

---

## 2. 데이터 구성 및 전처리

- **데이터 출처**:  
  - 구리 기반 시료: Cu1,2,4,5  
  - Microalgae가 포함된 시료: CuNCs-Microalgae

- **전처리 과정**:
  - 이상치 및 결측치 제거 ('NaN', 'inf', '-nan(ind)')
  - Raman Shift 기준 정렬
  - 공통 Raman Shift 범위(100–3000 cm⁻¹)에 대해 **1500 포인트 보간**
  - **정규화**: Min-Max 또는 Standard Scaling
  - **PCA** 적용: 95% 설명력을 가지는 차원까지 축소

---

## 3. 모델 구조 및 학습 설정

- **모델 아키텍처 (1D CNN)**:
Input → Conv1D(64) → BatchNorm → MaxPooling → Dropout(0.4) → Conv1D(128) → BatchNorm → MaxPooling → Dropout(0.5) → Flatten → Dense(64) → Dropout(0.5) → Dense(1, sigmoid)


- **Regularization**:
- L2 정규화 (λ = 0.02)
- Dropout: 0.4–0.5

- **학습 설정**:
- Optimizer: Adam (Learning Rate = 0.0001)
- Loss: Binary Crossentropy
- Epochs: 최대 100회 (EarlyStopping: patience=10)
- Batch Size: 32
- 학습/검증 비율: 80:20 (Stratified Split)

---

## 4. 모델 성능 결과

### Cu 데이터 기준 (정규화 + PCA 포함)

- **Training Accuracy**: 0.9749  
- **Validation Accuracy**: 0.9794  
- **Training Loss**: 0.3287  
- **Validation Loss**: 0.3000  
- **ROC AUC Score**: 0.9989

> 높은 정확도와 AUC 값은 모델이 Microalgae의 존재 유무에 따른 라만 스펙트럼 차이를 효과적으로 학습했음을 의미합니다.

---

### CuS 데이터 기준 (정규화 및 PCA 생략)

| Epoch | Train Accuracy | Train Loss | Val Accuracy | Val Loss |
|-------|----------------|------------|--------------|----------|
| 1     | 0.8500         | 5.1084     | 0.9808       | 4.2058   |
| 2     | 0.9968         | 3.9520     | 0.9840       | 3.7862   |
| 3     | 1.0000         | 3.5848     | 0.9904       | 3.3845   |
| 4     | 0.9963         | 3.2761     | 1.0000       | 3.0519   |
| 5     | 0.9996         | 2.9918     | 0.9840       | 2.8877   |

- **최종 Training Accuracy**: 0.9992  
- **최종 Validation Accuracy**: 0.9840  
- **최종 Training Loss**: 2.9323  
- **최종 Validation Loss**: 2.8877

> PCA나 정규화 없이도 높은 분류 정확도를 보이며, CuS 라만 스펙트럼만으로도 Microalgae의 영향을 판별 가능함을 시사합니다.

---

## 5. 시각화 결과

![image](https://github.com/user-attachments/assets/78da65fe-f4f1-442c-b31d-c3e932491b81)
![image](https://github.com/user-attachments/assets/3408bd41-e71d-45cb-a5a8-fb409be83c60)
![image](https://github.com/user-attachments/assets/39927e20-d62e-4379-a3ea-9291f881041c)
![image](https://github.com/user-attachments/assets/e44ff330-8353-4dd0-ab22-f57e1d2e22a3)

---

## 6. 결론 및 향후 방향

- CNN 기반 딥러닝 모델은 라만 분광 데이터를 통해 촉매 반응의 화학적 차이를 효과적으로 학습할 수 있음을 입증하였습니다.
- 특히 Microalgae의 존재 유무에 따른 Cu 및 CuS 표면 반응의 차이를 정량적으로 분류 가능하였으며,
  이는 향후 광촉매 개발, 바이오-무기재료 융합 연구, AI 기반 스펙트럼 분석 등에 활용될 수 있습니다.
- 향후에는 다양한 촉매 물질, 실험 조건, 다중 레이블 분류 모델 등으로 확장하여 더욱 정교한 해석이 가능할 것으로 기대됩니다.

---

## 참고

- 데이터 파일 형식: `.txt` (Raman Shift, Intensity)
- 분석 도구: Python, TensorFlow, scikit-learn, Matplotlib, Seaborn
