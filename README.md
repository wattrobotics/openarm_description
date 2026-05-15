# openarm_description

OpenArm 의 URDF / Xacro / Mesh / RViz 설정. **오른팔만 있는 구성**을
위해 `include_left` / `include_right` 인자가 추가되어 있음.

## 제공 launch

| 파일 | 용도 |
|---|---|
| `display_openarm.launch.py` | `robot_state_publisher` + `joint_state_publisher_gui` + RViz. 슬라이더로 조인트 각도 확인 |

## 핵심 xacro 인자 (`urdf/robot/v10.urdf.xacro`)

| 인자 | 기본값 | 설명 |
|---|---|---|
| `arm_type` | `v10` | OpenArm 버전 (현재 v1.0 만 지원) |
| `bimanual` | `false` | `true` 면 body 포함 양팔 모드, `false` 면 body 없는 단일 팔 |
| `include_left` | `true` | `bimanual=true` 일 때 왼팔 포함 여부 |
| `include_right` | `true` | `bimanual=true` 일 때 오른팔 포함 여부 |
| `ee_type` | `openarm_hand` | end-effector (`openarm_hand` 또는 `none`) |
| `ros2_control` | `false` | ros2_control hardware interface 포함 여부 |
| `use_fake_hardware` | `false` | `mock_components/GenericSystem` 사용 |
| `right_can_interface` | `can0` | 오른팔 CAN |
| `left_can_interface` | `can1` | 왼팔 CAN |

## 오른팔만 구성

```
bimanual:=true include_left:=false include_right:=true
```
→ body + 오른팔 + `openarm_arm_ros2_control` (right_ prefix, right CAN).

## 빠른 실행

```bash
ros2 launch openarm_description display_openarm.launch.py arm_type:=v10 bimanual:=true include_left:=false include_right:=true
```

상세 사용법 → [TUTORIAL.md](TUTORIAL.md).

## Related links

- 📚 [docs.openarm.dev](https://docs.openarm.dev/software/description) (업스트림 문서)
- 💬 [Discord](https://discord.gg/FsZaZ4z3We)
- 📬 <openarm@enactic.ai>

## License

[Apache License 2.0](LICENSE.txt) — Copyright 2025 Enactic, Inc.
