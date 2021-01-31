Compiling with Visual Studio 2017 on Windows 10 x64

Downloads
- Download and install Visual Studio community edition - https://visualstudio.microsoft.com/vs/older-downloads/
- (optional) download and install TortoiseGIT - https://tortoisegit.org/download/
- Download and install boost 1_66 14.1 x64 - https://sourceforge.net/projects/boost/files/boost-binaries/1.66.0/
- Download and unzip swigwin-4.0.2 - http://www.swig.org/download.html
- checkout repo using tortoiseGit, or download as zip -  https://github.com/gnuradio/gr-tutorial to a folder location D:\gnuradio\gr-tutorial

Assumptions:
- GNURadio is installed to D:\Program Files\GNURadio-3.7
- Boost is installed to D:\local\boost_1_66_0
- GIT repo cloned to D:\gnuradio\vs\gr-rtty
- Swigwin unzipped to D:\gnuradio\vs\swigwin-4.0.2

Visual Studio solution/project setup Proj1
- open visual studio, 'new project from existing code', C++, D:\gnuradio\vs\gr-rtty, gr-rtty, DLL project
- remove all files from 'header files' that derive from the D:\gnuradio\vs\gr-rtty\include folder
- remove all qa and test files from Source files and Header files
- set profile to Release x64
- edit project properties under C/C++, General, 'Additional Include Directories' and add following folders:
 - D:\local\boost_1_66_0;
 - D:\Program Files\GNURadio-3.7\include;
 - D:\gnuradio\vs\gr-rtty\include;
- under C/C++, Preprocessor, set  'Preprocessor Definitions' to gnuradio_rtty_EXPORTS (note that this will depend on what is in the api.h 'ifdef' macro 
- under Linker, General, 'Additional Library Directories' add following folders:
 - D:\Program Files\GNURadio-3.7\lib;
 - D:\local\boost_1_66_0\lib64-msvc-14.1;
- under Linker, Input, Additional Dependencies add following libraries (may need to adapt depending on OOT module's needs)
 - gnuradio-runtime.lib;
 - gnuradio-pmt.lib;
- Under General, Project Defaults set 'Configuration Type' to '.dll' and Windows SDK version to '10.xxx'
- Rebuild solution - ensure builds ok
- edit D:\gnuradio\vs\gr-rtty\swig\rtty_swig.i and comment out '%include "rtty_swig_doc.i"' line using //
- open command line and run swig against D:\gnuradio\vs\gr-rtty\swig\rtty_swig.i (note command argument -module rtty_swig will depend on OOT module compiling)

Swig generation
..\..\swigwin-4.0.2\swig.exe -module rtty_swig -c++ -python -I"D:\Program Files\GNURadio-3.7\include\gnuradio\swig" -I"D:\gnuradio\vs\gr-rtty\include" -I"D:\Program Files\GNURadio-3.7\include" -I"D:\local\boost_1_66_0\boost" rtty_swig.i

Visual Studio project setup Proj2
- In visual studio add new project to current solution, DLL, tutorial_swig 
- add in rtty_swig_wrap.cxx from D:\gnuradio\vs\gr-rtty\swig folder and remove all other auto generated files from header and source files
- ensure C/C++, Precompiled Headers is set to 'Not Using Precompiled Headers'
- set profile to Release x64
- edit project properties under C/C++, General, 'Additional Include Directories' and add following folders:
 - D:\Program Files\GNURadio-3.7\include\gnuradio\swig;
 - D:\Program Files\GNURadio-3.7\gr-python27\Include;
 - D:\local\boost_1_66_0;
 - D:\Program Files\GNURadio-3.7\include;
 - D:\gnuradio\vs\gr-rtty\include;
- under C/C++, Preprocessor, set  'Preprocessor Definitions' to gnuradio_rtty_EXPORTS (note that this will depend on what is in the api.h 'ifdef' macro) 
- under Linker, General, 'Additional Library Directories' add following folders:
 - D:\Program Files\GNURadio-3.7\gr-python27\Libs;
 - D:\Program Files\GNURadio-3.7\lib;
 - D:\local\boost_1_66_0\lib64-msvc-14.1;
- under Linker, Input, Additional Dependencies add following libraries (may need to adapt depending on OOT module's needs)
 - gnuradio-runtime.lib;
 - gnuradio-pmt.lib;
- Under General, Project Defaults set 'Configuration Type' to '.dll' and Windows SDK version to '10.xxx'
- Add a reference to the first project
- Rebuild solution and both projects should compile

Deployment
copy D:\gnuradio\vs\gr-rtty\x64\Release\gr-rtty.dll to D:\Program Files\GNURadio-3.7\bin
create folder D:\Program Files\GNURadio-3.7\lib\site-packages\rtty
copy D:\gnuradio\vs\gr-rtty\python\__init__.py to D:\Program Files\GNURadio-3.7\lib\site-packages\rtty\
copy D:\gnuradio\vs\gr-rtty\swig\rtty_swig.py D:\Program Files\GNURadio-3.7\lib\site-packages\rtty
copy and rename D:\gnuradio\vs\gr-rtty\x64\Release\rtty_swig.dll D:\Program Files\GNURadio-3.7\lib\site-packages\rtty\_rtty_swig.pyd
copy D:\gnuradio\vs\gr-rtty\grc\*.xml D:\Program Files\GNURadio-3.7\share\gnuradio\grc\blocks