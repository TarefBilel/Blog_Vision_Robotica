# 📦 Practice 2: 3D Reconstruction

---

## 👨‍💻 Author

**Taref Bilel**
**Master en Visión Artificial**
**Asignatura:** Visión Robótica

## 📌 Overview

This project implements a real-time **3D reconstruction system** using **stereo vision** in the RoboticsAcademy / Unibotics environment.

The objective is to reconstruct a **3D point cloud** from two stereo camera images using:

- 🖼️ Edge detection
- 🔍 Stereo matching
- 📏 Epipolar constraints
- 📐 Triangulation
- 🌐 3D visualization

The implementation follows the theory of **Stereo Reconstruction** and **Epipolar Geometry** explained in the RoboticsAcademy guide.

---

# 🧠 Reconstruction Pipeline

The system is divided into several stages:

---

## 1️⃣ Image Acquisition

The stereo images are retrieved from the simulator using the HAL API.

```python
imgL = HAL.getImage("left")
imgR = HAL.getImage("right")
```

---

## 2️⃣ Image Preprocessing

Each image goes through several preprocessing steps:

- Convert to grayscale
- Bilateral filtering
- Canny edge detection
- Edge dilation

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.bilateralFilter(gray, 7, 50, 50)
edges = cv2.Canny(blur, 40, 120)
edges = cv2.dilate(edges, np.ones((2, 2), np.uint8), iterations=1)
```

### ✅ Why preprocessing?

This improves edge quality while reducing noise and unstable matches.

---

## 3️⃣ Feature Detection

Instead of using SIFT or ORB, edge pixels are used as feature points.

```python
if edges[y, x] > 0:
    pts.append((x, y))
```

### ✅ Why edges?

Because 3D reconstruction requires a large amount of points.

Edges provide:
- More features
- Better scene coverage
- Faster extraction

---

## 4️⃣ Stereo Matching

For every point in the left image:

- A patch is extracted
- Search is restricted to the epipolar line
- Only left disparities are considered
- Correlation matching is applied

```python
cv2.matchTemplate(
    patchR.astype(np.float32),
    patchL.astype(np.float32),
    cv2.TM_CCOEFF_NORMED
)
```

---

## 🔒 Matching Constraints

Several filters were added to reduce false matches:

### ✅ Minimum score threshold

```python
self.min_score = 0.50
```

Rejects weak correlations.

---

### ✅ Ambiguity rejection

```python
if second_best > 0 and (best_score - second_best) < self.ambiguity_margin:
    return None
```

Rejects unstable correspondences.

---

### ✅ Epipolar constraint

```python
self.row_tolerance = 0
```

The matching only searches on the same row.

This dramatically improves stability.

---

### ✅ Disparity limits

```python
self.min_disparity = 2
self.max_disparity = 120
```

Prevents impossible correspondences.

---

# 📐 5️⃣ Triangulation

The system reconstructs 3D points using the HAL geometric functions:

```python
HAL.graficToOptical()
HAL.backproject()
HAL.getCameraPosition()
```

The rays generated from both cameras are intersected geometrically.

---

## 🧮 Geometric Validation

Bad triangulations are rejected:

```python
if distance > self.max_ray_distance:
    return None
```

This removes floating outliers.

---

## 📍 Final 3D Point

The final reconstructed point is computed as the midpoint between both rays.

---

# 🌐 6️⃣ 3D Visualization

The reconstructed cloud is visualized using WebGUI.

```python
WebGUI.ShowAllPoints(points3D)
```

Each point contains:

```python
[x, y, z, r, g, b]
```

---

# ⚙️ Final Parameters

The final configuration used was:

```python
self.feature_step = 3
self.patch_size = 9
self.max_points = 900

self.min_score = 0.50
self.ambiguity_margin = 0.10

self.min_disparity = 2
self.max_disparity = 120
self.row_tolerance = 0

self.max_ray_distance = 15.0
```

These parameters provided the best balance between:

- ✅ Reconstruction quality
- ✅ Number of points
- ✅ Noise reduction
- ✅ Stability

---

# 📊 Results

The final reconstruction successfully generates a recognizable 3D point cloud.

Objects visible in the reconstruction:

- 🦆 Duck
- 🍄 Mario characters
- 🧱 Blocks
- 🏗️ Scene structure
- 🌌 Scene depth

Although the reconstruction is not perfectly dense, it is:

- Geometrically coherent
- Stable
- Recognizable
- Real-time capable

---

# 🚀 Technologies Used

- Python 🐍
- OpenCV 👁️
- NumPy 🔢
- HAL API 🤖
- WebGUI 🌐
- RoboticsAcademy / Unibotics 🎓

---

# 📂 Main File

Implementation file:

- `code.py`

---

# 🏁 Conclusion

This project demonstrates a complete **software-based stereo reconstruction pipeline** using:

- Edge extraction
- Stereo correspondence
- Epipolar geometry
- Triangulation
- Real-time visualization

The system successfully reconstructs a coherent 3D representation of the environment from stereo images in real time.

---

Although the reconstruction is not perfect, it successfully captures the spatial structure and provides a solid foundation for more advanced computer vision techniques.

---
