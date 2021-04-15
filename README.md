# OpenVDB-Getting-Started
Steps taken to get from OpenVDB source to simple compiled C++ application

This repo serves as a record, and hopefully a reference, for the steps taken to get to a simple C++ app using the OpenVDB library. https://github.com/AcademySoftwareFoundation/openvdb

## tl;dr - to get to the end point without worrying about the details listed below

- Download/clone this repo
- Ensure you have downloaded and integrated the vckpg steps directly below
- Overwrite or create OpenVDB folder in Program Files folder with contents of .zip
  - such that you have *C:\Program Files\OpenVDB* with *bin*, *include*, and *lib* from the zip
- open .sln in openvdb_test folder
- Right click project and Build
- Currently only works for Release (not Debug)
- Ctrl - F5 to run without debugging

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

### Prepare the vcpkg libraries for OpenVDB :
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

### Create console project in VS2019 and resolve issues

This project is available in openvdb_test folder.
Created console app, pasted the "Hello, World" code from the OpenVDB documentation examples page:
https://www.openvdb.org/documentation/doxygen/codeExamples.html#sPointsHelloWorld

Add "C:\Program Files\OpenVDB\include" to Properties -> VC++ Directories -> Include Directories

Folder created in solution directory of openvdb_test called "libs", and both openvdb .lib files from the "openvdb-8.0.1\build\openvdb\openvdb\Release" folder were copied over.
Add the below to Properties -> Linker -> Input -> Additional Dependencies
$(SolutionDir)libs\openvdb.lib
$(SolutionDir)libs\libopenvdb.lib

For runtime required dll's, copy all dll's in "C:\dev\openvdb-8.0.1\build\openvdb\openvdb\Release" to "openvdb_test\x64\Release".
The executable openvdb_test.exe is created in "openvdb_test\x64\Release"

A list of errors were found in 7 different .h files in:
"C:\Program Files\OpenVDB\include\openvdb\math\"
and
"C:\Program Files\OpenVDB\include\openvdb\points"
and manually edited and saved.  Requires admin permissions in app used to edit files.  I like VSCode as it offers to run in Admin if it gets stuck.

"C:\Program Files\OpenVDB\include\openvdb\math\Mat.h"

add 
`#define _USE_MATH_DEFINES`
and edit math include to be: 
`#include <math.h>`

see https://stackoverflow.com/questions/2561368/illegal-token-on-right-side-of
added/editted brackets around occurances of max, for example:
```
std::numeric_limits<T>::max()
std::max
```
changed to 
```
(std::numeric_limits<T>::max)()
(std::max)
```

NOTE:

I'm not completely sure that the below edit is valid; yet to reach out for support and/or test.  C:\Program Files\OpenVDB\include\openvdb\points\PointConversion.h lines 575 and 581:
```
mMax(-std::numeric_limits<Real>::max()) {}
```
changed to:
```
mMax(-(std::numeric_limits<Real>::max)()) {}
```

for all uses of max and min in the below:
```
C:\Program Files\OpenVDB\include\openvdb\points\AttributeArray.h
C:\Program Files\OpenVDB\include\openvdb\points\AttributeSet.h
C:\Program Files\OpenVDB\include\openvdb\points\IndexFilter.h
C:\Program Files\OpenVDB\include\openvdb\points\PointGroup.h
C:\Program Files\OpenVDB\include\openvdb\points\PointDataGrid.h
C:\Program Files\OpenVDB\include\openvdb\points\PointConversion.h
```
Depending on your errors, you might make the changes yourself, or you can replace the OpenVDB contents in Program Files with the OpenVDB.zip in repo root folder