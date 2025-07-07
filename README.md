# Head-tracking-application
A head tracking application can be developed using Python for computer vision and Unity for creating the 3D environment, combining them to track head movement and translate it into Unity's scene.
🧠 Real-Time Head Tracking with Python & Unity
A cross-platform project that uses computer vision in Python and camera control in Unity to track head movements in real time. The system detects the user’s face via webcam and transmits the center coordinates to Unity, dynamically adjusting the scene camera to create an immersive experience.

🔧 Technologies Used
Python

OpenCV for webcam input

cvzone for face detection

socket for UDP data transmission

Unity

UDPReceive.cs to capture data from Python

HeadTracking.cs to control the camera

Real-time smoothing and mapping




📁 Project Structure
HeadTrackingApp/
├── Unity/
│   └── Assets/
│       └── Scripts/
│           ├── UDPReceive.cs
│           └── HeadTracking.cs
├── Python/
│   └── head_tracker.py
├── README.md
🚀 Applications
VR/AR simulations

Motion-driven camera scenes

Facial input for interactive installations

Gesture-based UI navigation


