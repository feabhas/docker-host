# Feabhas Docker Host base C/C++ Training Project

**Contents**
- [Feabhas Docker Host base C/C++ Training Project](#feabhas-docker-host-base-cc-training-project)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Obtaining the course exercises](#obtaining-the-course-exercises)
  - [Using the Visual Studio Code IDE](#using-the-visual-studio-code-ide)
- [Developing an application](#developing-an-application)
  - [Building the application binary](#building-the-application-binary)
- [Running an Application](#running-an-application)
- [Building an exercise solution](#building-an-exercise-solution)
- [Creating the template starter projects](#creating-the-template-starter-projects)
- [VS Code tasks and launch actions](#vs-code-tasks-and-launch-actions)
- [Debugging an application](#debugging-an-application)
  - [VS Code Debugging](#vs-code-debugging)
  - [GDB debugging in the container](#gdb-debugging-in-the-container)
  - [Exiting a session](#exiting-a-session)
- [Static analysis using clang-tidy](#static-analysis-using-clang-tidy)
- [Testing support](#testing-support)
- [C/C++ Versions](#cc-versions)
- [C++20 Modules](#c20-modules)
- [Disclaimer](#disclaimer)

# Prerequisites

You will need the following software to use this project:

   * [Docker desktop](https://www.docker.com/products/docker-desktop/)
   * [Visual Studio Code](https://code.visualstudio.com/) and DevContainers extension (installed through VS Code)

# Getting Started

Download this [Docker Host](https://github.com/feabhas/docker-host) git 
repo to your local machine  using either git or unpacking 
the ZIP archive. 

If possible you should store the repo on your local hard 
drive and avoid using network attached storage as the 
editing and build process is disk I/O intensive.

Avoid placing the folder in an area that is mirrored using OneDrive, 
Google Drive or similar for the same reasons.

Your cloned folder will be called `docker-host` by default but you can 
rename this folder if you wish. This is your workspace folder that you
would normally open using [Visual Studio Code](https://code.visualstudio.com/)
(see later).

## Obtaining the course exercises

Your course joining instructions or you instructor will provide a link
to a [Feabhas GitHub](https://github.com/orgs/feabhas/repositories) 
repo containing the the exercise solutions and and starter templates
required to complete the training exercises. 

From within the `docker-host` workspace folder you have just created 
clone the GitHub **exercises**. You will now have a sub-folder
with a name ending with `_exercises`. 

## Using the Visual Studio Code IDE

If you do not have Visual Studio Code installed then provided your company
security policy permits you to install applications you can 
download it from [Visual Studio Code] (https://code.visualstudio.com/).

Start VS Code and click on the extension icon in the left hand panel
(it shows four squares with the top right one detached from the rest).

In the search box at the top of the left hand panel enter the text

```
dev containers
```
Make sure you include the space. In the **Dev Containers** extension
(from Microsoft) shown in the list of matched extension click 
on the **Install** button and add the Dev Containers extenions.
You may need to restart VS code after doing this. 

In the bottom left corner of the screen there will now be a coloured
icon with an `><` symbol, click on this and select:

   * **Open Folder in container...** 

and open the `docker-host` workspace folder containing
this project.

When VS Code opens the folder this will download a Docker container from 
`feabhas/ubuntu-projects:latest`. This container is configured with a
toolchain for building Feabhas host training projects including:

   * Ubuntu GNU Toolchain and GDB
   * Build tools GNU Make, Ninja and CMake
   * Test tools googletest, gmock, puncover and valgrind

The container is about 3GB and will take a noticeable amount 
of time to download.

VS Code will connect to the remote container as user `feabhas` (password
`ubuntu`) and copy files from an host target project template to
the workspace folder. Within the container the working folder is mapped
onto `~/workspace`.

The `docker-host` workspace folder will now contain the files 
for building and running applications on the Ubuntu image.

# Developing an application

## Building the application binary

The Feahbas project build process uses [CMake](https://cmake.org/) as 
the underlying build system. CMake is itself a build system generator 
and we have configured it to generate the build files used 
by [GNU Make](https://www.gnu.org/software/make/).

Using CMake is a two step process:

   * generate the build configuration files
   * build the application

To simplify this process and to allow you to easily add additional 
source and header files we have created a front end script `build.sh` 
to automate the build.

You can add additional C/C++ source and header files to the `src` directory. If 
you prefer you can place your header files in the `include` directory.

From within VS Code you can use the keyboard shortcut `Ctrl-Shift-B` 
to run one of the build tasks:
    * **Build** standard build
    * **Clean** to remove object and executable files
    * **Reset** to regenerate the CMake build files

Alternatively from within a command shell terminal enter the command:

```
$ ./build.sh
```

This `build.sh` script will detect any source file changes and generate
a new build configuration if required. If new source files are created 
in the `src` folder these will be automatically detected and 
included in the build.

The executable `Application` is created in the folder `build/debug`.

You can add the `-v` option to the build command to see the underlying 
compile and link commands:

```
$ ./build.sh -v
```

To delete all object files and recompile the complete project use
the `clean` option:

```
$ ./build.sh clean
```

To clean the entire build directory and regenerate a new CMake build 
configuration use the `reset` option:

```
$ ./build.sh reset
```

# Running an Application

To run the application without debugging:

   * in VS code press Ctrl-Shft-P and type `test task` 
   * in the popup list select **Tasks: Run Test task**
   * in the list of tasks select **Run Application**

To run with debugging (described in the 
[Debugging an application](#debugging-an-application) section):

   * set a breakpoint in the code
   * press F5 (or run **Debug** task **Debug Application**)

# Building an exercise solution

To build any of the exercise solutions run the script:

```
$ ./build-one.sh N 
```

Where *N* is the exercise number. The exercises must be stored in the 
workspace folder in one of the following locations:
   * A cloned github repo name ending `_exercises`
   * An `exercises/solutions`sub-folder in the workspace
   * A `solutions`sub-folder in the workspace

**NOTE:** this script will copy all files in the `src`  and
`include` directories to a `src.bak` directory in the workspace; 
any files already present in `src.bak` will be deleted.

# Creating the template starter projects

Some training courses supply one or more template starter projects containing
a working application that will be refactored during the exercises.

These templates are used to generate multiple project workspaces in 
named sub folders. To generate the sub projects run the command:

```
$ ./build-template.sh
```

This will generate fully configured projects for each starter template
in a sub folder in the root workspace. Each project
contains a fully configured CMake based build system including a 
copy of the solutions folder. The original toolchain build files in the
project are moved to a `project` sub-folder as they are no longer required.

For each exercise you can now open the appropriate sub-project
folder and work within that folder to build and run your application.

**Note:** if there is a single starter template this will be copied
directly into the `src` and `include` folders rather than create a new
sub folder.

# VS Code tasks and launch actions

VS Code tasks:

   * **Build**
   * **Clean**
   * **Reset**

VS Code test tasks:

   * **Run Application**

VS Code debug tasks (use F5 or Debug view):

   * **Debug Application** 
   * 
# Debugging an application

## VS Code Debugging

To debug your code with the interactive (visual) debugger press the `<F5>` 
key or use the **Run -> Start Debugging** menu.

The debug sessions may stop at the entry to the `main` function and display 
a red error box saying:

```
Exception has occurred.
```

This is normal: just close the warning popup and use the debug 
icon commands at the top of the code window to
manage the debug system. The icons are (from left to right):

   *  **continue** - **stop over** - **step into** - **step return** - **restart** - **stop**
  
A number of debug launch tasks are shown in a drop down list at the top of
the debug view.

Preselect one of the launch options before pressing `<F5>` to debug with:

    * **QEMU debug** to debug using the Python GUI **Connect**
    * **QEMU debug serial** to debug using the Python GUI **Connect+serial** 
    * **QEMU debug container** run without the Python GUI
    * **QEMU debug container serial** to debug without the Python GUI 

## GDB debugging in the container

To debug a program just using the GPIO port requires two terminal sessions.

1. In one terminal invoke the following script:
```
$ ./run_qemu.sh gdb
```
A monitor window will appear and there will be some debug output. 
The QEMU simulation will halt at the first instruction waiting for a 
GDB connection.

2. In another terminal, run GDB with
```
$ ./gdb-qemu.sh
```

Diagnostic output will appear in the `gdb` window ending with prompt to 
continue:
```
...
..
-- Type <RET> for more, q to quit, c to continue without paging--
```

Press <Enter> at this point to see the code of the `main` function 
and the `(gdb)` prompt for debug commands.

3. Type 
   * `c` (continue) to run
   * `n` for next (step-over)
   * `s` for step (step-in)

If GPIO-D pins 8..11 are written to, output will appear in the QEMU windows, 
such as:

```
[led:A on]
[seven-segment 1]
[led:C on]
[seven-segment 5]
[led:A off]
[seven-segment 4]
```

## Exiting a session

To exit:
1. Use Ctrl-C in the GDB window to interrupt an executing process to return to
the `gdb` prompt.

2. Enter the kill (`k`) command to stop the remote qemu process.

3. Finally `q` will quit gdb

# Static analysis using clang-tidy

The CMake build scripts create a `clang-tidy` target in the 
generated build files if `clang-tidy` is in the command search 
path (`$PATH` under Linux).

To check all of the build files run the command:
```
$ ./build.sh clang-tidy
```

To run `clang-tidy` as part of the compilation process edit 
the `CMakeLists.txt` file and uncomment the line starting with
 `set(CMAKE_CXX_CLANG_TIDY`.

# Testing support

Create a sub-directory called `tests` with it's own `CMakeList.txt` and define
your test suite (you don't need to include `enable_testing()` as this is done
in the project root config).

Invoke the tests by adding the `test` option to the build command:

```
./build.sh test
```
Tests are only run on a successful build of the application and all tests.

You can also use `cmake` or `ctest` directly.

If a test won't compile the main application will still have been built. 
You can temporarily rename the `tests` directory to stop CMake building 
the tests, but make sure you run a `./build.sh reset` to regenerate 
the build scripts.

# C/C++ Versions

The build system supports compiling against different versions of C and 
C++ with the default set in `MakeLists.txt` as C11 and C++17. 
The `build.sh` and `build-one.sh` scripts accept a version option to 
choose a different language option. 

For example, to compile for:
   * C99 add the option `--c99` (or `--C99`) or for 
   * C++23 add `--cpp23` (or `--c++23` `--C++23` `--CPP23`)

# C++20 Modules

Support for compiling C++ modules is enabled by creating a file `Modules.txt` 
in the `src` folder and defining each module filename on a separate 
line in this file. The build will always use GNU Make to ensure modules
are compiled in the order defined in the `Modules.txt` file. Following MSVC 
and VS Code conventions the modules should be defined in `*.ixx` files.

# Disclaimer

Feabhas is furnishing these items *"as is"*. Feabhas does not provide any
warranty of them whatsoever, whether express, implied, or statutory,
including, but not limited to, any warranty of merchantability or fitness
for a particular purpose or any warranty that the contents their will
be error-free.

In no respect shall Feabhas incur any liability for any damages, including,
but limited to, direct, indirect, special, or consequential damages arising
out of, resulting from, or any way connected to the use of the item, whether
or not based upon warranty, contract, tort, or otherwise; whether or not
injury was sustained by persons or property or otherwise; and whether or not
loss was sustained from, or arose out of, the results of, the item, or any
services that may be provided by Feabhas.

The items are intended for use as an educational aid.Typically code solutions 
will show best practice of language features that have been introduced during 
the associated training, but do not represent production quality code. 
Comments and structured documentation are not included because the code 
itself is intended to be studied as part of the learning process.
