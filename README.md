ORB_SLAM3 IN WINDOWS 11, VISUAL STUDIO 2022 

# 1 References
- ORB-SLAM3 original project: [link](https://github.com/UZ-SLAMLab/ORB_SLAM3)
- ORB-SLAM3 for Windows: [link-1 (2020.8)](https://github.com/chanho-code/ORB-SLAM3forWindows) [link-2 (2021.1)](https://github.com/lydieusang/orbslam3-windows)
- ORB-SLAM2 for Windows: [link](https://github.com/Phylliida/orbslam-windows)

# 2 Prerequisites
- Visual Studio: tested with Visual Studio 2022
- CMake GUI: tested with 3.30.3
- boost: tested with [1.78.0](https://www.boost.org/releases/1.78.0/)
	- Please use `b2.exe --toolset=msvc variant=release link=static threading=multi runtime-link=static stage` to build the library to align with `/MT` VS settings (see [link](https://stackoverflow.com/questions/4619764/do-i-have-static-or-dynamic-boost-libraries))
- OpenCV: required at least 3.0, tested with [3.4.16](https://github.com/opencv/opencv/releases/tag/3.4.16)
    - Please add the folder containing `opencv_world3416.dll` to PATH before running the example `.exe`. (e.g. `...\opencv3.4.16\build\x64\vc14\bin`).

# 3 Build third-party libraries

## 3.1 Build DBoW2
- Included in Thirdparty
- Create a `build` directory in `Thirdparty/DBoW2`
- Open CMake GUI, click Configure and select `Visual Studio 17 2022`
- Enter value `OpenCV_DIR`: path to OpenCV build (e.g. `...\opencv3.4.16\build`)
- Click Configure until no variables are considered new, Generate, open project in Visual Studio
- Change build type to `Release`
- Open project properties (not solution properties!)
	-> General tab -> Configure Type: Static Library (.lib)
	-> Advanced tab -> Target File Extension: .lib
	-> C/C++ Tab -> Code Generation -> Runtime Library: Multi-threaded(/MT)
- Build ALL_BUILD. There are some warnings but no errors.
- Build succeeded with `Thirdparty/DBoW2/lib/Release/DBoW2.lib`

## 3.2 Build eigen
- Included in Thirdparty
- Required by g2o

## 3.3 Build g2o
- Included in Thirdparty
- Create a `build` directory in `Thirdparty/g2o`
- Open CMake GUI, click Configure and select `Visual Studio 17 2022`
- Click Generate and open the project in Visual Studio
- Change build type to `Release`
- Open project properties 
	-> General tab -> Configure Type: Static Library (.lib)
	-> Advanced tab -> Target File Extension: .lib
	-> C/C++ Tab -> Code Generation -> Runtime Library: Multi-threaded(/MT)
	-> C/C++ Tab -> Preprocessor -> Preprocessor Definitions: add a 'WINDOWS' on top 
- Build ALL_BUILD. There are some warnings but no errors.
- Build succeeded with `Thirdparty/g2o/build/Release/g2o.lib`

## 3.4 Build Pangolin
- Included in Thirdparty
- Create a `build` directory in Thirdparty/Pangolin
- Open CMake GUI, click Configure and select Visual Studio 17 2022
- Click Configure until no variables are considered new, generate and open the project in Visual Studio
- Change build type to `Release`
- Open project properties 
	-> C/C++ Tab -> Code Generation -> Runtime Library: Multi-threaded(/MT) 
- Build ALL_BUILD. Ignore the error `cannot open input file pthread.lib`
- Build succeeded with `ThirdParty/Pangolin/lib/Release/pangolin.lib`

# 4 BUILD ORB-SLAM3
- Create a `build` directory in ORB-SLAM3
- Open CMake GUI, click Configure and select Visual Studio 17 2022
- Enter value `OpenCV_DIR`: path to OpenCV build (e.g. `...\opencv3.4.16\build`)
- Click Configure until no variables are considered new, generate and open the project in Visual Studio
- Change build type to `Release`
- Set boost include and lib (can be done in `CMakeLists.txt` where places need to be changed: 1. in `include_directories(...)`, 2. in `target_link_libraries(...)`)
- Open project properties 
	-> General tab -> Configure Type: Static Library (.lib)
	-> Advanced tab -> Target File Extension: .lib
	-> C/C++ Tab -> Code Generation -> Runtime Library: Multi-threaded(/MT)
- Build ORB-SLAM3
- Build succeeded with `ORB_SLAM3/build/Release/ORB-SLAM3.lib`

# 5 BUILD examples

- Extract `ORB_SLAM3/Vocabulary/ORBvoc.txt.tar.gz` to `ORB_SLAM3/Vocabulary/ORBvoc.txt`

## 5.1 stereo_inertial_euroc (tested with VS2022)

reference: [link](https://zhuanlan.zhihu.com/p/372299812)

### Dataset

- Download The EuRoC MAV Dataset [here](https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets). Choose Machine Hall 01 - ASL Dataset Format (`.zip`, [link](http://robotics.ethz.ch/~asl-datasets/ijrr_euroc_mav_dataset/machine_hall/MH_01_easy/MH_01_easy.zip))
- Extract all the folders in the `.zip` into `ORB_SLAM3/Examples/Stereo-Inertial/MH01`

### BUILD
- Change build type to Release
- Open project properties
	-> C/C++ tab -> Code Generation -> Runtime Library: Multi-threaded(/MT)
	-> Linker tab -> Advanced -> delete path in Import library
- Build `stereo_inertial_euroc`
- Build succeeded with `ORB_SLAM3/Examples/Stereo-Inertial/Release/stereo_inertial_euroc.exe`
- At `ORB_SLAM3/Examples/Stereo-Inertial/Release`, run the following command to test the example:
```cmd
.\stereo_inertial_euroc.exe ..\..\..\Vocabulary\ORBvoc.txt ..\EuRoC.yaml ..\MH01\ ..\EuRoC_TimeStamps\MH01.txt dataset-MH01_stereoi
```
- Result:
![example screenshot](https://github.com/Chesium/orbslam3-windows/blob/master/Examples/Stereo-Inertial/example_stereo_inertial_euroc.png)

## 5.2 stereo_inertial_tum_vi (not tested with VS2022)
- Change build type to Release
- Open project properties
	-> C/C++ tab -> Code Generation -> Runtime Library: Multi-threaded(/MT)
	-> Linker tab -> Advanced -> delete path in Import library
- Build `stereo_inertial_tum_vi`
- Build succeeded with `ORB_SLAM3/Examples/Stereo-Inertial/Release/stereo_inertial_tum_vi.exe`
- Run: Examples\Stereo-Inertial\Release\stereo_inertial_tum_vi
	path_to_vocabulary
	path_to_settings
	path_to_image_folder_1
	path_to_image_folder_2
	path_to_times_file
	path_to_imu_data
	trajectory_file_name
- Result
![alt text](https://github.com/ds-ly/orbslam3-windows/blob/master/example_stereo_intertial_tum_vi.png)
