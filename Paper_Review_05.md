# [Review] 3D Gaussian Splatting for Real-Time Radiance Field Rendering (2023)
**Authors**: Bernhard Kerbl, Georgios Kopanas, Thomas Leimkühler, George Drettakis
**Link**: 

## 📌 Summary
본 논문은 기존 NeRF(Neural Radiance Fields)가 가지는 '느린 학습 및 렌더링 속도'라는 치명적인 단점을 극복하기 위해, 3D 공간을 미분 가능한 가우시안(3D Gaussians)들의 집합으로 표현하는 **3D Gaussian Splatting(3DGS)** 기법을 제안합니다. 무거운 신경망(MLP)이나 광선 추적(Ray Marching)을 배제하고, 수학적으로 미분 가능한 타원체(Gaussian)들을 화면에 투영(Splatting)하는 타일 기반 래스터화(Tile-based Rasterization)를 통해 **1080p 해상도에서 130 FPS 이상의 SOTA급 실시간 렌더링**과 초고속 학습(약 45분)을 달성했습니다.

## 🚀 Key Takeaways
1. **Explicit yet Continuous Representation**:
   - 기존의 포인트 클라우드나 메쉬(Explicit)가 가지는 빠른 렌더링 속도의 장점과, NeRF(Implicit)가 가지는 고품질 연속 표현의 장점을 결합하여 3D 가우시안을 씬(Scene)의 기본 단위로 사용했습니다.
2. **Adaptive Density Control (적응형 밀도 제어)**:
   - SfM(Structure-from-Motion)으로 얻은 초기 포인트에서 시작해, 최적화 과정에서 비어있는 공간(Under-reconstruction)은 가우시안을 복제(Clone)하고, 과도하게 큰 가우시안(Over-reconstruction)은 분할(Split)하여 디테일을 스스로 채워나갑니다.
3. **Tile-based Rasterization & Custom CUDA**:
   - 화면을 타일 단위로 나누고 가우시안들을 정렬(Sorting)하여 알파 블렌딩(Alpha Blending)을 수행하는 커스텀 CUDA 커널을 통해 압도적인 렌더링 속도를 확보했습니다.

## 💡 My Thoughts & Connection (SDF Integration Idea)
본 논문의 3DGS는 시각적인 품질과 속도 면에서 혁신적이지만, 씬을 단순히 '색상을 가진 구름 덩어리(Gaussians)'로 표현하기 때문에 명확한 **표면(Surface) 기하 정보가 부족하여 물리적 충돌이나 임의의 변형(Deformation)을 제어하기 매우 까다롭다**는 한계가 있습니다. 

현재 연구 중인 "SDF 기반 실시간 볼륨 다중 변형 시스템"의 개념을 3DGS와 결합한다면, 두 기술의 한계를 상호 보완하는 강력한 하이브리드 파이프라인을 구축할 수 있을 것이라 생각합니다.

* **Idea: SDF-Guided Deformable 3D Gaussians**
  1. **물리와 기하 제어는 SDF로**: 명확한 안/밖 구분과 곡면 계산이 가능한 SDF를 백본(Backbone) 뼈대로 사용하여 물리적 충돌, CSG 연산, 복잡한 위상 변형 연산을 처리합니다.
  2. **시각적 렌더링은 3DGS로**: 최적화된 3D 가우시안들을 SDF의 표면(Zero-level set)에 앵커링(Anchoring)시킵니다.
  3. **실시간 변형 렌더링**: SDF 시스템에서 다중 볼륨 변형이 일어나면, 표면에 붙어있는 가우시안들이 SDF의 그래디언트장(Gradient field)을 따라 유기적으로 이동하고 회전합니다. 
* **기대 효과**: 이를 통해 위상 변화(Topology change)가 일어나는 복잡한 애니메이션 상황에서도 3DGS 특유의 극사실적인 질감과 빛 반사(SH)를 유지하며 **초당 100프레임 이상으로 렌더링할 수 있는 차세대 Deformable 렌더링 엔진**으로 확장 가능할 것입니다.
