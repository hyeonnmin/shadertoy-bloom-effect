# 📚 ShaderToy Bloom Effect

## Project Overview

이 프로젝트는 **Bloom Effect**를 구현한 post-processing shader이다.

Bloom Effect는 화면 내에서 밝기가 높은 영역이 주변으로 퍼져 보이도록(blurring) 만들어, 강한 조명이나 발광 물체를 강조하는 시각 효과이다. 실시간 렌더링에서는 HDR 결과를 기반으로 장면의 밝기 대비를 부드럽게 만들고, 보다 자연스러운 광원 표현을 위해 사용된다.

본 구현에서는 Gaussian Filter를 이용해 밝은 영역을 추출한 뒤, 다단계 downsampling 및 upsampling 기반 bluring을 적용하여 Bloom을 생성하고, 이를 원본 resource와 결합하는 방식으로 Bloom Effect를 구현하였다.

---

## 📌 Key Concepts & Implementation

### **1) Bright Pass (Threshold 기반 밝기 추출)**
   
  Bloom은 전체 화면에 적용하지 않고, 밝기가 일정 threshold 이상인 픽셀만 대상으로 한다.

  - 초기 render target에서
  - threshold보다 낮은 픽셀은 제거(검정색)
  - threshold보다 높은 밝기 영역만 Bloom 처리

  이를 통해 Bloom이 불필요하게 화면 전체를 흐리지 않도록 제한하였다.

---

### **2) Gaussian Filter 기반 Blur** 

  밝은 영역이 자연스럽게 퍼져 보이도록 Gaussian Blur를 적용하였다.
  
  - X 방향 Blur (BlurX)
  - Y 방향 Blur (BlurY)

  를 분리하여 적용함으로써:

  - 연산 비용 감소
  - 2D Gaussian Blur 효과를 효율적으로 구현


---

### **3) Multi-Stage Down Sampling (Aliasing 감소)**

  단일 해상도에서 바로 Blur를 적용하면 aliasing과 불안정한 블러가 발생할 수 있기 때문에,
  
  본 구현에서는 해상도를 단계적으로 줄이는 방식을 사용하였다.
  
  - 원본 해상도 → 1/2 → 1/4 → 1/8 …
  - 각 단계에서 이전 단계의 결과를 입력으로 사용

  이 방식은:

  - 고주파 노이즈 감소
  - Bloom 영역을 더 넓고 부드럽게 확산

---


### **4) Upsampling + Blur 병행 처리**

  Downsampling 이후에는, Blur를 유지한 채 순차적으로 Upsampling을 수행한다.
  
  - 낮은 해상도 → 높은 해상도로 복원
  - 각 단계에서 BlurX / BlurY를 반복 적용

  이를 통해:

  - Bloom이 뭉개지는 현상을 방지
  - 원본 해상도로 돌아오면서도 자연스러운 Bloom 확산 유지

---

### **5) Original Scene과 Combine**

  최종적으로 Bloom 결과 리소스를 원본 장면과 결합한다.
  
  - Bloom 결과에 Bloom Strength를 곱한 뒤
  - 원본 Scene Color에 더하는 방식으로 처리

이를 통해 Bloom의 강도를 조절 가능하도록 구성하였다.

---

### 🧩Code Snippet

<details>
  <summary><b></b></summary>

  <img width="720" alt="carbon (11)" src="https://github.com/user-attachments/assets/f2259bd7-c74f-4e6e-ac49-6b86ea935591" />
</details>

---

## 📌 Sample Outputs

<img width="720" alt="스크린샷 2026-01-06 210336" src="https://github.com/user-attachments/assets/08f7da55-336a-409d-96d1-d8f0c3bd7b5a" />

---


## 📌 Future Work


---


## Development Environment

- Language: C++
- Graphics API: DirectX11
- Development Environment: Visual Studio 2022
- Build Configuration: x64, Debug / Release

### Hardware
- CPU: Intel CPU (Desktop)
- GPU: NVIDIA RTX 3060
- RAM: 16GB

### Platform
- OS: Windows 10 / 11
  
---

## References

- HongLab
  *Introduction to Computer Graphics with DirectX 11 – Part 2: Realtime Pipeline*
