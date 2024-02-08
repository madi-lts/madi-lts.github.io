---
layout: post
title: OpenCV with C++ and CUDA
category: c++, CUDA
mathjax: true
tags: c++, opencv, CUDA
---

First install `opencv`. On arch linux, you may also need to install `vtk`, `glew`, and `fmt`. You also need `qt6-base`, `hdf5`, and possibly more! Scan the output of `ld` the see what dependencies are missing. To compile and link a program:
```
g++ main.cpp `pkg-config --cflags opencv4` `pkg-config --libs opencv4` 
```
For a first program, let's display an 8-bit monochrome image of white noise:

```c++
#include <iostream>
#include <vector>
#include <random>
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>

using namespace cv;
using random_bytes_engine = 
    std::independent_bits_engine<std::default_random_engine, 
                                 UINT8_WIDTH, unsigned char>;

int main()
{
    int ImgSize = 512 * 512 * 1;
    

    random_bytes_engine rbe;
    std::vector<unsigned char> data(ImgSize);
    std::generate(begin(data), end(data), std::ref(rbe));
    
    Mat image = Mat(512, 512, CV_8UC1, 
                    reinterpret_cast<unsigned char*>(data.data()));

    imshow("Display window", image); 
    waitKey(0);
    return 0;
}
```
OpenCV's `imshow()` displays the image in a GUI window:
![opencv imshow()](/assets/images/2024-02-03/opencv-test.png)
Note that the controls above allow us to pan, zoom, save, and copy the image to the clipboard.

## Building CPU and CUDA code with Make
To integrate OpenCV with CUDA code, we could use the `nvcc` compiler and pass the same options as before:
```
nvcc main.cu `pkg-config --cflags opencv4` `pkg-config --libs opencv4`
```
But since we also have a large amount of CPU only code, the proper way to develop here is to compile the CUDA code with `nvcc` into a library:
```
nvcc -c cuda_code.cu
```
This will generate `cuda_code.o`, which we can then link using `g++`:
```
g++ -o program `pkg-config --cflags opencv4` `pkg-config --libs opencv4 cuda cudart` cuda_code.o main.cpp  
```
To streamline the build process, use Make. Create a file named `makefile`:
```make
all: program

program: cuda_code.o
    g++ -o program `pkg-config --cflags opencv4` `pkg-config --libs opencv4 cuda cudart` cuda_code.o main.cpp -I.

cuda_code.o:
    nvcc -c cuda_code.cu

clean:
    rm -f *.o program

```
The indentation in the `makefile` must be done with tabs, and every line must have an end of line character. 
So make sure to convert spaces to tabs in your text editor. Also note the `-I.` flag which tells `g++` to look
in the current directory.

## CMake
Building with Make is a platform specific process. To generalize our build instructions to multiple platforms, we can use CMake.

```cmake
cmake_minimum_required(VERSION 3.28.0)
set(PROJECT_NAME project.out)
project(${PROJECT_NAME}
	VERSION 1.0 
	LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_CXX_EXTENSIONS ON)

find_package(OpenCV REQUIRED)

add_executable(${PROJECT_NAME} main.cpp)
target_include_directories(${PROJECT_NAME} PUBLIC ./)	
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
```