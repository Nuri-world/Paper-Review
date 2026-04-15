# [Review] Differentiable Signed Distance Function Rendering (2022)
**Authors**: Delio Vicini, Sébastien Speierer, Wenzel Jakob (Slide by: 박누리)
**Link**: [공식 논문 링크 추가 필요]

## 📌 Summary
본 논문은 물리 기반 미분 가능 렌더링(Physically-based Differentiable Rendering, PBDR)에서 발생하는 불연속성(Discontinuity)으로 인한 그래디언트 왜곡(Gradient bias) 문제를 해결하는 새로운 접근법을 제안합니다. 메쉬(Mesh)나 볼륨(Volume) 대신 부드러운 그래디언트를 제공하는 SDF(Signed Distance Function)를 활용하며, 스피어 트레이싱(Sphere Tracing) 과정에 **재매개화(Reparameterization)** 기법을 도입하여 실루엣(Silhouette) 손실 함수 없이도 3D 형상을 매우 안정적으로 재구성(Reconstruction)하고 최적화할 수 있음을 증명했습니다.

## 🚀 Key Takeaways
1. **Reparameterization을 통한 불연속성 해결**:
   - 객체의 이동이나 회전, 가림(Occlusion) 등으로 인해 렌더링 적분식에서 발생하는 갑작스러운 불연속성을 매끄럽게 보정합니다.
   - 역함수 정리(Inverse Function Theorem)와 Jacobian factor(`|| D T(s) x D T(t) ||`)를 추가하여 스피어 트레이싱 적분식을 재매개화함으로써 정확한 그래디언트를 계산합니다.
2. **Weighted Reparameterization (분산 감소)**:
   - 픽셀 엣지(Edge)나 거리(Dist), Bounding box 등의 요소를 고려한 가중합(Weighted sum)으로 거리 함수를 재설계하여, 몬테카를로(Monte Carlo) 추정 시 발생하는 분산(Variance)을 획기적으로 줄였습니다.
3. **간접 효과(Secondary Effects)의 미분 가능성 보장**:
   - 단순한 형태 복원을 넘어 그림자(Shadows)나 상호 반사(Interreflections) 같은 2차적인 광원 효과까지 그래디언트로 역산할 수 있어, 훨씬 정교하고 사실적인 3D 토폴로지 복원이 가능합니다.

## 💻 Implementation Details (Focused on Volume Data)
현재 개발 중인 **"SDF 기반 실시간 볼륨 다중 변형 시스템"**의 시각화 및 파라미터 최적화(Optimization) 과정에 본 논문의 개념을 부분적으로 차용하여 파이프라인을 고도화할 수 있습니다.

- **환경**: C++, CUDA (역전파 및 그래디언트 연산 가속)
- **주요 구현 사항**:
  - **Differentiable SDF Pipeline**: 볼륨 데이터가 다중 변형될 때, 변형 파라미터(Deformation parameters)를 역산(Inverse)하여 자동으로 최적화할 수 있도록 미분 가능한 스피어 트레이싱 구조를 적용합니다.
  - **안정적인 Gradient 확보**: 볼륨이 겹치거나 잘려나가는 불연속 구간(Silhouette/Occlusion)에서도 그래디언트가 터지지(발산) 않도록 논문의 Jacobian 보정 수식을 렌더링 셰이더(Shader)에 통합합니다.

## 💡 My Thoughts & Connection
- **연구 적합성**: 기존에는 볼륨 변형 시 발생하는 아티팩트나 렌더링 오류를 휴리스틱하게 잡아야 했다면, 이 논문의 방식을 통해 '렌더링 결과'로부터 '변형 파라미터'를 수학적이고 자동적으로 보정(Optimize)하는 역방향 파이프라인을 구상해 볼 수 있게 되었습니다.
- **인사이트**: 복잡한 형태의 장난감(굴삭기 등)이나 토폴로지 변화가 심한 모델도 적은 수의 뷰포인트(Viewpoint)만으로 안정적으로 복원해내는 결과가 인상적입니다. 다만 논문에서도 언급했듯 국소 최적해(Local minima)에 빠지는 한계가 여전히 존재하므로, 실시간 볼륨 다중 변형 시 초기값(Initial state) 설정과 효율적인 재거리화(Redistancing) 기법을 함께 고민해야 완벽한 시스템이 될 것 같습니다.
