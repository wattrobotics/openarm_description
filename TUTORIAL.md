# openarm_description 튜토리얼 (오른팔 전용)

오른팔만 있는 구성으로 시각화 / URDF 디버깅하는 방법.

---

## A. 빌드

```bash
cd ~/openarm_ws
colcon build --packages-select openarm_description
source install/setup.bash
```

---

## B. RViz + 슬라이더 GUI 로 오른팔 시각화

```bash
ros2 launch openarm_description display_openarm.launch.py \
    arm_type:=v10 bimanual:=true include_left:=false include_right:=true
```
- `joint_state_publisher_gui` 창이 뜸 → 슬라이더로 `openarm_right_joint1..7`,
  `openarm_right_finger_joint1` 을 직접 움직여 가동 범위 / 자세 확인 가능
- RViz 는 `rviz/bimanual.rviz` 가 로드됨 (오른팔만 표시됨)
- ros2_control 없이 동작하므로 CAN / 컨트롤러 무관

---

## C. xacro 단독으로 URDF 출력 (디버깅용)

```bash
ros2 run xacro xacro \
  $(ros2 pkg prefix openarm_description)/share/openarm_description/urdf/robot/v10.urdf.xacro \
  bimanual:=true include_left:=false include_right:=true \
  ros2_control:=true use_fake_hardware:=true right_can_interface:=can0 \
  > /tmp/right_arm.urdf
```

확인:
```bash
grep -c openarm_left /tmp/right_arm.urdf          # 0 이어야 함
grep -c openarm_right_joint /tmp/right_arm.urdf   # 14 이상 (joint 마다 여러 줄)
grep openarm_body_link0 /tmp/right_arm.urdf       # body link 살아있는지
```

---

## D. 오른팔만 있는 구성 메모

- 핵심 조합: **`bimanual:=true` + `include_left:=false`**
- 이 조합으로 body + 오른팔만 URDF / ros2_control 에 포함됨.
- body 없이 팔 하나만 띄우는 시나리오는 `bimanual:=false` 인데 본 환경에서는
  사용하지 않음.
- `openarm_robot.xacro` 의 `bimanual=true` 블록 안에서 `include_left` /
  `include_right` 로 한쪽만 활성화하면 `openarm_arm_ros2_control` 매크로가
  자동으로 사용됨.

---

## E. 변경된 사항 (이미 반영)

- `ros2_control` 의 plugin 이름을 `openarm_hardware/OpenArmHW` 로 패치
  (`urdf/ros2_control/openarm.ros2_control.xacro`).
- `openarm_robot` 매크로에 `include_left` / `include_right` 인자 추가
  (`urdf/robot/openarm_robot.xacro`).
- `v10.urdf.xacro` 에서 위 인자를 노출.
- `display_openarm.launch.py` 가 `include_left` / `include_right` 인자를
  받도록 확장.

---

## F. ROS 2 패키지와의 관계

실제 컨트롤러 / MoveIt 데모는 `openarm_ros2` 의
[TUTORIAL.md](../openarm_ros2/TUTORIAL.md) 참고.
