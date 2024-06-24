# Qt and PySide build instructions

## macOS

### Qt
Instructions here: https://doc.qt.io/qt-6/macos-building.html

Need macOS Sonoma 14.x to get Xcode 15.4 and build tools.  In particular, older versions of the linker that comes with Xcode will fail with the error "ld: unknown option: -no_warn_duplicate_libraries"d".

To distribute the PySide6 package, the build needs to be perfomed with a paid Apple Developer ID.  Using a free one will produce a PySide6 package that will work on your own computers, but will be blocked by gatekeeper elsehwere.  You _can_ add exclusions, but Qt is distributed as many dynamic libs, so it's very annoying to try and add exceptions for each lib.

download the source code, get the .tar.xz for the right line endings

cd /tmp
tar -xf ~/Downloads/qt-everywhere-src-6.7.1.tar.xz 

set up the build location

git clone git@github.com:gregbci/qtbuild.git
cd qtbuild

install build dependencies

conda env create -f ./environment.yml
conda activate qtbuild

configure build
use prefix to set qt install location someplace pyqt build can access it

/tmp/qt-everywhere-src-6.7.1/configure -webengine-proprietary-codecs -prefix ~/Documents/Code/qt

build it

cmake --build . --parallel

install it (to -prefix)

cmake --install .


### PySide6

Instructions: https://doc.qt.io/qtforpython-6/gettingstarted/macOS.html

Repo instructions are better: https://github.com/qtproject/pyside-pyside-setup


Download libclang provided by Qt: https://download.qt.io/development_releases/prebuilt/libclang/.
unzip someplace 

export LLVM_INSTALL_DIR=~/Downloads/libclang

Using same conda environment qtbuild:

git clone https://code.qt.io/pyside/pyside-setup

cd pyside-setup

git checkout 6.7.1

pip install -r requirements.txt

build pyside using Qt we built:

python setup.py build --qtpaths=/Users/gregwilding/Documents/Code/qt/bin/qtpaths --parallel=8

Note - The first time you run this, macOS will prevent libclang.dylib from being dynamically linked because it's not signed properly.  To bypass the security check, go to Settings -> Privacy & Security -> Security (scroll down).  The library will be listed here with an "allow anyway" button.  Press it and redo the build.

Note - it seems like some tests were failing with shitboken6, so i disabled by removing --build-tests from the setup.py command line.

install pyside locally with:

python setup.py install --qtpaths=/Users/gregwilding/Documents/Code/qt/bin/qtpaths --parallel=8


Finally, test that pyside + qt webengine works with this test: 

python examples/webenginequick/nanobrowser/quicknanobrowser.py


## Windows

### Qt
Start with clean build environment.  A development VM from Microsoft is one way to get this: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/ (I also didn't have a Windows machine available, so ran this on an x86 macbook).  This build was done using MSVC 2019, as there's a bug in the MSVC 2022 compiler that breaks the Qt build: https://github.com/microsoft/vcpkg/issues/39053.  A resolution for this is pending, which will simplify the steps below.

If using a fresh Windows install, be sure to exclude your build drive from MS Defender (duh-fender).  Or switch it off completely.  It's also helpful to disable updates as Windows loves to start updating right before you start this lengthy build.

Following these instructions: https://doc.qt.io/qt-6/windows-building.html

I didn't bother with virtual environments (like the macOS build) for this build as I used a VM.

Install Node.js, let it also install build tools and Chocolatey.  This will provide Python 3.12 and MSVC 2019 Build Tools.

Open up Visual Studio Installer and ensure that Build Tools for Visual Studio 2019 has the "MFC/ATL support” sub-components installed.  See Chromium build instructions for more info: https://chromium.googlesource.com/chromium/src/+/HEAD/docs/windows_build_instructions.md#setting-up-windows

Install html5lib

pip install html5lib

Install Gperf, Bison and Flex (needed by QWebEngine).  Chocolately provides these:

choco install gperf winflexbison

Download the .zip version to get Windows line endings: https://download.qt.io/official_releases/qt/6.7/6.7.1/single/

Create a build folder (or drive).  This build will required long paths.  I was able to get away with putting the build in C:\Code\qtbuild, but it's probably better to enable long paths.  Uzip the qt source into your build folder.

cd C:\Code
tar -xf \Users\User\downloads\qt-everywhere-src-6.7.1.zip

Make a qt build folder

cd C:\Code
mkdir qtbuild

Okee, it's important to get all the right stuff in the cmd window's path before trying to build.  Qt suggests using the VS Native Tools Command Prompt 2019. Ensure that cmake, python and node commands are recognized.

Configure the qt build:

cd C:\Code\qtbuild
..\qt-everywhere-src-6.7.1\configure -webengine-proprietary-codecs -prefix c:\Code\qt

Start the build

cmake --build . --parallel

install it (to -prefix location)

cmake --install .


### PySide6

Following these instructions: https://doc.qt.io/qtforpython-6/gettingstarted/windows.html#getting-started-on-windows

Install git and clone the pyside-setup repo

cd c:\Code
git clone https://code.qt.io/pyside/pyside-setup

cd pyside-setup

git checkout 6.7.1

pip install -r requirements.txt

Set up the libclang dependencies.  Download the precompiled version for x64 msvc 2019.  Instructions here: https://doc.qt.io/qtforpython-6/gettingstarted/windows.html#setting-up-clang.  Be sure to set both the path and the LLVM_INSTALL_DIR.

set LLVM_INSTALL_DIR=c:\Code\libclang
set PATH=C:\Code\libclang\bin;%PATH%

build pyside using Qt we built:

python setup.py build --qtpaths=c:\Code\qt\bin\qtpaths.exe --parallel=8

This build is pretty quick.  The resulting packages will be in c:\Code\pyside-setup\build\qfp-py3.11-qt6.7.1-64bit-release\package_for_wheels.  The PySide6 and shiboken6 are what you need.

