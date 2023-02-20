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

Alternatively at the project root do:

```
$ ./build.sh
```

This will generate the file `build/debug/Application`, additional size and 
hex files used by some flash memory software tools are also generated.

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

# Runing an Application

To run the application without debugging:

   * in VS code press Ctrl-Shft-P and type `test` 
   * in the popup list select **Tasks: Run Test task**
   * in the list of tasks select **Run Application**

To run with debugging:

   * set a breakpoint in the code
   * press F5 (or run **Debug** task **Debug Application**)
