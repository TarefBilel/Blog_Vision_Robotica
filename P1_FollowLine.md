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

### 2. Color Detection (HSV)

The image is converted from BGR to HSV color space:

```python
hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
