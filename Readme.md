# Qt and PySide build instructions

## macOS

### Qt
Need macOS Sonoma 14.x to get Xcode 15.4 and build tools.  In particular, older versions of the linker that comes with Xcode will fail with the error "ld: unknown option: -no_warn_duplicate_libraries"d".


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


### PySide

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

python setup.py build --qtpaths=/Users/gregwilding/Documents/Code/qt/bin/qtpaths6 --build-tests --parallel=8

