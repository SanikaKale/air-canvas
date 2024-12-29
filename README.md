# air-canvas
First year Project Based learning Air canvas project

# Air Canvas Project

## Overview
The Air Canvas project enables users to draw on a virtual canvas by moving a colored marker in front of a camera. The project uses computer vision techniques to detect the marker and trace its movement, creating drawings in real-time.

---

## Features
- Real-time marker detection using HSV color space.
- Adjustable ink colors and brush sizes via on-screen buttons.
- Noise reduction through morphological operations.
- Contour detection to identify the marker's position.
- Dynamic drawing by tracking the marker's movement.

---

## Prerequisites
- Python 3.x
- OpenCV Library

Install dependencies using pip:
```bash
pip install opencv-python numpy
```

---

## Basic Flowchart
```plaintext
+-------------------+
| Start Application |
+-------------------+
          |
          v
+---------------------------+
| Capture frames from camera |
+---------------------------+
          |
          v
+---------------------------+
| Convert frames to HSV color |
+---------------------------+
          |
          v
+-----------------------------+
| Prepare canvas and ink buttons |
+-----------------------------+
          |
          v
+---------------------------------+
| Adjust track bar for marker mask |
+---------------------------------+
          |
          v
+------------------------------+
| Apply morphological operations |
+------------------------------+
          |
          v
+--------------------------+
| Detect marker and contours |
+--------------------------+
          |
          v
+---------------------------------+
| Track marker and draw on canvas |
+---------------------------------+
          |
          v
+-------------------+
| Display the output |
+-------------------+
```

---

## Steps to Implement

### Step 1: Read Frames and Convert to HSV
- Capture frames from the webcam using OpenCV.
- Convert the captured frames to HSV color space for better color detection.

```python
import cv2

cap = cv2.VideoCapture(0)
while True:
    ret, frame = cap.read()
    if not ret:
        break
    hsv_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    cv2.imshow('HSV Frame', hsv_frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
cap.release()
cv2.destroyAllWindows()
```

### Step 2: Prepare Canvas and Ink Buttons
- Create a blank canvas for drawing.
- Add buttons for ink colors and other options.

### Step 3: Adjust Track Bar Values for Mask
- Use a trackbar to adjust HSV thresholds to isolate the marker's color.

```python
def nothing(x):
    pass

cv2.namedWindow("Trackbars")
cv2.createTrackbar("Lower-H", "Trackbars", 0, 179, nothing)
cv2.createTrackbar("Lower-S", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("Lower-V", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("Upper-H", "Trackbars", 179, 179, nothing)
cv2.createTrackbar("Upper-S", "Trackbars", 255, 255, nothing)
cv2.createTrackbar("Upper-V", "Trackbars", 255, 255, nothing)
```

### Step 4: Preprocess Mask with Morphological Operations
- Apply erosion and dilation to reduce noise in the mask.

```python
mask = cv2.inRange(hsv_frame, lower_hsv, upper_hsv)
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
mask = cv2.erode(mask, kernel, iterations=1)
mask = cv2.dilate(mask, kernel, iterations=1)
```

### Step 5: Detect Contours and Track Marker
- Identify contours in the mask and find the largest one.
- Extract the center of the largest contour and save the coordinates.

```python
contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
if contours:
    largest_contour = max(contours, key=cv2.contourArea)
    moments = cv2.moments(largest_contour)
    if moments["m00"] != 0:
        cx = int(moments["m10"] / moments["m00"])
        cy = int(moments["m01"] / moments["m00"])
        points.append((cx, cy))
```

### Step 6: Draw on Canvas
- Use the stored coordinates to draw lines on the canvas.

```python
for i in range(1, len(points)):
    cv2.line(canvas, points[i-1], points[i], (255, 0, 0), 5)
cv2.imshow('Canvas', canvas)
```

---

## Usage
1. Run the script.
2. Adjust trackbars to isolate the desired marker color.
3. Start drawing by moving the marker in front of the camera.
4. Use the buttons to switch colors or clear the canvas.

---

## Contribution
Feel free to improve the project by:
- Adding new features like shape drawing or eraser functionality.
- Enhancing marker detection accuracy.
- Integrating AI models for advanced tracking.

---
