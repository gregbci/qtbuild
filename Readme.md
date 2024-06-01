# Qt and PyQt build instructions

# macOS

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

/tmp/qt-everywhere-src-6.7.1/configure -webengine-proprietary-codecs -prefix ../qt

build it

cmake --build . --parallel

install it (to -prefix)

cmake --install .
