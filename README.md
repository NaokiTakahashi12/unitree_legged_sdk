# v3.8.0
The unitree_legged_sdk is mainly used for communication between PC and Controller board.
It also can be used in other PCs with UDP.

### Notice
support robot: Go1

not support robot: Laikago, Aliengo, A1. (Check release [v3.3.1](https://github.com/unitreerobotics/unitree_legged_sdk/releases/tag/v3.3.1) for support)

### Sport Mode
```bash
Legged_sport    >= v1.36.0
firmware H0.1.7 >= v0.1.35
         H0.1.9 >= v0.1.35
```

### Dependencies
* [Boost](http://www.boost.org) (version 1.5.4 or higher)
* [CMake](http://www.cmake.org) (version 2.8.3 or higher)
* [g++](https://gcc.gnu.org/) (version 8.3.0 or higher)


### Build
```bash
$ cmake -S . -B build
$ cmake --build build
$ cmake --install build
```

### Run

#### Cpp
Run examples with 'sudo' for memory locking.

#### Python
##### arm
change `sys.path.append('../lib/python/amd64')` to `sys.path.append('../lib/python/arm64')`

### SDK usage
#### C++
You can link to it by adding the following to your `CMakeLists.txt`.

```CMakeLists.txt
...
find_package(UnitreeLeggedSDK REQUIRED)
target_link_libraries(<your cool library or executable>
  PUBLIC
    UnitreeLeggedSDK::UnitreeLeggedSDK
)
...
```

You can include the following in your C++ source.

```cpp
#include <unitree_legged_sdk/unitree_legged_sdk.h>
```
