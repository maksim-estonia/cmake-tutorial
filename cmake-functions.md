## step 1

- `cmake_minimum_required(VERSION 3.10)`

```cmake
cmake_minimum_required(VERSION <min>[...<policy_max>] 
    [FATAL_ERROR]
    )
```
Sets the minimum required version of cmake for a project.

If the running version of CMake is lower than the `<min>` required version it will stop processing the project and report an error.

This command will set the value of the `CMAKE_MINIMUM_REQUIRED_VERSION` variable to `<min>`

- `project(Tutorial VERSION 1.0)`

```cmake
project(<PROJECT-NAME>
        [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
        [DESCRIPTION <project-description-string>]
        [HOMEPAGE_URL <url-string>]
        [LANGUAGES <language-name>...])
```

Sets the name of the project, and stores it in the variable `PROJECT_NAME`. When called from the top-level `CMakeLists.txt` also stores the project name in the variable `CMAKE_PROJECT_NAME`. 

Also sets the variables:

`PROJECT_SOURCE_DIR`, `<PROJECT-NAME>_SOURCE_DIR`
    Absolute path to the source directory for the project.

`PROJECT_BINARY_DIR`, `<PROJECT-NAME>_BINARY_DIR`
    Absolute path to the binary directory of the project.

`PROJECT_IS_TOP_LEVEL`, `<PROJECT-NAME>_IS_TOP_LEVEL`
    Boolean value indicating whether the project is top-level.

- `configure_file(TutorialConfig.h.in TutorialConfig.h)`

```cmake
configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

Copies an `<input>` file to an `<output>` file and substitutes values referenced as `@VAR@` or `${VAR}` in the input file content. Each variable reference will be replaced with the current value of the variable, or the empty string if the variable is not defined. Furthermore, input lines of the form

```
#cmakedefine VAR ...
```

will be replaced with either 

```
#define VAR ...
```

or

```
/* #undef VAR */
```

depending on whether `VAR` is set in CMake to any value not considered a false constant by the `if()` command.

- `set(CMAKE_CXX_STANDARD 11)`
- `set(CMAKE_CXX_STANDARD_REQUIRED True)`

```cmake
set(<variable> <value>... [PARENT_SCOPE])
```

Sets the given `<variable>` in the current function or directory scope.

If the `PARENT_SCOPE` is given the variable will be set in the scope above the current scope. Each new directory or function creates a new scope. This command will set the value of a variable into the parent directory or calling function (whichever is applicable to the case at hand).

- `add_executable(Tutorial tutorial.cxx)`

```cmake
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               [source1] [source2 ...])
```

Adds an executable target called `<name>` to be built from the source files listed in the command invocation. The `<name>` corresponds to the logical target name and must be globally unique within a project. The actual file name of the executable is constructed based on conventions of the native platform. 

- `target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )`

```cmake
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

Specifies include directories to use when compiling a given target. The named `<target>` must have been create by a command such as `add_executable()` or `add_library()` and must not be an ALIAS target.

The `INTERFACE`, `PUBLIC` and `PRIVATE` keywords are required to specify the scope of the following arguments. `PRIVATE` and `PUBLIC` items will populate the `INCLUDE_DIRECTORIES` property of `<target>`. `PUBLIC` and `INTERFACE` items will populate the `INTERFACE_INCLUDE_DIRECTORIES` of `<target>`. The following arguments specify include directories.

- `option(USE_MYMATH "Use tutorial provided math implementation" ON)`

```cmake
option(<variable> "<help_text>" [value])
```

Provides an option for the user to select as `ON` or `OFF`. If no initial `<value>` is provided, `OFF` is used. If `<variable>` is already set as a normal or cache variable, then the command does nothing.

- `configure_file(TutorialConfig.h.in TutorialConfig.h)`

```cmake
configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

Copies an `<input>` file to an `<output>` file and substitutes variable values referenced as `@VAR@` or `${VAR}` in the input file content. Each variable reference will be replaced with the current value of the variable, or the empty string if the variable is not defined. Furthermore, input lines of the form

```
#cmakedefine VAR ...
```

will be replaced with either 

```
#define VAR ...
```

or 

```
/* #undef VAR */
```

depending on whether `VAR` is set in CMake to any value not considered a false constant by the `if()` command. 

## step 2

- `add_subdirectory(MathFunctions)`

```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

Adds a subdirectory to the build. The `source_dir` specifies the directory in which the source `CMakeLists.txt` and code files are located. If it is a relative path it will be evaluated with respect to the current directory (the typical usage), but it may also be an absolute path. The `binary_dir` specifies the directory in which to place the output files. 

- `list(APPEND EXTRA_LIBS MathFunctions)`

```
Reading
  list(LENGTH <list> <out-var>)
  list(GET <list> <element index> [<index> ...] <out-var>)
  list(JOIN <list> <glue> <out-var>)
  list(SUBLIST <list> <begin> <length> <out-var>)

Search
  list(FIND <list> <value> <out-var>)

Modification
  list(APPEND <list> [<element>...])
  list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
  list(INSERT <list> <index> [<element>...])
  list(POP_BACK <list> [<out-var>...])
  list(POP_FRONT <list> [<out-var>...])
  list(PREPEND <list> [<element>...])
  list(REMOVE_ITEM <list> <value>...)
  list(REMOVE_AT <list> <index>...)
  list(REMOVE_DUPLICATES <list>)
  list(TRANSFORM <list> <ACTION> [...])

Ordering
  list(REVERSE <list>)
  list(SORT <list> [...])
```

The list subcommands `APPEND`, `INSERT`, `FILTER`, `PREPEND`, `POP_BACK`, `POP_FRONT`, `REMOVE_AT`, `REMOVE_ITEM`, ``REMOVE_DUPLICATES`,`REVERSE` and `SORT` may create new values for the list within the current CMake variable scope. Similar to the `set()` command, the LIST command creates new variable values in the current scope, even if the list itself is actually defined in a parent scope. To propagate the results of these operations upwards, use `set()` command with `PARENT_SCOPE`, `set()` with `CACHE_INTERNAL`, or some other means of value propagation.

A list in CMake is a `;` separated group of strings. To create a list the set command can be used. For example, `set(var a b c d e)` creates a list with `a;b;c;d;e`, and `set(var "a b c d e")` creates a string or a list with one item in it. 

```
list(APPEND <list> [<element> ...])
```

Appends elements to the list. If no variable named `<list>` exists in the current scope its value is treated as empty and the elements are appended to that empty list. 

- `target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})`

```cmake
target_link_libraries(<target> ... <item>... ...)
```

This command has several signatures as detailed in the subsections below.

The named `<target>` must have been created by a command such as `add_executable()` or `add_library()` and must not be an ALIAS target. 

Each `<item>` may be:

- **A library target name**: The generated link line will have the full path to the linkable library file associated with the target. The buildsystem will have a dependency to re-link `<target>` if the library file changes. 

  The named target must be created by `add_library()` within the project or as an `IMPORTED library`. 

- **A full path to a library file**: The generated link line will normally preserve the full path to the file.

- **A plain library name**: The generated link line will ask the linker to search for the library (e.g. `foo` becomes `-l foo` or `foo.lib`).

- **A link flag**
- **A generator expression**

_Libraries for a Target and/or its Dependents_

```cmake
target_link_libraries(<target>
                      <PRIVATE|PUBLIC|INTERFACE> <item>...
                     [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
```

The `PUBLIC`, `PRIVATE` and `INTERFACE` keywords can be used to specify both the link dependencies and the link interface in one command. Libraries and targets following `PUBLIC` are linked to , and are made part of the link interface. Libraries and targets following `PRIVATE` are linked to, but are not made part of the link interface. Libraries following `INTERFACE` are appended to the link interface and are not used for linking `<target>`. 

## step 3

## step 4

- `install(TARGETS Tutorial DESTINATION bin)`

```cmake
install(TARGETS <target>... [...])
install(IMPORTED_RUNTIME_ARTIFACTS <target>... [...])
install({FILES | PROGRAMS} <file>... [...])
install(DIRECTORY <dir>... [...])
install(SCRIPT <file> [...])
install(CODE <code> [...])
install(EXPORT <export-name> [...])
install(RUNTIME_DEPENDENCY_SET <set-name> [...])
```

This command generates installation rules for a project. Install rules specified by calls to the `install()` command within a source directory are executed in order during installation.

There are multiple signatures for this command. Some of them define installation options for files and targets. Options common to multiple signatures are covered here but they are valid only for signatures that specify them. 

- `DESTINATION`
  Specify the directory on disk to which a file will be installed. Arguments can be relative or absolute paths. 

  If a relative path is given it is interpreted relative to the value of the `CMAKE_INSTALL_PREFIX` variable. The prefix can be relocated at install time using the `DESTDIR` mechanism explained in the `CMAKE_INSTALL_PREFIX` variable documentation.

  If an absolute path (with a leading slash) is given it is used verbatim.

- `PERMISSIONS`
  Specify permission for installed files. Valid permissions are `OWNER_READ`, `OWNER_WRITE`, `OWNER_EXECUTE`, `GROUP_READ`, `GROUP_WRITE`, `GROUP_EXECUTE`, `WORLD_READ`, `WORLD_WRITE`, `WORLD_EXECUTE`, `SETUID` and `SEGID`. 

- `CONFIGURATIONS`
  Specify a list of build configurations for which the install rule applies (Debug, Release, etc.). Note that the values specified for this option only apply to options listed AFTER the `CONFIGURATIONS` option. For example, to set separate install paths for the Debug and Release configurations, do the following: 

```cmake
install(TARGETS target
        CONFIGURATIONS Debug
        RUNTIME DESTINATION Debug/bin)
install(TARGETS target
        CONFIGURATIONS Release
        RUNTIME DESTINATION Release/bin)
```

- `COMPONENT`
  Specify an installation component name with which the install rule is associated, such as "run-time" or "development". During component-specific installation only install rules associated with the given component name will be executed. During a full installation all components are installed unless marked with `EXCLUDE_FROM_ALL`. If `COMPONENT` is not provided a default component "Unspecified" is created.

- `EXCLUDE_FROM_ALL`
- `RENAME`
- `OPTIONAL`
- ...

_Installing Files_

```cmake
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
        DESTINATION include
        )

```
```cmake
install(<FILES|PROGRAMS> files...
        TYPE <type> | DESTINATION <dir>
        [PERMISSIONS permissions...]
        [CONFIGURATIONS [Debug|Release|...]]
        [COMPONENT <component>]
        [RENAME <name>] [OPTIONAL] [EXCLUDE_FROM_ALL])
```

The FILES form specifies rules for installing files for a project. File names given as relative paths are interpreted with respect to the current source directory. Files installed by this form are by default given permissions `OWNER_WRITE`, `OWNER_READ`, `GROUP_READ` and `WORLD_READ` if no `PERMISSIONS` argument is given. 

The `PROGRAMS` form is identical to the `FILES` form except that the default permissions for the installed file also include `OWNER_EXECUTE`, `GROUP_EXECUTE` and `WORLD_EXECUTE`. This form is intended to install programs that are not targets, such as shell scripts. 

Either a `TYPE` or a `DESTINATION` must be provided, but not both. A `TYPE` argument specifies the generic type of the files being installed. A destination will then be set automatically by taking the corresponding variable form `GNUInstallDirs`, or by using a built-in default if that variable is not defined. See the table below for the supported file types and their corresponding variables and built-in defaults.


`TYPE` Argument | GNUInstallDirs Variable | Built-In Default
---------|----------|---------
 `BIN`        | ${CMAKE_INSTALL_} | `bin`
 `SBIN`       | ${CMAKE_INSTALL_} | `sbin`
 `LIB`        | ${CMAKE_INSTALL_} | `lib`
 `INCLUDE`    | ${CMAKE_INSTALL_} | `include`
 `SYSCONF`    | ${CMAKE_INSTALL_} | `etc`
 `SHAREDSTATE`| ${CMAKE_INSTALL_} | `com`
 `LOCALSTATE` | ${CMAKE_INSTALL_} | `var`
 `RUNSTATE`   | ${CMAKE_INSTALL_} | `<LOCALSTATE dir>/run`
 `DATA`       | ${CMAKE_INSTALL_} | `<DATAROOT dir>`
 `INFO`       | ${CMAKE_INSTALL_} | `<DATAROOT>/info`
 `LOCALE`     | ${CMAKE_INSTALL_} | `<DATAROOT>/locale`
 `MAN`        | ${CMAKE_INSTALL_} | `<DATAROOT>/man`
 `DOC`        | ${CMAKE_INSTALL_} | `<DATAROOT>/doc`

- `enable_testing()`

```cmake
enable_testing()
```

Enables testing for this directory and below.

This command should be in the source directory root because the ctest expects to find test file in the build directory root.

- `add_test(NAME Runs COMMAND Tutorial 25)`

```cmake
add_test(NAME <name> COMMAND <command> [<arg>...]
         [CONFIGURATIONS <config>...]
         [WORKING_DIRECTORY <dir>]
         [COMMAND_EXPAND_LISTS])
```

Adds a test called <name>. The options are:

- `COMMAND`

  Specify the test command-line. If `<command>` specifies an executable target (created by `add_executable()`) it will automatically be replaced by the location of the executable created at build time. 

- `CONFIGURATIONS`

  Restrict execution of the test only to the named configurations.

- `WORKING_DIRECTORY`
  
  Set the `WORKING_DIRECTORY` test property to specify the working directory in which to execute the test. If not specified the test will be run with the current working directory set to the build directory corresponding to the current source directory.

The given test command is expected to exit with code 0 to pass and non-zero to fail, or vice-versa if the `WILL_FAIL` test property is set. 

```
set_tests_properties(Usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
  )
```

```
set_tests_properties(test1 [test2...] PROPERTIES prop1 value1 prop2 value2)
```

Sets a property for the tests. If the test is not found, CMake will report an error. 

`PASS_REGULAR_EXPRESSION`: The output must match this regular expression for the test to pass. The process exit code is ignored. 

```cmake
function(do_test target arg result)
...
endfunction()
```

```cmake
function(<name> [<arg1> ...])
  <commands>
endfunction()
```

Defines a function named `<name>` that takes arguments named `<arg1>`,... The `<commands>` in the function definitions are recorded; they are not executed until the function is invoked. 

```cmake
do_test(Tutorial 4 "4 is 2")
do_test(Tutorial 9 "9 is 3")
do_test(Tutorial 5 "5 is 2.236")
do_test(Tutorial 7 "7 is 2.645")
do_test(Tutorial 25 "25 is 5")
do_test(Tutorial -25 "-25 is (-nan|nan|0)")
do_test(Tutorial 0.0001 "0.0001 is 0.01")
```