# Isaac Sim Dual Surface Gripper Pick & Place

Doosan M0609 로봇과 두 개의 Surface Gripper를 이용하여  
트레이를 흡착하고 지정된 위치로 운반하는 Isaac Sim Pick & Place 프로젝트입니다.

두 Surface Gripper를 하나의 그리퍼 인터페이스로 제어하도록 어댑터를 구현했으며,  
RMPflow 기반 Pick & Place Controller를 이용하여 로봇의 접근, 흡착, 이동, 배치 동작을 수행합니다.

## Demo

<p align="center">
  <img src="docs/demo.gif" width="800">
</p>

<p align="center">
  <b>Dual Surface Gripper를 이용한 트레이 Pick & Place</b>
</p>

<p align="center">
  <a href="https://www.youtube.com/watch?v=QmtYdE0tgoA">
    <img
      src="https://img.youtube.com/vi/QmtYdE0tgoA/maxresdefault.jpg"
      width="800"
      alt="Isaac Sim Dual Surface Gripper Demo"
    >
  </a>
</p>

<p align="center">
  <a href="https://www.youtube.com/watch?v=QmtYdE0tgoA">
    전체 시연 영상 보기
  </a>
</p>

---

## Project Overview

이 프로젝트에서는 넓은 트레이를 안정적으로 들어 올리기 위해  
로봇 말단에 좌우 두 개의 흡착 지점을 배치했습니다.

Isaac Sim의 기본 Pick & Place Controller는 일반적으로 하나의 그리퍼 인터페이스를 사용하므로,  
두 개의 Surface Gripper를 동시에 열고 닫을 수 있는 `DualSurfaceGripperAdapter`를 구현했습니다.

### Main Features

- Doosan M0609 6축 로봇 모델 사용
- 좌우 2개의 Isaac Sim Surface Gripper 동시 제어
- RMPflow 기반 End-Effector 경로 제어
- Isaac Sim Pick & Place 10단계 상태기 활용
- 트레이 접근, 흡착, 상승, 운반, 배치 및 복귀 동작
- 로봇 USD, 트레이 USD 및 RMPflow 설정 파일 포함
- 프로젝트 폴더 기준 상대 경로를 이용한 에셋 로드
- 실행 중 두 그리퍼의 상태 및 흡착 물체 출력

---

## Operation Sequence

Pick & Place 동작은 다음 순서로 진행됩니다.

1. 트레이 상단으로 이동
2. 흡착 위치까지 하강
3. 좌우 Surface Gripper 동시 활성화
4. 흡착 안정화 대기
5. 트레이 상승
6. 목표 위치로 이동
7. 목표 위치까지 하강
8. 좌우 Surface Gripper 동시 해제
9. End-Effector 상승
10. 초기 자세로 복귀

Pick & Place Controller의 상태기에서 다음 이벤트에 그리퍼 명령이 실행됩니다.

- Event 2: 두 Surface Gripper 활성화
- Event 7: 두 Surface Gripper 비활성화

---

## System Structure

```text
PickPlaceController
        │
        ├── RMPFlowController
        │     └── M0609 joint motion control
        │
        └── DualSurfaceGripperAdapter
              ├── SurfaceGripperAdapter (Left)
              └── SurfaceGripperAdapter (Right)
```

`DualSurfaceGripperAdapter`는 두 Surface Gripper의 `open()` 및 `close()` 명령을 묶어  
상위 Pick & Place Controller에서 하나의 그리퍼처럼 사용할 수 있도록 구성했습니다.

---

## Environment

본 프로젝트는 다음 환경에서 개발 및 테스트했습니다.

| Component          | Environment                   |
| ------------------ | ----------------------------- |
| OS                 | Ubuntu Linux                  |
| Simulator          | NVIDIA Isaac Sim 6            |
| Robot              | Doosan M0609                  |
| Motion Controller  | RMPflow                       |
| Gripper            | Dual Surface Gripper          |
| Middleware         | ROS 2 Humble                  |
| RMW Implementation | Fast DDS (`rmw_fastrtps_cpp`) |
| Language           | Python 3                      |
| Simulation Mode    | GUI mode (`headless=False`)   |

> GPU, NVIDIA Driver, CUDA 버전은 실제 시연 PC 환경에 맞게 추가해 주세요.

---

## Repository Structure

```text
isaacsim_dual_surface_gripper/
├── Collected_model_redtray_scaled_for_180mm_pads/
│   └── model_redtray_scaled_for_180mm_pads.usda
│
├── Collected_test_dual_suction_surface_grippers_fixed_paths/
│   └── test_dual_suction_surface_grippers_fixed_paths.usda
│
├── doosan-robot2/
│   └── urdf/
│       └── m0609_isaac_sim.urdf
│
├── rmpflow/
│   ├── m0609_description.yaml
│   ├── m0609_rmpflow_common.yaml
│   └── m0609_rmpflow_controller.py
│
├── docs/
│   └── demo.gif
│
├── dual_surface_gripper_adapter.py
├── surface_gripper_adapter.py
├── m0609_pick_place_controller_surface.py
├── m0609_pick_place_dual_tray.py
└── README.md
```

### Main Files

| File                                     | Description                               |
| ---------------------------------------- | ----------------------------------------- |
| `m0609_pick_place_dual_tray.py`          | 시뮬레이션 환경 구성 및 Pick & Place 실행 |
| `m0609_pick_place_controller_surface.py` | M0609용 Pick & Place Controller           |
| `dual_surface_gripper_adapter.py`        | 좌우 Surface Gripper 통합 제어            |
| `surface_gripper_adapter.py`             | 단일 Surface Gripper 인터페이스           |
| `rmpflow/m0609_rmpflow_controller.py`    | M0609 RMPflow Controller                  |
| `rmpflow/*.yaml`                         | 로봇 및 RMPflow 설정                      |
| `Collected_*`                            | 로봇과 트레이의 수집된 USD 에셋           |

---

## Installation

### 1. Clone Repository

```bash
git clone https://github.com/k-chan-l/isaacsim_dual_surface_gripper.git
cd isaacsim_dual_surface_gripper
```

### 2. Configure ROS 2 Environment

Isaac Sim에 포함된 ROS 2 Humble 라이브러리를 사용하는 경우 다음 환경 변수를 설정합니다.

```bash
export ROS_DOMAIN_ID=140
export ROS_DISTRO=humble
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<ISAAC_SIM_PATH>/exts/isaacsim.ros2.bridge/humble/lib
```

예시:

```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/dev_ws/isaac_sim/isaacsim/_build/linux-x86_64/release/exts/isaacsim.ros2.bridge/humble/lib
```

> `<ISAAC_SIM_PATH>`는 Isaac Sim이 설치된 실제 경로로 변경해야 합니다.

---

## Run

Isaac Sim Python 환경에서 메인 스크립트를 실행합니다.

```bash
isaac_python m0609_pick_place_dual_tray.py
```

설치 방식에 따라 다음과 같이 실행할 수도 있습니다.

```bash
<ISAAC_SIM_PATH>/python.sh m0609_pick_place_dual_tray.py
```

Isaac Sim 창이 열리면 상단의 **Play** 버튼을 눌러 시뮬레이션을 시작합니다.

실행이 완료되면 터미널에 다음 정보가 출력됩니다.

- 현재 Pick & Place 이벤트
- 좌우 Surface Gripper 상태
- 각 그리퍼가 흡착한 물체
- 트레이 위치
- End-Effector 높이
- Pick & Place 완료 상태

---

## Main Configuration

작업 위치와 그리퍼 설정은  
`m0609_pick_place_dual_tray.py`에서 수정할 수 있습니다.

### Tray Position

```python
TRAY_INIT_POS = np.array([0.30, 0.40, 0.001])
```

### Goal Position

```python
GOAL_FLOOR_POS = np.array([0.55, -0.35, 0.0])
```

### Tool Orientation

```python
TOOL_YAW_DEG = 90.0
```

### Surface Gripper Prim Paths

```python
SURFACE_GRIPPER_PATHS = [
    "/World/m0609/onrobot_rg2ft/gripper_body/dual_suction_tool/"
    "suction_contact_left/SurfaceGripper_left",

    "/World/m0609/onrobot_rg2ft/gripper_body/dual_suction_tool/"
    "suction_contact_right/SurfaceGripper_right",
]
```

### Pick & Place Event Speed

```python
EVENTS_DT = [
    0.008,   # 트레이 접근
    0.005,   # 하강
    0.02,    # 두 gripper close
    0.15,    # 흡착 안정화
    0.0025,  # 상승
    0.01,    # 배치 위치로 이동
    0.0025,  # 하강
    1.0,     # 두 gripper open
    0.008,   # 상승
    0.08,    # 복귀
]
```

각 값은 Pick & Place 상태기의 단계별 진행 속도에 영향을 줍니다.

---

## Implementation Details

### Dual Surface Gripper Adapter

Isaac Sim의 Pick & Place Controller는 하나의 그리퍼 객체에 대해  
`open()`과 `close()`를 호출합니다.

이 프로젝트에서는 `DualSurfaceGripperAdapter`가 두 개의  
`SurfaceGripperAdapter`를 내부에 보관하고 동일한 명령을 순서대로 전달합니다.

```python
DualSurfaceGripperAdapter
├── Left Surface Gripper
└── Right Surface Gripper
```

이를 통해 기존 Pick & Place Controller의 상태기를 크게 수정하지 않고  
듀얼 흡착 구조를 적용했습니다.

### End-Effector Offset

로봇의 `link_6` 위치와 두 흡착 접촉점의 평균 위치 차이를 실행 중 계산하여  
End-Effector Offset으로 사용합니다.

접근 및 배치 단계에서는 이벤트에 따라 Z축 Offset을 추가로 조정하여  
흡착 패드가 트레이 표면에 접촉하도록 구성했습니다.

### Task Completion

트레이의 현재 XY 위치와 목표 XY 위치의 평균 오차를 계산하고,  
오차가 설정 범위 안에 들어오면 작업 완료로 판단합니다.

---

## ROS 2 Image Transmission Experiment

본 프로젝트에서는 Isaac Sim ROS 2 Bridge를 이용한  
외부 PC 이미지 전달 및 영상 처리도 실험했습니다.

외부 ROS 2 PC에서 사용할 수 있는 기본 환경은 다음과 같습니다.

```bash
source /opt/ros/humble/setup.bash

export ROS_DOMAIN_ID=140
export ROS_DISTRO=humble
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp

source ~/cobot3_ws/install/setup.bash
ros2 run cobot3 m0609_color_detector
```

현재 Pick & Place 동작은 Isaac Sim 내부에서 실행되며,  
외부 영상 인식 노드는 별도의 실험 코드로 구성되어 있습니다.

---

## Known Issues

외부 PC가 Isaac Sim 카메라 이미지 토픽을 구독할 때  
시뮬레이션 속도가 저하되는 현상이 확인되었습니다.

가능한 원인은 다음과 같습니다.

- 원본 이미지 해상도에 따른 데이터 전송량 증가
- 렌더링과 ROS 2 이미지 발행의 동시 처리
- 이미지 복사 및 변환 연산
- 외부 구독자가 연결된 이후 발생하는 통신 부하
- 시뮬레이션 루프 내부의 대기 시간

---

## Future Work

- Python API를 이용한 카메라 프레임 직접 획득
- 이미지 Crop 및 Downscaling 적용
- `sensor_msgs/Image`와 `CompressedImage` 성능 비교
- ROS 2 QoS 및 발행 주기 조정
- 이미지 처리와 로봇 제어 루프 분리
- 중복 연산과 대기 시간 최소화
- 흡착 성공 여부를 이용한 예외 처리
- 한쪽 그리퍼만 흡착된 경우의 안전 동작 추가
- 트레이 자세 오차에 대응하는 흡착 위치 보정
- 카메라 인식 결과를 이용한 동적 Pick Position 설정

---

## Notes

- USD 내부 Prim 경로가 변경되면 `SURFACE_GRIPPER_PATHS`도 수정해야 합니다.
- 로봇 및 트레이 에셋은 상대 경로로 로드되므로 저장소의 기본 폴더 구조를 유지해야 합니다.
- Isaac Sim 설치 위치에 따라 ROS 2 Bridge 라이브러리 경로가 달라질 수 있습니다.
- 다른 ROS 2 PC와 통신할 경우 `ROS_DOMAIN_ID`와 RMW 구현을 동일하게 설정해야 합니다.

---

## License

This repository is intended for educational and research purposes.

사용한 로봇 모델과 외부 에셋의 라이선스는 각 원본 프로젝트 및 제공자의 라이선스를 따릅니다.
