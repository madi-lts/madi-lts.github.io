---
layout: post
title: OpenCV with C++ and CUDA
category: c++
mathjax: true
tags: c++, opencv, CUDA
---

First install `opencv`. On arch linux, you may also need to install `vtk`, `glew`, and `fmt`. To compile and link a program:
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

## CUDA
W