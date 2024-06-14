# Qt and PySide build instructions

## macOS

### Qt
Instructions here: https://doc.qt.io/qt-6/macos-building.html

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

python setup.py build --qtpaths=/Users/gregwilding/Documents/Code/qt/bin/qtpaths --parallel=8

Note - The first time you run this, macOS will prevent libclang.dylib from being dynamically linked because it's not signed properly.  To bypass the security check, go to Settings -> Privacy & Security -> Security (scroll down).  The library will be listed here with an "allow anyway" button.  Press it and redo the build.

Note - it seems like some tests were failing with shitboken6, so i disabled by removing --build-tests from the setup.py command line.

install pyside locally with:

python setup.py install --qtpaths=/Users/gregwilding/Documents/Code/qt/bin/qtpaths --parallel=8


Finally, test that pyside + qt webengine works with this test: 

python examples/webenginequick/nanobrowser/quicknanobrowser.py


