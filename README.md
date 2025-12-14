# Image-Guided Robotic Cannula Alignment

This project for the Medical Robotics course BME714 implements an image-guided procedure for robotic surgical navigation. Using a **Niryo Ned 2** robot, an onboard camera, and **ArUco markers**, the system automatically identifies a target anatomy (simulated by a phantom head) and aligns a surgical tool (cannula) along a planned trajectory from an entry point to an internal tumor.

This project simulates the workflow of clinical systems that use CT or MRI-derived coordinates to guide precise tool alignment by computing and chaining transformations between the robot, camera, marker, and patient frames.

## 🎯 Project Objective

The goal is to program the robot to:
1. **Calibrate** the camera to estimate the pose of a phantom head using ArUco markers.
2. **Compute** the transformation chain linking the robot, camera, marker, and patient.
3. **Calculate** a trajectory vector from a defined entry point to a target tumor.
4. **Execute** a precise movement to align the end-effector with this trajectory, maintaining a safe pre-insertion offset.

## 🖼️ Results

The robot successfully detected the phantom, computed the necessary kinematics, and aligned itself with the entry-to-tumor vector.

### Initial State
The robot starts in an observation pose, searching for the ArUco marker on the phantom.
<img src="BME714%20Lab%205%20Initial%20Robot%20Position.png" width="400" alt="Initial Robot Position">

### Final Alignment
The robot aligns its end-effector with the calculated trajectory, stopping at the safe pre-insertion offset.
<img src="BME714%20Lab%205%20Final%20Robot%20Position.png" width="400" alt="Final Robot Position">

### Accuracy Analysis
| Coordinate | Planned (m) | Executed (m) | Difference (m) |
| :--- | :--- | :--- | :--- |
| **X** | -0.013 | -0.015 | 0.002 |
| **Y** | -0.029 | -0.031 | 0.002 |
| **Z** | 0.205 | 0.200 | 0.005 |

The minor discrepancies (2-5mm) are attributed to camera measurement noise, mechanical tolerances, and calibration limits.

## ⚙️ Methodology

1. **Camera Calibration:** Intrinsic parameters (`mtx.npy`, `dist.npy`) are loaded to correct lens distortion.
2. **Pose Estimation:** OpenCV's `solvePnP` computes the marker pose ($T_{CM}$) relative to the camera.
3. **Kinematic Chaining:**
    - **Forward Kinematics:** Computes the camera frame relative to the world frame ($T_{WC}$) using robot joint angles ($T_{1-5}$) and fixed camera offsets.
    - **World-to-Marker:** $T_{WM} = T_{WC} \times T_{CM}$
    - **World-to-Patient:** $T_{WP} = T_{WM} \times T_{MP}$ (where $T_{MP}$ is a fixed offset).
4. **Trajectory Planning:**
    - Anatomical targets (Entry Point & Tumor) are transformed from Patient frame to World frame.
    - An approach vector $\vec{x}_{new}$ is normalized from Entry $\to$ Tumor.
    - A rotation matrix $R_{new}$ is generated to align the end-effector's x-axis with $\vec{x}_{new}$.
5. **Execution:** A pre-insertion offset ($d_{offset} = 5cm$) is applied to calculate the final target pose, which is sent to the robot controller.

## 🛠️ Prerequisites & Setup

### Hardware
- Niryo Ned 2 Robot
- Phantom head with ArUco marker (Dict 4x4_250)
- Calibrated Camera

### Software Dependencies
- Python 3.x
- [PyNiryo](https://github.com/NiryoRobotics/pyniryo)
- OpenCV (`opencv-python`, `opencv-contrib-python`)
- NumPy
- Matplotlib

### Installation
```bash
pip install pyniryo numpy opencv-python opencv-contrib-python matplotlib
