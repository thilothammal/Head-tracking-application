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

📷 How It Works
Python captures video frames and detects the position of the user's face.

The center of the detected face is sent over UDP to Unity.

Unity receives the data and computes a smooth average.

The Unity camera's position and rotation are adjusted based on head movement.

💻 Installation
Python Setup
bash
pip install opencv-python cvzone
Run the tracking script:

bash
python head_tracker.py
Unity Setup
Create a Unity scene and import:

UDPReceive.cs

HeadTracking.cs

Attach HeadTracking.cs to your camera GameObject.

Link the scripts and configure ports (5052 default).

Press Play in Unity while Python is running.

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

Python code

from cvzone.FaceDetectionModule import FaceDetector
import cv2
import socket

cap = cv2.VideoCapture(0)
cap.set(3, 640)
cap.set(4, 480)

detector = FaceDetector(minDetectionCon=0.8)

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
serverAddressPort = ('127.0.0.1', 5052)

while True:
    success, img = cap.read()
    # Finding the faces
    img, bboxs = detector.findFaces(img)

    if bboxs:
        center = bboxs[0]['center']
        data = str.encode(str(center))
        sock.sendto(data, serverAddressPort)

    cv2.imshow("image", img)
    cv2.waitKey(1)




headtracking code
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System.Linq;

public class HeadTracking : MonoBehaviour
{
    public UDPReceive uDPReceive;
    public GameObject cameraObject;
    List<float> xList = new List<float>();
    List<float> yList = new List<float>();


    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        string data = uDPReceive.data;
        data = data.Remove(0, 1);
        data = data.Remove(data.Length-1, 1);

        string[] points = data.Split(',');

        print(points[0]);

        float x = (float.Parse(points[0])-320) / 100;
        float y = (float.Parse(points[1])-240) / 100;
        xList.Add(x);
        yList.Add(y);

        if (xList.Count > 50) { xList.RemoveAt(0); }
        if (yList.Count > 50) { yList.RemoveAt(0); }

        float xAverage = Queryable.Average(xList.AsQueryable());
        float yAverage = Queryable.Average(yList.AsQueryable());


        Vector3 cameraPos = cameraObject.transform.localPosition;
        Vector3 cameraRot = cameraObject.transform.localPosition;


        cameraObject.transform.localPosition = new Vector3(-26.75f- xAverage, 2.9f- yAverage, cameraPos.z);
        cameraObject.transform.localEulerAngles = new Vector3(18.76f- yAverage * 10, xAverage * 10 , 0);

    }
}



UDP receives.cs
using UnityEngine;
using System;
using System.Text;
using System.Net;
using System.Net.Sockets;
using System.Threading;

public class UDPReceive : MonoBehaviour
{

    Thread receiveThread;
    UdpClient client; 
    public int port = 5052;
    public bool startRecieving = true;
    public bool printToConsole = false;
    public string data;


    public void Start()
    {

        receiveThread = new Thread(
            new ThreadStart(ReceiveData));
        receiveThread.IsBackground = true;
        receiveThread.Start();
    }


    // receive thread
    private void ReceiveData()
    {

        client = new UdpClient(port);
        while (startRecieving)
        {

            try
            {
                IPEndPoint anyIP = new IPEndPoint(IPAddress.Any, 0);
                byte[] dataByte = client.Receive(ref anyIP);
                data = Encoding.UTF8.GetString(dataByte);

                if (printToConsole) { print(data); }
            }
            catch (Exception err)
            {
                print(err.ToString());
            }
        }
    }

}
