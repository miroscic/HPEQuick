# HPEQuick
Quick example of Human Pose Estimation using OpenVINO and an OpenPose model

## Requirements

- OpenCV
- OpenVINO 2023.3

## How to Build

First, install the requirements. On Unixes, `brew` or `apt` can be used to install OpenVINO.

OpenCV is better installed from source. Follow the instructions on the OpenCV documentation to install it: <https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html>

On Windows, follw the instructions on the OpenVINO documentation to install the toolkit: <https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html>

Then, build the project using CMake:

```bash
cmake -Bbuild -DCMAKE_BUILD_TYPE=Release
```

## How to Run

The program takes a single argument, which is the path to the OpenPOSE model to use for inference. The model can be downloaded from the OpenVINO model zoo with the following command:

```bash
wget https://storage.openvinotoolkit.org/repositories/open_model_zoo/2023.0/models_bin/1/human-pose-estimation-0001/FP32/human-pose-estimation-0001.xml
wget https://storage.openvinotoolkit.org/repositories/open_model_zoo/2023.0/models_bin/1/human-pose-estimation-0001/FP32/human-pose-estimation-0001.bin
```

Then, run the program:

```bash
build/quick human-pose-estimation-0001.xml
```