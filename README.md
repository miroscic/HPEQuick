# HPEQuick
Quick example of Human Pose Estimation using OpenVINO and an OpenPose model

## Requirements

- OpenCV
- OpenVINO 2023.3

## How to Build

First, install the requirements. On Unixes, `brew` or `apt` can be used to install OpenVINO.

OpenCV is better installed from source. Follow the instructions on the OpenCV documentation to install it: <https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html>

On Windows, follw the instructions on the OpenVINO documentation to install the toolkit: <https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html>

Then, build the project using CMake (by default, the project is built in `Release` configuration):

```bash
cmake -Bbuild
cmake --build build
```

On Windows (with typical setup dirs for OpenVINO and OpenCV):

```bash
cmake -Bbuild -DOpenCV_DIR=C:\OpenCV\build -DOpenVINO_DIR="C:\Program Files (x86)\Intel\openvino_2023.3\runtime\cmake" 
cmake --build build --config Release
```


## How to Run

The program takes a single argument, which is the path to the OpenPOSE model to use for inference. The model is automatically downloaded by CMake in the `models` directory. Tu run the program, use the following command:

```bash
build/quick human-pose-estimation-0001.xml
```

If you have more than one camera, you can specify the camera index as a second argument:

```bash
build/quick human-pose-estimation-0001.xml 1
```