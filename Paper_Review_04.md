# [Review] Non-linear Sphere Tracing for Rendering Deformed Signed Distance Fields (2021)
**Authors**: [논문 원저자 추가 필요] (Slide by: 박누리)
**Link**: [공식 논문 링크 추가 필요]

## 📌 Summary
본 논문은 무한한 해상도와 부드러운 CSG 연산의 장점을 가진 SDF(Signed Distance Function) 모델에 애니메이션과 같은 공간 변형(Spatial Deformation)을 적용한 후, 이를 실시간으로 렌더링하기 위한 **비선형 스피어 트레이싱(Non-linear Sphere Tracing, NLST)** 기법을 제안합니다. 기존에는 변형된 공간에서 렌더링하기 위해 복잡하고 계산 비용이 큰 역변형(Inverse Deformation)이나 립시츠 보정(Lipschitz Bound)이 필수적이었으나, 광선(Ray)의 추적 궤적을 상미분방정식(ODE) 기반의 곡선 형태로 변환함으로써 **역변환 과정 없이도 안전하고 매끄럽게 변형된 SDF를 렌더링**하는 혁신적인 접근을 보여줍니다.

## 🚀 Key Takeaways
1. **역변환(Inverse Deformation) 불필요**:
   - 변형된 표면과 광선의 교차점을 찾기 위해 공간을 역으로 추적할 필요 없이, 기존의 Forward Deformation(LBS, FFD, Kelvinlets 등) 수식만으로도 변형된 SDF를 직접 렌더링할 수 있습니다.
2. **ODE 기반 곡선 레이 트레이싱 (Curved Ray Tracing)**:
   - "변형된 공간에서 직진하는 광선은, 원본(비변형) 공간에서는 휘어진 곡선이다"라는 통찰을 기반으로 합니다.
   - 레이의 궤적을 ODE 초기값 문제로 정식화하고, ODE Solver를 통해 원본 공간 안에서 동적으로 휘어지는 곡선을 따라 스피어 트레이싱을 수행합니다.
3. **가속 파이프라인 (Hull Generation & Barycentric Interpolation)**:
   - 렌더링 최적화를 위해 저해상도의 헐(Hull) 메쉬를 생성한 뒤 Forward 변형을 가합니다. 이후 화면에 Rasterization된 결과의 무게중심 좌표(Barycentric coordinates)를 보간하여, 역변환 연산 없이 초기 비변형 공간 좌표를 얻어내는 트릭으로 실시간 성능을 달성했습니다.

## 💻 Implementation Details 

## 💡 My Thoughts & Connection
- **연구의 돌파구**: 볼륨 데이터 변형 시 실시간성을 깎아먹는 가장 큰 원인이었던 역변환 연산의 병목을 완벽하게 우회할 수 있다는 점에서 제 연구 파이프라인의 핵심적인 이론적 배경이 되었습니다.
- **인사이트**: 렌더링 단계를 'SDF 거리 룩업'과 '레이 궤적 적분'으로 분리한 덕분에, 추후 어떠한 복잡한 변형 알고리즘을 시스템에 추가하더라도 렌더링 셰이더(Shader) 코드를 크게 수정할 필요 없이 유연하게 대응할 수 있는 강력한 확장성을 얻게 되었습니다.
