# Practice 3: Visual Localization

## 🤖 Marker Based Visual Localization — AprilTags

This project solves the **Marker Based Visual Localization** practice from Robotics Academy / Unibotics.

The objective is to estimate the **2D pose** of a mobile robot:

```text
pose = (x, y, yaw)
```

using visual markers called **AprilTags**.

In the simulator:

- 🟢 Green robot = Ground Truth pose
- 🔵 Blue robot = Odometry pose with noise
- 🔴 Red robot = Estimated pose

The goal is to make the **red robot follow the green robot** as accurately as possible.

---

# 📌 Project Idea

The robot uses its camera to detect AprilTags placed in the environment.

Each AprilTag has a known position in the map, stored in:

```text
/resources/exercises/marker_visual_loc/apriltags_poses.yaml
```

The system combines:

- AprilTag detection
- Perspective-n-Point pose estimation
- Visual correction
- Odometry prediction
- Laser obstacle avoidance
- Pose smoothing
- WebGUI visualization

---

# 🧠 Localization Pipeline

```text
Camera image
    ↓
Grayscale preprocessing
    ↓
AprilTag detection
    ↓
PnP pose estimation
    ↓
Camera pose in world frame
    ↓
Robot pose estimation
    ↓
Visual correction of odometry
    ↓
Estimated pose shown in WebGUI
```

---

# 🏷️ AprilTag Detection

The project uses `pyapriltags` to detect AprilTags from the robot camera.

```python
detector = pyapriltags.Detector(
    searchpath=["apriltags"],
    families="tag36h11",
    nthreads=2,
    quad_decimate=1.0,
    quad_sigma=0.0,
    refine_edges=True,
    decode_sharpening=0.25
)
```

Only valid tags are accepted:

- Tag must exist in the YAML map
- Tag area must be large enough
- PnP solution must be valid

---

# 📷 Camera Model

A simple pinhole camera model is used.

```python
K = [
    [fx, 0, cx],
    [0, fy, cy],
    [0, 0, 1]
]
```

The focal length is approximated using the image width.

---

# 📐 Pose Estimation with PnP

The 3D corners of the AprilTag are known because the tag size is known.

```python
TAG_SIZE = 0.24
```

The project uses OpenCV:

```python
cv2.solvePnP(
    ...,
    flags=cv2.SOLVEPNP_IPPE_SQUARE
)
```

`SOLVEPNP_IPPE_SQUARE` is especially suitable for square planar markers such as AprilTags.

---

# 🌍 From Tag Pose to Robot Pose

The tag pose in the world is loaded from the YAML file.

The camera pose is computed using:

```text
world_T_camera = world_T_tag × inverse(camera_T_tag)
```

Then the robot pose is extracted:

```text
x, y, yaw
```

---

# 🔁 Odometry + Visual Correction

The localization system does not depend only on vision.

When AprilTags are visible:

```text
Vision corrects odometry
```

When AprilTags disappear:

```text
Odometry keeps tracking the robot
```

This prevents the red robot from freezing when tags are temporarily lost.

Final estimation:

```text
Estimated Pose = Odometry + Visual Correction
```

---

# 🧭 Yaw Calibration

The code includes automatic yaw correction to align:

- AprilTag/OpenCV frame
- Gazebo/world frame
- Robot movement direction

Important parameters:

```python
AUTO_YAW_CALIBRATION = True
YAW_BIAS_ALPHA = 0.25
MIN_MOVE_FOR_YAW_CALIB = 0.05
```

---

# 🧪 Robustness Features

This implementation includes:

- ✅ AprilTag area filtering
- ✅ Multi-tag fusion
- ✅ PnP validation
- ✅ Positive depth validation
- ✅ Reprojection filtering
- ✅ Visual jump rejection
- ✅ Odometry fallback
- ✅ Pose smoothing
- ✅ Automatic yaw calibration
- ✅ Laser obstacle avoidance

---

# 🚗 Robot Movement

Robot behavior:

```text
Tag visible     → move forward slowly
No tag visible  → rotate and search
Obstacle close  → avoid obstacle
```

The robot also tries to keep the tag centered in the camera image.

---

# 🖥️ Debug Visualization

The debug window displays:

- Number of detections
- Valid tags
- Estimated pose
- Visual pose
- Yaw information
- Pose source
- Tag ID and bounding boxes

Estimated pose is displayed using:

```python
WebGUI.showEstimatedPose(tuple(estimated_pose))
```

---

# 📂 Project Structure

```text
P3_TarefBilel/
└── code.py
```

---

# ▶️ How to Run

Open the Robotics Academy practice:

```text
Marker Based Visual Loc
```

Paste the content of:

```text
code.py
```

Run the simulation.

---

# ⚙️ Main Parameters

```python
TAG_SIZE = 0.24
TAG_MIN_AREA = 180

FWD_SPEED = 0.15
TURN_SPEED = 0.35
OBS_DIST = 0.55

SEARCH_TURN_SPEED = 0.35
LOOP_HZ = 15

CORRECTION_ALPHA = 0.35
MAX_VISUAL_JUMP = 1.50
MAX_VISUAL_YAW_JUMP = math.radians(140)
```

---

# ✅ Expected Result

The estimated red robot should follow the green ground-truth robot closely.

Small differences may appear because of:

- Camera approximation
- Odometry noise
- PnP estimation noise
- Temporary tag loss
- Simulator physics

However, the system remains stable thanks to continuous odometry correction using AprilTags.

---

# 📚 Technologies Used

- Python
- OpenCV
- NumPy
- PyYAML
- pyapriltags
- Robotics Academy HAL API
- Robotics Academy WebGUI API
- Frequency API

---

# 👨‍💻 Author

**Taref Bilel**

Practice:

```text
Marker Based Visual Localization with AprilTags
```
