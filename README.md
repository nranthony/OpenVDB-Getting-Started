# OpenVDB-Getting-Started
Steps taken to get from OpenVDB source to simple compiled V++ application

This repo serves as a record, and hopefully a reference, for the steps taken to get to a simple C++ app using the OpenVDB library. https://github.com/AcademySoftwareFoundation/openvdb

### Download/setup https://github.com/microsoft/vcpkg:
```
git clone https://github.com/microsoft/vcpkg
cd C:\dev\vcpkg
bootstrap-vcpkg.bat
```
and install for Visual Studio (assuming already installed with C++ dev option checked; here using VS2019 Community)
```
vcpkg integrate install
```

### Prepare the vcpkg libraries for OpenVDB https://github.com/microsoft/vcpkg:
```
vcpkg install zlib:x64-windows
vcpkg install blosc:x64-windows
vcpkg install tbb:x64-windows
vcpkg install boost-iostreams:x64-windows
vcpkg install boost-system:x64-windows
vcpkg install boost-any:x64-windows
vcpkg install boost-algorithm:x64-windows
vcpkg install boost-uuid:x64-windows
vcpkg install boost-interprocess:x64-windows
vcpkg install openexr:x64-windows

```
Note, here I've added `openexr` to the list detailed on the OpenVDB github page.  Also note that if you copy paste, double check the last line is executed before moving on.

### Starting from OpenVDB 8.0.1 release here: https://github.com/AcademySoftwareFoundation/openvdb/releases which I've downloaded/cloned into C:\dev\

Using cmd (Run as Administrator, not just logged into an Administrator account; you should see "Adminsitrator" in the cmd window title):
```
cd C:\dev\openvdb-8.0.1
mkdir build
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=C:\dev\vcpkg\scripts\buildsystems\vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x64-windows -A x64 ..
cmake --build . --parallel 4 --config Release --target install

```
Again, double check the last line runs.

### Create console project in VS2019

This project is available in openvdb_test folder.


