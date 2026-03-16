# video360-agent

A prototype pipeline for analyzing participant behavior in **360° meeting videos**.

The system converts a panoramic meeting recording into participant-level video streams and extracts behavioral signals that can later be used for action detection and behavioral logging.

---

## Pipeline

The current repository implements the first two stages:

1. **Participant Auto-Cropping**
2. **Behavioral Feature Extraction**

Future stages will include:

- action detection
- behavioral event logging

---

# 01. Participant Auto-Cropping

This module detects participants in a **360° panoramic meeting video** and generates stabilized per-person cropped videos.

The purpose of this step is to transform a panoramic recording into **clean participant-level video streams** that can be used for downstream analysis such as face landmarks, pose estimation, and behavior coding.

### What it does

- reads a stitched 360° meeting video
- samples frames from the video
- detects people using **YOLOv8**
- assigns detections to spatial participant slots
- aggregates detections across frames to estimate stable bounding boxes
- generates crop windows that preserve face, upper body, and desk area
- exports one cropped video per participant
- supports empty slots when fewer participants are present

### Input

### Example input:
- output_top.mp4


### Output

### Example output directory:
autocrop_out/
├── person_slot0_640x900.mp4
├── person_slot2_640x900.mp4
├── person_slot3_640x900.mp4
└── debug_crop_windows.jpg


Each video corresponds to a stabilized view of one participant.

### Debug visualization

![Debug Crop Windows](debug_crop_windows.jpg)

---

# 02. Behavioral Feature Extraction

This module extracts **frame-level behavioral signals** from the cropped participant videos.

It uses facial landmarks and body pose estimation to convert raw video frames into interpretable behavioral features.

### Methods

- **MediaPipe FaceMesh** for facial landmark detection
- **MediaPipe Pose** for body pose estimation

### Extracted signals

#### Head motion

- `yaw_proxy` – horizontal head orientation
- `pitch_proxy` – vertical head tilt

#### Facial expression

- `smile_score` – normalized mouth width
- `mouth_open_score` – mouth opening level

#### Hand activity

- `left_wrist_x`, `left_wrist_y`
- `right_wrist_x`, `right_wrist_y`
- `left_wrist_speed`
- `right_wrist_speed`

#### Detection flags

- `face_detected`
- `pose_detected`

### Output

A dataframe containing frame-level behavioral signals.

### Example columns:
frame_idx
t
face_detected
pose_detected
yaw_proxy
pitch_proxy
smile_score
mouth_open_score
left_wrist_x
left_wrist_y
right_wrist_x
right_wrist_y
left_wrist_speed
right_wrist_speed



Each row corresponds to one video frame.

---

## Requirements

Python 3.10+

Install dependencies:

```bash
pip install ultralytics opencv-python mediapipe numpy pandas

