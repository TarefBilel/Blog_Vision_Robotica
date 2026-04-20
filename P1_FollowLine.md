# 🚗 Practice 1: Visual Follow Line

---

## 👨‍💻 Author
**Taref Bilel**  
**Master en Visión Artificial**  
**Asignatura:** Visión Robótica  

---

## 📘 Introduction

In the field of mobile robotics, autonomous navigation is a fundamental challenge. One of the most common introductory problems is line following, where a robot must track a path using visual information.

This practice focuses on solving this problem using **computer vision techniques** combined with a **control system**. The robot uses its onboard camera to detect a red line and adjusts its movement in real time.

This approach is widely used in robotics applications such as:
- Autonomous vehicles  
- Industrial robots  
- Smart transportation systems  

---

## 🎯 Objective

The objective of this practice is to design and implement a system that allows a robot to:

- Detect a red line using image processing  
- Compute its position relative to the robot  
- Apply a control strategy to follow the line  
- Maintain stable and smooth movement  

---

## ⚙️ Methodology

### 1. Image Acquisition
The robot continuously captures images using its onboard camera. These images are processed in real time.

---

### 2. Color Detection using HSV Space

To detect the red line, the captured image is converted from the BGR color space to HSV. This transformation is essential because HSV separates color information from brightness, making it more robust under varying lighting conditions.

```python
hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
```

Since red appears at both ends of the HSV spectrum, two different ranges are defined to capture all variations of the color.

---

### 3. Binary Mask Generation

Using the defined HSV ranges, a binary mask is created to isolate the red regions in the image.

```python
mask1 = cv2.inRange(hsv, lower_red1, upper_red1)
mask2 = cv2.inRange(hsv, lower_red2, upper_red2)

mask = mask1 + mask2
```

In this mask:
- White pixels represent the detected red line  
- Black pixels represent the background  

---

### 4. Region of Interest (ROI)

To improve efficiency and reduce noise, only the lower part of the image is processed.

```python
roi = mask[int(h*0.6):h, :]
```

This region is selected because the line is usually closer to the robot.

---

### 5. Line Position Estimation

The position of the detected line is computed using image moments.

```python
moments = cv2.moments(roi)
cx = int(moments["m10"] / moments["m00"])
```

This value represents the horizontal position of the line.

---

### 6. Error Computation

The system calculates the deviation between the detected line and the center of the image.

```python
error = cx - center
```

---

### 7. PD Control Strategy

A PD controller is used to adjust the robot’s direction.

```python
derivative = error - previous_error
previous_error = error

w_cmd = -Kp * error - Kd * derivative
```

The output is limited to ensure stability:

```python
w_cmd = max(min(w_cmd, w_cmd_max), -w_cmd_max)
```

---

### 8. Adaptive Speed Control

The robot adjusts its forward speed depending on the error.

```python
speed = 6 - abs(error) * 0.02
speed = max(2.5, min(6, speed))
```

---

### 9. Recovery Mechanism

If the line is not detected, the robot rotates to find it again.

```python
HAL.setV(2)
HAL.setW(2)
```
