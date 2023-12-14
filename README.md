# Feabhas Docker Training Project

# Getting Started

Download this git repo to your local machine. If possible you should clone
the repo to your local hard drive and avoid using network attached storage
as the editing and build process is disk I/O intensive.

Avoid placing the folder in an area that is mirrored using OneDrive, 
Google Drive or similar for the same reasons.

Install and then startup VS Code. Use the extension icon (left hand icon
bar) to install the Dev Containers extension from Microsoft. You may need to 
restart VS code after doing this. 

In the bottom left corner of the screen there will now be a green icon with 
an `><` symbol, click on this and select:

   * **Open Folder in container...** 

and open the folder you have just created.

When VS Code opens the folder this will download a Docker container from 
`feabhas/docker-projects:latest`. This container is configured with a
toolchain for building Feabhas embedded training projects including:

   * Host based GCC 11.2 and GDB
   * Build tools GNU Make and CMake 2.23

The container is about 2GB and will take a noticeable amount 
of time to download.

VS Code will connect to the remote container as user `feabhas` (password
`ubuntu`) and copy files from an embedded target project template to
the working folder. Within the container the working folder is mapped
onto `~/workspace`.

This folder will now contain the files for building applications
and running them on the Ubuntu image.

# Building an Application

The Feahbas project build process uses [CMake](https://cmake.org/) as the underlying
build system. CMake is itself a build system generator and we have configured
it to generate the build files used by [GNU Make](https://www.gnu.org/software/make/).

Using CMake is a two step process: generate build files and then build. To simplify 
this and to allow you to add additional source and header files we have 
created a front end script `build.sh` to automate the build.

You can add additional C/C++ source and header files to the `src` directory. If 
you prefer you can place your header files in the `include` directory.

From within VS Code you can use the keyboard shortcut `Ctrl-Shift-B` 
to run one of the build tasks:
    * **Build** standard build
    * **Clean** to remove object and executable files
    * **Reset** to regenerate the CMake build files

Alternatively at the project root run:

```
$ ./build.sh
```

This `build.sh` script will detect any source file changes and generate
a new build configuration if required. If new source files are created 
in the `src` folder these will be automatically detected and 
included in the build.

The executable `Application` is created in the folder `build/debug`
and can be run using the command:

```
$ build/debug/Application
```
You can add a `-v` option to see the underlying build commands:

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

# Building an exercise solution

To build any of the exercise solutions run the script:
```
$ ./build-one.sh N 
```
where `N` is the exercise number. The exercises must be stored in a folder
called `exercises` in one of the following locations:
   * The $HOME folder
   * The workspace`s parent or grandparent folder
   * A sub-folder in the workspace folder

**NOTE:** this script will copy all files in the `src` directory to 
the `src.bak` directory having
removed any files already present in `src.bak`.

# Creating the template starter projects

Some training courses supply one or more template starter projects containing
a working application that will be refactored during the exercises.

These templates are used to generate fully configured projects in 
named subfolders. To generate the sub projects run the command:

```
$ ./build-template.sh
```

This will generate fully configured projects each starter template
as a sub project in teh root workspace. Each sub project
contains a fully configured CMake based build system including a 
copy of the solutions folder. The original toolchain build files in the
project are moved to a `project` sub-folder as they are no longer required.

For each exercise you can now open the appropriate sub-project
folder and work within that folder to build and run your application.

# Runing an Application

To run the application without debugging:

   * in VS code press Ctrl-Shft-P and type `test` 
   * in the popup list select **Tasks: Run Test task**
   * in the list of tasks select **Run Application**

To run with debugging:

   * set a breakpoint in the code
   * press F5 (or run **Debug** task **Debug Application**)

# Static analysis using clang-tidy

The CMake build scripts create a `clang-tidy` target in the generated build files if
`clang-tidy` is in the command search path (`$PATH` under Linux).

To check all of the build files run the command:
```
$ ./build.sh clang-tidy
```

To run `clang-tidy` as part of the compilation process edit the `CMakeLists.txt` file
and uncomment the line starting with `set(CMAKE_CXX_CLANG_TIDY`.

# Testing support

Create a sub-directory called `tests` with it's own `CMakeList.txt` and define
yoru test suite (you don't need to include `enable_testing()` as this is done
in the project root config).

Invoke the tests by adding the `test` option to the build command:

```
./build.sh test
```
Tests are only run on a successful build of the application and all tests.

You can also use `cmake` or `ctest` directly.

If a test won't compile the main application will still have been built. You can
temporarily rename the `tests` directory to stop CMake building the tests, but make
sure you run a `./build.sh reset` to regenerate the build scripts.

# C/C++ Versions

The build system supports compiling against different versions of C and C++ with the 
default set in `MakeLists.txt` as C11 and C++17. The `build.sh` and `build-one.sh` scripts
accept a version option to choose a different language option. To compile against C99 add 
the optiuon `--c99 (or --C99) or for C++20 add --cpp20 (or --c++20 --C++20 --CPP20).

# C++20 Modules

Support for compiling C++ modules is enabled by creating a file `Modules.txt` in the
`src` folder and defining each module filename on a separate line in this file. The build 
ensures modules are compiled in the order defined in the `Modules.txt` file and before the 
main `src` files. Following MSVC and VS Code conventions the modules should be defined 
in `*.ixx` files.

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
