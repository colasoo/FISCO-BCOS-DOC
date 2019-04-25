# Get executable program

Users are free to choose any of the following methods to get executable program of FISCO BCOS. It is recommended to download precompiled binaries from GitHub.
- The officially provided statically linked precompiled files can be run on Ubuntu 16.04 and CentOS 7.2 version and above.
- To get the executable program by source code compilation, reference [source code compilation](get_executable.html#id2).

## Download precompiled fisco-bcos

The statically linked pre-compiler we provided has been tested on Ubuntu 16.04 and CentOS 7. Please download the latest released **pre-compiler** from the [Release](https://github.com/FISCO-BCOS/FISCO-BCOS/releases).


## Source code compilation

```eval_rst
.. note::
    
    The source code compilation is suitable for the users with rich development experience. You need to download dependent library during the compilation process. Please keep network unobstructed. Affected by network and computer configuration, the compilation may take 5-20 minutes.
```

FISCO-BCOS using generic [CMake](https://cmake.org) to build system to generate specific platform building files, which means that no matter what workflow systems you operate, they are very similar:
1.	Install build tools and dependent package (depends on platform).
2.	Clone code from [FISCO BCOS][FSICO-BCOS-GitHub].
3.	Run `cmake` to generate the build file and compile.


### Installation dependencies

- Ubuntu

Ubuntu 16.04 version and above are recommended. The versions below 16.04 have not been tested. The source code compiling depends on the build tools and `libssl`.

```bash
$ sudo apt install -y libssl-dev openssl cmake git build-essential autoconf texinfo
```

- CentOS

CentOS7 version and above are recommended.

```bash
$ sudo yum install -y epel-release
$ sudo yum install -y openssl-devel openssl cmake3 gcc-c++ git
```

- macOS

xcode10 version and above are recommended. macOS dependent package installation depends on [Homebrew](https://brew.sh/).

```bash
$ brew install -y openssl git
```

### Code clone

```bash
$ git clone https://github.com/FISCO-BCOS/FISCO-BCOS.git
```

### Compile

After compilation completes, binary file is located at `FISCO-BCOS/build/bin/fisco-bcos`.

```bash
$ cd FISCO-BCOS
$ git checkout master
$ mkdir build && cd build
# please use cmake3 for CentOS
$ cmake ..
#To add -j4 to accelerate compilation by 4 compilation processes
$ make
```

### Compile options

- BUILD_GM，off default, state secret compilation switch. Open it through `cmake -DBUILD_GM=on ..`

- TESTS, off default, unit test compilation switch. Open it through `cmake -DTESTS=on ..`

- STATIC_BUILD, off default,static compilation switch，supports Ubuntu only. Open it through `cmake -DSTATIC_BUILD=on ..`

- Generate source document.
    ```bash
    # Install Doxygen
    $ sudo apt install -y doxygen graphviz
    # Generate source documentation locate at build/doc
    $ make doc
    ```

[FSICO-BCOS-GitHub]:https://github.com/FISCO-BCOS/FISCO-BCOS
