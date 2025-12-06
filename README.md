# RL-vs-VLA-for-Autonomous-Cleaning-Manipulation

# 🧹 **자율 청소 수행을 위한 RL vs VLA 비교 연구**

**로봇 플랫폼:** Doosan Robotics E0509  
**도메인:** Manipulation, Digital Twin, Reinforcement Learning, VLA, Imitation Learning

---

## 📘 **1. 개요 (Introduction)**

로봇팔(e0509)이 테이블 위 물체를  
**인식 → 접근 → 파지 → 정리**하는 작업을 자율적으로 수행할 수 있도록 하기 위해  
두 가지 학습 방식을 실제로 적용하여 비교.

비교 학습 방식은 다음 두 가지:
- **강화학습(Reinforcement Learning, RL)**  
- **VLA(Visual-Language-Action) 기반 학습 모델(OpenPI)**

Digital Twin 환경에서 RL 정책을 먼저 학습시도한 후,  
한계를 확인하고 VLA 기반 접근으로 전환하며 차이를 분석하였다.

---

## 🎯 **2. 프로젝트 목표 (Project Objective)**

- Isaac Sim 기반 Digital Twin 환경에서 조작 정책 학습  
- e0509 실제 로봇으로 전이 가능한 행동 정책 확보  
- RL과 VLA가 복합 조작 작업에 어느 정도 적합한지 비교  
- 청소/정리 기반 조작 태스크에 최적의 학습 프레임워크 찾기  

---

## ⚙️ **3. 접근 방법 (Methodology Overview)**

### **3.1 강화학습(Reinforcement Learning, RL) **

- **시뮬레이터:** Isaac Sim + e0509 URDF  
- **태스크:**  
  “테이블 위 임의 위치의 물체를 인식 → 접근 → 파지”
- **사용 알고리즘:** SAC  
- **핵심 엔지니어링 작업:**  
  - Reward Shaping  
  - Domain Randomization  
  - Policy Stability 분석  
  - Continuous Action Space 튜닝  
---

### **3.2 VLA(Visual-Language-Action) **

모방학습 기반 VLA 모델인 **OpenPI pi05-droid**로 행동 정책을 학습.

- **선택 이유:**  
  - Franka 기반 단일 로봇팔 학습 → e0509와 구조적 패턴 유사  
  - 최신 모델로 일반화 성능 강화  
  - Vision + Language + Action 통합 구조 → 조작 작업에 적합  

- **학습 전략:**  
  1) Short Full Fine-Tuning  
  2) LoRA 기반 Incremental Fine-Tuning  

---

## 🧪 **4. 강화학습 기반 행동 정책 학습 (RL-Based)**

### **4.1 환경 구성 (Environment Setup)**

- Isaac Sim 내 e0509 작업 공간 Digital Twin 구성  
- 테이블 위 물체 랜덤 생성(Random Spawn)  
- Collision Layer, Material 튜닝  

### **4.2 보상 설계 (Reward Function Design)**

| 보상 요소 | 설명 |
|-----------|-------|
| + 접근 보상 | 목표 물체 방향으로 이동 시 보상 |
| + 파지 보상 | 파지 성공 시 높은 보상 |
| - 충돌 패널티 | 충돌 또는 부적절한 움직임 |
| - 대기 패널티 | Idle 상태 지속 시 패널티 |

### **4.3 실험 결과 (Findings)**

- 접근 행동은 어느 정도 학습  
- **파지·정리까지 연결된 정책은 학습 실패**  
- 랜덤성 증가 시 성공률 급락  
- Reward 구조 변화에 지나치게 민감  
- 고차원 연속 제어 → 학습 불안정

### **4.4 RL 접근의 한계 (RL Limitations)**

- Sparse Reward 문제  
- 높은 학습 시간 / GPU 비용  
- 실환경 일반화 성능 매우 낮음  
- 복합 조작 태스크는 RL 단독으로 해결하기 어려움

> **결론: RL만으로는 실사용 가능한 청소 정책 확보가 어렵다.**

---

## 🤖 **5. VLA 기반 행동 정책 학습 (VLA-Based )**

### **5.1 OpenPI pi05-droid 선택 이유**

- 단일 로봇팔 조작 데이터 기반 → e0509와 동작 패턴 유사  
- 최신 LLM/VLM 계열로 일반화 성능 우수  
- 이미지 + 언어 + 행동을 한 모델에서 처리  

### **5.2 데이터 전략: “청소 작업” 집중**

- 반복 가능한 패턴 → 고품질 데모 수집 쉬움  
- 실사용 태스크와 시연 효과 모두 큼  
- 이후 다양한 조작 태스크로 확장 가능

### **5.3 학습 파이프라인 (Training Pipeline)**

1) **Full Fine-Tuning (짧은 초기 적응)**  
2) **LoRA Fine-Tuning (반복 실험 / 비용 절감)**  

---

## ⚖️ **6. RL vs VLA 기술 비교 (Technical Comparison)**

### **6.1 비교 요약 표**

| 구분 | 강화학습 (RL) | VLA (OpenPI) |
|------|---------------|---------------|
| 학습 방식 | 보상 기반 탐험 | 시연 기반 지도학습 |
| 데이터 효율 | 매우 낮음 | 매우 높음 |
| 학습 안정성 | 낮음 | 높음 |
| 개발 난이도 | Reward 설계 어려움 | 상대적으로 쉬움 |
| 일반화 성능 | 약함 | 강함 |
| 실환경 적용 | 거의 불가 | 가능성 매우 높음 |
| 비용/시간 | 매우 큼 | 효율적 |

---

## 📝 **7. 종합 평가 (Final Evaluation)**

### **7.1 핵심 결론**

- RL은 접근 행동까지는 학습되나  
  **파지 + 정리 단계까지 안정적 정책 학습 실패**
- 보상 민감도·학습 비용 문제로 실환경 적용 불가 수준  
- VLA는  
  - 빠른 수렴  
  - 높은 일반화  
  - 조작 prior  
  덕분에 실사용 정책 개발에 매우 적합

> **최종 결론:**  
> 자율 청소 로봇 개발에는 RL보다  
> **VLA 기반 Imitation Learning 접근이 훨씬 효과적이다.**

---

## 🚀 **8. 향후 계획 (Future Work)**

- e0509 실로봇 기반 VLA 정책 검증  
- 멀티카메라 + 깊이센서 + proprioception 결합 입력  
- RL-VLA Hybrid 구조 연구  
- Multi-task VLA로 확장  
- Sim2Real Gap 감소 실험  

---

## 📎 **9. 부록 (Appendix, Optional)**

- RL 학습 곡선  
- Digital Twin 환경 이미지  
- VLA Inference 예시  
- 데이터 수집 파이프라인  

---

# ✅ END OF README
