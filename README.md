# The Checked C LLVM test-suite repo

This repo contains a version of the LLVM test-suite repo that is being modified
to use Checked C. The modified programs will be used to benchmark the Checked C
version of LLVM/clang.

We have deleted test-only code from the main branch of the repo and left only
benchmarks in the main branch.  That makes the repo easier to work with.  It
decreases disk usage from about 2.3 GBytes to under 500 MBytes when using the
main branch.

Checked C is an extension to C that adds checking to detect or prevent common 
programming  errors such as out-of-bounds memory accesses.  For more information
on Checked C, see the Checked C specification in the
[Checked C repo](https://github.com/Microsoft/checkedc).  The Checked C
version of LLVM/clang lives in two repos: the
[Checked C clang repo](https://github.com/Microsoft/checked-clang)
and the [Checked C LLVM repo](https://github.com/Microsoft/checkedc-llvm).


## Branch organization

There are 3 branches in the repo:
- main: this branch contains benchmarks, some of which may have been modified
to use Checked C.
- baseline: this branch contains benchmarks that have not been modified.
- original: this contains all the tests, including application tests.

This main branch should be used for modifying benchmarks.  This branch can be diffed
against the baseline branch to see the changes in benchmarks.
The original branch can be used to test that
the Checked C implementation has not broken existing tests.

## Running tests

This has been adapted from the `test-suite`
[Quickstart guide](https://llvm.org/docs/TestSuiteGuide.html).

Prerequisite: Make sure you have checked out and
[built](https://github.com/checkedc/checkedc-llvm-project/blob/main/clang/docs/checkedc/Setup-and-Build.md)
the Checked C compiler.

1. The lit test runner is required to run the tests. You can use the one from
your Checked C compiler build:
```
% <path to llvm build>/bin/llvm-lit --version
lit 0.8.0dev
```
2. Check out the test-suite repo:
```
 git clone https://github.com/checkedc/checkedc-llvm-test-suite.git test-suite
 ```

 3. Create a build directory and use CMake to configure the suite. Use the
 `CMAKE_C_COMPILER`` option to specify the compiler to test. Use a cache file
to choose a typical build configuration:
```
% mkdir test-suite-build
% cd test-suite-build
% cmake -DCMAKE_C_COMPILER=<path to llvm build>/bin/clang \
        -C../test-suite/cmake/caches/Debug.cmake \
        ../test-suite
```
To run tests with optmizations enabled, use
```
-C../test-suite/cmake/caches/O3.cmake
```
instead of Debug.cmake

4. Build the benchmarks:
```
% make
Scanning dependencies of target timeit-target
[  0%] Building C object tools/CMakeFiles/timeit-target.dir/timeit.c.o
[  0%] Linking C executable timeit-target
...
```
5. Run the tets with lit:
```
% <path to llvm build>/bin/llvm-lit -v -j 1 -o results.json .
```

## Deprecated approach using LNT to run tests

We do not recommend running tests using LNT because LNT is not well-maintained.
It use very old packages that use features deprecated and removed from the core 
Python frameworks as of Python 3.10.

### On Linux
1. Setup LNT
Note: These steps have been adopted from the
[LNT Quickstart Guide](http://llvm.org/docs/lnt/quickstart.html).
These instructions are for Ubuntu 20.
```
sudo apt install bison flex tclsh
sudo apt install virtualenv
sudo virtualenv ~/mysandbox
git clone https://github.com/llvm/llvm-lnt.git  ~/lnt
sudo ~/mysandbox/bin/python ~/lnt/setup.py develop
```

2. Invoke LNT tests

Prerequisite: Make sure you have checked out and
[built](https://github.com/checkedc/checkedc-llvm-project/blob/main/clang/docs/checkedc/Setup-and-Build.md)
the Checked C compiler.
```
git clone https://github.com/checkedc/workfilow.git <AUTOMATION_DIR>
export SRC_DIR=</path/to/llvm/src>
export BUILD_DIR=</path/to/llvm/build>
export CLANG_REPO=https://github.com/checkedc/checkedc-llvm-project.git
export CHECKEDC_REPO=https://github.com/checkedc/checkedc.git
<AUTOMATION_DIR>/UNIX/run-lnt-local.sh
```

Optional flags:
```
TEST_TARGET="X86_64;ARM"
LNT_BIN=</path/to/lnt> // By default, lnt is picked up from ~/mysandbox/bin/lnt.
```

The test results are generated at:
```
<BUILD_DIR>/LNT-Results-Release-Linux/<TEST_TARGET>/test-<TIME_STAMP>/test.log
```

### On MacOS

This has been tested on MacOS Ventura 13.4.1 on Apple Silicon.

1. Setup LNT
- Install Python 3.9 or earlier.
- Run
```
   python3.9 venv ~/mysandbox
   git clone https://github.com/llvm/llvm-lnt.git  ~/lnt
   ~/mysandbox/bin/python ~/lnt/setup.py develop
```
 It will fail to install.  Change director to your sandbox, install the
 following packages, and re-run the LNTinstall script
```
cd ~/mysandbox
bin/pip install pyyaml==5.1.2
bin/pip install SQLAlchemy==1.2.19
bin/pip install MarkupSafe==0.23
bin/python ~lnt/setup.py develope
```
2. Invoke LNT tests
Follow the directions in step 2 for Linux.

### On Windows

The LNT tests can also be run on Windows 10 using
the [Windows Subsystem for Linux](https://blogs.msdn.microsoft.com/wsl/2016/04/22/windows-subsystem-for-linux-overview/).
See the directions [here](docs/Benchmarking-on-Windows.md).



## Contributing

We would be happy for people to convert existing benchmarks to use Checked C.
For code contributions, we follow the standard
[Github workflow](https://guides.github.com/introduction/flow/).  See 
[Contributing to Checked C](https://github.com/checkekdc/checkedc/blob/main/CONTRIBUTING.md) for more detail.
You will need to sign a contributor license agreement before contributing a
converted benchmark.

For more information on contributing on the Checked C project, see 
[Contributing to Checked C](https://github.com/checkedc/checkedc/blob/main/CONTRIBUTING.md).

## Code of conduct

This project has adopted a
[Code of Conduct](https://github.com/checkedc/checkedc/blob/main/CODE_OF_CONDUCT.md).

