# CMake Tutorial

[source](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

The tutorial documentation and source code for examples can be found in `CMake/Help/guide/tutorial`. 

- [CMake Tutorial](#cmake-tutorial)
  - [Step 1](#step-1)
    - [Adding a Version number and Configured Header File](#adding-a-version-number-and-configured-header-file)
    - [Specify the C++ Standard](#specify-the-c-standard)
    - [Rebuild](#rebuild)
  - [Step 2: Adding a Library](#step-2-adding-a-library)
  - [Step 3: Adding Usage Requirements for a Library](#step-3-adding-usage-requirements-for-a-library)
  - [Step 4: Installing and Testing](#step-4-installing-and-testing)
    - [Install Rules](#install-rules)
    - [Testing Support](#testing-support)

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

## Step 2: Adding a Library

We will put the library into a subdirectory called `MathFunctions`. This directory contains a header file, `MathFunctions.h`, and a source file `mysqrt.cxx`.

Add the following one line `CMakeLists.txt` file to the MathFunctions directory

```cmake
add_library(MathFunctions mysqrt.cxx)
```

To make use of the new library we will add an `add_subdirectory()` call in the top-level `CMakeLists.txt` file so that the library will get build.

```cmake
target_link_libraries(Tutorial PUBLIC MathFunctions)

target_include_directories(Tutorial PUBLIC
                          "${PROJECT_BINARY_DIR}"
                          "${PROJECT_SOURCE_DIR}/MathFunctions"
                          )
```

Now let us make `MathFunctions` library optional. The first step is to add an option to the top-level `CMakeLists.txt`. 

```cmake
option(USE_MYMATH "Use tutorial provided math implementation" ON)
```

This option will be displayed in the `cmake-gui` and `ccmake` with a default value of `ON` that can be changed by the user. 

The next change is to make building and linking the `MathFunctions` library conditional. 

```cmake
if(USE_MYMATH)
  add_subdirectory(MathFunctions)
  list(APPEND EXTRA_LIBS MathFunctions)
  list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
endif()

# add the executable
add_executable(Tutorial tutorial.cxx)

target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           ${EXTRA_INCLUDES}
                           )
```

Note the use of the variable `EXTRA_LIBS` to  collect any optional libraries to later be linked into the executable. The variable is used similarly for optional header files.

First, in `tutorial.cxx`, include the `MathFunctions.h` header if we need it:

```
#ifdef USE_MYMATH
#   include "MathFunctions.h"
#endif
```

Then, in the same file, make USE_MYMATH control which square root function is used:

```cmake
#ifdef USE_MYMATH
    const double outputValue = mysqrt(inputValue);
#else
    const double outputValue = sqrt(inputValue);
#endif
```

Since the source code now requires `USE_MYMATH` we can add it to `TutorialConfig.h.in` with the following line:

Build:

```
cd build
cmake ..
cmake --build .
```

or using option `USE_MYMATH`:

```
cmake .. -D USE_MYMATH=OFF
cmake --build .
```

## Step 3: Adding Usage Requirements for a Library

Usage requirements allow for far better control over a library or executable's link and include line while also giving more control over the transitive property of targets inside CMake. The primary commands:

- `target_compile_definitions()`
- `target_compile_options()`
- `target_include_directories()`
- `target_link_libraries()`

Let's refactor our code to use the modern CMake approach of usage requirements. We first state that anybody linking to `MathFunctions` needs to include the current source directory, while `MathFunctions` itself doesn't. So this can become an `INTERFACE` usage requirement. 

Remember `INTERFACE` means things that consumers require but the producer doesn't. Add the following line to the end of `MathFunctions/CMakeLists.txt`:

```cmake
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
```

Now that we've specified usage requirements for `MathFunctions` we can safely remove our uses of the `EXTRA_INCLUDES` variable from the top-level `CMakeLists.txt`.

```cmake
if(USE_MYMATH)
    add_subdirectory(MathFunctions)
    list(APPEND EXTRA_LIBS MathFunctions)
    #list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
endif()
```

and here:

```cmake
# add configured file directory to the list of paths to search for include files
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           #${EXTRA_INCLUDES}
                           )
```

## Step 4: Installing and Testing

### Install Rules

The install rules are simple: for `MathFunctions` we want to install the library and header file and for the application we want to install the executable and configured header.

To the end of `MathFunctions/CMakeLists.txt` we add:

```cmake
install(TARGETS MathFunctions DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
```

And to the end of the top-level `CMakeLists.txt` we add:

```cmake
install(TARGETS Tutorial DESTINATION bin)
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  DESTINATION include
  )
```

This is all we need to create a basic local install of the tutorial.

Now configure and build:

```
cd build
cmake ..
cmake --build .
```

Then run the install step by using the `install` option of the `cmake` command.

```
cmake --install .
```

### Testing Support

At the end of `CMakeLists.txt` file we can enable testing and then add a number of basic tests to verify that the application is working correctly.

```end
enable_testing()

# does the application run
add_test(NAME Runs COMMAND Tutorial 25)

# does the usage message work?
add_test(NAME Usage COMMAND Tutorial)
set_tests_properties(Usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
  )

# define a function to simplify adding tests
function(do_test target arg result)
  add_test(NAME Comp${arg} COMMAND ${target} ${arg})
  set_tests_properties(Comp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endfunction()

# do a bunch of result based tests
do_test(Tutorial 4 "4 is 2")
do_test(Tutorial 9 "9 is 3")
do_test(Tutorial 5 "5 is 2.236")
do_test(Tutorial 7 "7 is 2.645")
do_test(Tutorial 25 "25 is 5")
do_test(Tutorial -25 "-25 is (-nan|nan|0)")
do_test(Tutorial 0.0001 "0.0001 is 0.01")
```

The first test simply verifies that the application runs, does not segfault or otherwise crash, and has a zero return value. This is the basic form of a CTest.

Configure and build:

```
cd build
cmake .. -D USE_MYMATH=OFF
cmake --build .
```

Test:

```
ctest -N
ctest -VV
```

