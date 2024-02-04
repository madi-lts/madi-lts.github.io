---
layout: post
title: OpenCV with C++ and CUDA
category: c++
mathjax: true
tags: c++, opencv, CUDA
---

First install opencv. On arch linux, you may also need to install vtk, glew, and fmt. To compile and link a program:
```
g++ main.cpp `pkg-config --cflags opencv4` `pkg-config --libs opencv4` 
```
