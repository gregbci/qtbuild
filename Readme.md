# Qt and PyQt build instructions

# macOS

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
