# CMake Tutorial

[source](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

The tutorial documentation and source code for examples can be found in `CMake/Help/guide/tutorial`. 

## Step 1

The most basic project is an executable built from source code files. 

`CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.10)

# set the project name
project(Tutorial)

# add the executable
add_executable(Tutorial tutorial.cxx)
```

Create a build directory:

`mkdir build && cd build`

Run CMake to configure project and generate a native build system:

`cmake ..`

Call the build system to compile/link the project:

`cmake --build .`

Try to use the newly built `Tutorial` script

`./Tutorial 10`

### Adding a Version number and Configured Header File

The first feature we will add is to provide our executable and project with a version number.

Modify `CMakeLists.txt` to use the `project()` command to set the project name and version number.

```cmake
cmake_minimum_required(VERSION 3.10)

# set the project name
project(Tutorial VERSION 1.0)
```

Then, configure a header file to pass the version number to the source code:

```cmake
configure_file(TutorialConfig.h.in TutorialConfig.h)
```

Since the configured file will be written into the binary tree, we must add that directory to the list of paths to search for include files. 

```cmake
target_include_directories(Tutorial PUBLIC
                            "${PROJECT_BINARY_DIR}"
                            )
```

Create `TutorialConfig.h.in` in the source directory:

```cpp
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

When CMake configures this header file the values for `@Tutorial_VERSION_MAJOR@` and `@Tutorial_VERSION_MINOR@` will be replaced.

Modify `tutorial.cxx` to include the configured header file, `TutorialConfig.h`.

### Specify the C++ Standard

We will need to explicitly state in the CMake code that it should use the correct flags. Make sure to add the `CMAKE_CXX_STANDARD` declarations before the call to `add_executable`. 

### Rebuild

`cd build`

`cmake --build .`