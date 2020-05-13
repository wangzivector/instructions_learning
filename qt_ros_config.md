## Setup Qt 
* download Qt and Qtreator (at least qt5 download the .run file from address:  http://mirror.bit.edu.cn/qtproject/archive/qt/) 
* make the .run file executive, install Qt and qtcreator in directory: /opt/Qt5.xx (needed or not)
* sudo gedit /usr/share/applications/DigiaQt-qtcreator-community.desktop    modify:( add: bash -i -c)
  E.g : Exec=/opt/Qt5.9.0/Tools/QtCreator/bin/qtcreator  --->   Exec=bash -i -c /opt/Qt5.9.0/Tools/QtCreator/bin/qtcreator

## catkin_ws modification
< if you just need to add a package as the Qt project>
(check the file ~/.bashrc has contained: source ~/ws/devel/setup.sh)
* add the folllowing content after <project(chat)> in catkin_ws/src/chat/CMakeLists.txt:
```
 file(GLOB_RECURSE EXTRA_FILES */*)
 add_custom_target(${PROJECT_NAME}_OTHER_FILES ALL WORKING_DIRECTORY ${PROJECT_SOURCE_DIR} SOURCES ${EXTRA_FILES})
```
* catkin_make the ws

< if you just need to add a workspace as the Qt project>
(check the two lines have been added in src/CMakeLists.txt in new version of ros)
* but you need to link CMakeLists.txt by:
```
 cd ~/catkin_ws/src
 sed -i '' CMakeLists.txt (In order to avoid driving QtCreator mad,we 
    must replace the symlink’ed CMakeLists.txt in catkin_ws/src with a physical copy of the actual stuff；)
```
* catkin_make the ws.


## Setting in Qt creator
* open qtcreator 
* open project, choosing catkin_ws/src/chat/CMakeLists.txt(if want to open the whole workspace,then choose catkin_ws/src/CMakeLists.txt)

* choose complier build tool: Desktop GCC 64bit(right side) --> build -->  Debug mode -
  -> choose the build derectory :/home/wang/catkin_spaces/src/chat/ (or /home/wang/catkin_spaces)
  <this step is to select the makecache file of build options that need modify below.>

* add string 
 1.setting:CMAKE_BUILD_TYPE   value: Debug (add string)
 2.setting:CMAKE_PREFIX_PATH  value: /opt/ros/kinetic (after gcc_64/tool/dir with ; )
* apply configration change (then choose apply to project)


## note 
if you rebuild the project after modifying the code, the output executive files are in subtree of build directory. 
when you rosrun package rosnode, it may go to the wrong position to execute.(which you built before before qt.) you should check the output dir. 

## Create GUI
above just modify for a IDE, you need to create a GUI with ROS, instructions:
* sudo apt-get install ros-kinetic-qt-create
* sudo apt-get install ros-kinetic-qt-build
* cd catkin_spaces/src
* catkin_create_qt_pkg ros_qt_pack
* cd ..
* catkin_make
