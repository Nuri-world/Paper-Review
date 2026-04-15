# [Review] Sphere tracing: a geometric method for the antialiased ray tracing of implicit surfaces (1996)
**Authors**: John C. Hart (Slide by: 박누리)
**Link**: [공식 논문 링크 추가 필요]

## 📌 Summary
본 논문은 SDF(Signed Distance Function)로 정의된 음함수 표면(Implicit Surfaces)을 화면에 렌더링하기 위한 혁신적인 레이 트레이싱(Ray Tracing) 기법인 Sphere Tracing을 제안합니다. 기존의 다각형화(Marching Cubes) 방식이 가지는 해상도 한계나, 직접 근을 찾는 방식(Newton's method 등)의 불안정성을 극복했습니다. 대신 립시츠 경계(Lipschitz bound)를 활용하여 광선이 표면을 뚫고 지나가지 않게 안전한 보폭으로 전진하며 교차점을 찾는 방법을 통해, 복잡한 CSG 연산이나 노이즈 함수 표면까지 안정적으로 렌더링할 수 있음을 증명했습니다.

## 🚀 Key Takeaways
1. **안정적인 Ray Intersection (Sphere Tracing)**:
   - 광선을 따라 이동할 때, 현재 위치에서 표면까지의 최단 거리(SDF 값)를 반지름으로 하는 구(Sphere)를 상상하며 그 거리만큼만 전진합니다. (`t_{i+1} = t_i + f(r(t_i))`)
   - 표면을 절대 침범하지 않으며 선형 수렴(Linear convergence)을 보장하는 매우 안정적인 루트 파인딩(Root-finding) 기법입니다.
2. **간결하고 강력한 CSG 연산**:
   - 두 오브젝트의 합집합(Union)은 `min()`, 교집합(Intersection)은 `max()`, 여집합(Complement)은 부호 반전(`-`)이라는 단순한 연산만으로 복잡한 형태를 실시간으로 합성할 수 있습니다.
3. **Cone Tracing을 통한 안티앨리어싱(Antialiasing)**:
   - 광선을 선이 아닌 픽셀 반경 크기의 원뿔(Cone) 형태로 추적합니다.
   - 픽셀 반경 내에 표면이 차지하는 비율(Coverage Function, `α`)을 계산하여 경계면의 계단 현상(Aliasing)을 수학적으로 부드럽게 처리합니다.

## 💻 Implementation Details (Focused on Volume Data)
진행 중인 **"SDF 기반 실시간 볼륨 다중 변형 시스템"**의 시각화(Rendering) 파이프라인의 핵심 근간으로 본 논문의 Sphere Tracing(현업에서는 Ray Marching으로 자주 불림) 기법을 활용하여 구현했습니다.

- **환경**: C++, OpenGL, CUDA (Compute Shader)
- **주요 구현 사항**:
  - **GPU 기반 Ray Marching 렌더러**: 화면의 각 픽셀마다 발사되는 광선을 CUDA/Compute Shader를 이용해 병렬로 Sphere Tracing 연산 처리하여 실시간 렌더링을 구현했습니다.
  - **동적 CSG 블렌딩 적용**: 다중 볼륨 데이터가 실시간으로 변형되고 겹칠 때 발생하는 결합부를 `min/max` 연산을 통해 매끄럽게(Smooth Blending) 렌더링하도록 적용했습니다.
  - **최적화 기법 도입**: 논문에서 제안된 Bounding Volumes 및 거리 근사치(Distance Estimate) 방식을 적용하여 불필요한 빈 공간(Empty space)에서의 연산 스텝을 획기적으로 줄였습니다.

## 💡 My Thoughts & Connection
- **연구 뼈대 형성**: 현재 연구 중인 SDF 볼륨 변형 시스템에서, 변형된 3D 결과를 2D 모니터 화면에 가장 효율적이고 정확하게 뿌려주기 위해 반드시 깊게 이해해야 하는 근본 논문입니다.
- **인사이트**: Marching Cubes처럼 폴리곤 메쉬를 거치지 않고, 거리장 데이터 자체를 수학적으로 렌더링한다는 점이 볼륨 데이터 처리에 압도적으로 유리하다는 것을 깨달았습니다. 향후 렌더링 속도 개선을 위해 논문 말미에 언급된 유클리드 거리 이외의 Distance Metric(예: Manhattan 거리 기반의 Cube-tracing)을 연구에 접목해 보는 것도 좋은 발전 방향이 될 것 같습니다.
