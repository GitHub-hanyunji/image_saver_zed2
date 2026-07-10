# ZED Image Saver

ZED 카메라(또는 압축 이미지를 퍼블리시하는 임의의 토픽)로부터 `sensor_msgs/CompressedImage`를 구독하여, 터미널 키 입력으로 원하는 시점의 프레임을 PNG 파일로 저장하는 ROS 2 노드입니다.

## 노드 정보

| 파일 | 노드 이름 | 설명 |
|---|---|---|
| `image_saver.cpp` | `image_saver` | 압축 이미지 토픽을 구독하고, 키보드 입력(`s`)에 따라 최신 프레임을 PNG로 저장합니다. |

## 의존성 (Dependencies)

- ROS 2 (Humble 이상 권장)
- `rclcpp`
- `sensor_msgs`
- `cv_bridge`
- `OpenCV` (`opencv2/opencv.hpp`)
- C++17 이상 (`<filesystem>` 사용)

`package.xml` 예시:

```xml
<depend>rclcpp</depend>
<depend>sensor_msgs</depend>
<depend>cv_bridge</depend>
<depend>OpenCV</depend>
```

`CMakeLists.txt`에서 C++17 이상을 지정해야 합니다.

```cmake
set(CMAKE_CXX_STANDARD 17)
```

## 빌드 방법

```bash
cd ~/your_ws/src
git clone https://github.com/GitHub-hanyunji/image_saver_zed2.git
cd ~/your_ws
colcon build --packages-select image_saver
source install/setup.bash
```

## 실행 방법

```bash
ros2 run image_saver image_saver
```

### 파라미터

| 파라미터 | 기본값 | 설명 |
|---|---|---|
| `image_topic` | `/zed/zed_node/rgb/color/raw/image/compressed` | 구독할 `CompressedImage` 토픽 이름 |
| `save_dir` | `$HOME/zed_captures` | 캡처 이미지를 저장할 디렉터리 (없으면 자동 생성) |

파라미터를 바꿔서 실행하는 예시:

```bash
ros2 run <패키지_이름> image_saver \
  --ros-args \
  -p image_topic:=/camera/color/image_raw/compressed \
  -p save_dir:=/home/user/my_captures
```

## ⌨️ 키보드 조작

노드 실행 후 **해당 터미널에 포커스가 있는 상태**에서 아래 키를 입력하세요.

| 키 | 동작 |
|---|---|
| `s` / `S` | 현재까지 수신된 최신 프레임을 `save_dir`에 PNG로 저장 |
| `q` / `Q` | 노드 종료 |

저장되는 파일명 형식: `capture_YYYYMMDD_HHMMSS_mmm.png` (밀리초 단위 타임스탬프 포함, 중복 방지)

## 참고 사항

- 이미지가 한 번도 수신되지 않은 상태에서 `s`를 누르면 "저장할 이미지가 없습니다" 경고만 출력되고 저장되지 않습니다.
- 구독 QoS는 `SensorDataQoS`를 사용하며, 이미지 인코딩은 `bgr8`로 변환하여 저장합니다.
- 키 입력 감지를 위해 `termios` 기반 non-blocking 입력을 사용하므로 **Linux/macOS 환경 전용**입니다.
- 내부적으로 30Hz(`rclcpp::Rate(30)`)로 `spin_some` + 키 입력 폴링을 반복하는 커스텀 spin 루프(`spin_loop()`)를 사용합니다 (일반적인 `rclcpp::spin()` 대신).

## 📄 License

원하는 라이선스를 명시하세요. (예: MIT, Apache-2.0)
