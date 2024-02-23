# HPEQuick
Quick example of Human Pose Estimation using OpenVINO and an OpenPose model

## Requirements

- OpenCV
- OpenVINO 2023.3

## How to Build

First, install the requirements. On Unixes, `brew` or `apt` can be used to install OpenVINO.

**On MacOS**, OpenCV is better installed from source. Follow the instructions on the OpenCV documentation to install it: <https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html>

**On Linux**, `apt install libopencv-dev` is fine.

Then, build the project using CMake (by default, the project is built in `Release` configuration):

```bash
cmake -Bbuild
cmake --build build
```

**On Windows**, follw the instructions on the OpenVINO documentation to install the toolkit: <https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html>. Likewise, for OpenCV follow instrictions on <https://docs.opencv.org/4.x/d3/d52/tutorial_windows_install.html>.

With typical setup dirs for OpenVINO and OpenCV, remember to add to the `PATH` environment variable the dll directories, typically: `C:\OpenCV\build\x64\vc16\bin`, `C:\Program Files (x86)\Intel\openvino_2023.3\runtime\bin\intel64\Release`, and `C:\Program Files (x86)\Intel\openvino_2023.3\runtime\3rdparty\tbb\bin`, assuming that you installed OpenCV on `C:\OpenCV` and OpenVINO under `C:\Program Files (x86)\Intel\openvino_2023.3`.

Then, create the following environment variables to the paths of the respective installation dirs (once and for good):

```powershell
setx OpenCV_DIR C:\OpenCV
setx OpenVINO_DIR 'C:\Program Files (x86)\Intel\openvino_2023.3'
```

Finally, configure the project and build it as follows:

```powershell
cmake -Bbuild 
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