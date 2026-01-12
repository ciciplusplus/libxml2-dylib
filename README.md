# Building libxml2 dylib 

## Prerequisites
Download and extract the common-3.0.sdk:
- Download the latest sdk from: <https://github.com/touchHLE/common-3.0-sdk/releases/latest>
- Extract in project root

## Build Dylib

`cmake -DCMAKE_TOOLCHAIN_FILE="common-3.0.sdk/cmake/Toolchain/common-3.0.cmake" -S . -B build`

`cmake --build build`

## Licensing 

This repository contains components under multiple licenses:

libxml2 dylib binary
- Licensed under libxml2 License
- Full license text in `LICENSE.libxml2`

All other repo content
- Licensed under Mozilla Public License 2.0
- Full license text in `LICENSE`