# how to use qtcreator to build ROS project?
* install ROS and QT5.x

* download the qtcreator and install, change .conf file and so on. 
  sites: https://ros-qtc-plugin.readthedocs.io/en/latest/_source/How-to-Install-Users.html
  some coding regulization and debug settings are in this doc.

* setup details follow instructions : https://www.ncnynl.com/archives/201701/1277.html

* create a ros workspace and rosnode instructions : https://blog.csdn.net/qq_17032807/article/details/80540290
  take care of the cmakelists.txt by adding addexcutive and linklibraries.
'''
 add_executable(${PROJECT_NAME}_node src/qt_ros_pcl_node.cpp)
 target_link_libraries(${PROJECT_NAME}_node
   ${catkin_LIBRARIES}
 )
'''

whole example:
'''
cmake_minimum_required(VERSION 2.8.3)
project(qt_ros_pcl)

 add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES qt_ros_pcl
#  CATKIN_DEPENDS roscpp
#  DEPENDS system_lib
)

include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)

 add_executable(${PROJECT_NAME}_node src/basic.cpp)


 target_link_libraries(${PROJECT_NAME}_node
   ${catkin_LIBRARIES}
 )
'''

* make build

* modify run mode to ros command.


# import a exist ros workspace 
 add new project --> same name and directory of existing project with catkinmake
 --> run this in ws: <unlock the file.>
'''
sed -i '' CMakeLists.txt
'''
 
 --> add catkin_ws.workspace<for qtcreator only>
'''
<?xml version="1.0"?>
<Workspace>
    <Distribution name="kinetic"/>
    <DefaultBuildSystem value="0"/>
    <WatchDirectories>
        <Directory>src</Directory>

       <Directory>你想加入的文件夹名字</Directoory>
    </WatchDirectories>
    <Directory>/opt/ros/kinetic/include</Directory>
</Workspace>
'''



# build GUI instru
* add a GUI qt source instructionss : https://blog.csdn.net/qq_16481211/article/details/90708055
based on above;
create /gui folder in qt_ros_pcl/src 

add /gui a new qtdesigner ui class with mainwindow template.

add a new rosnode gui_node.cpp as the main() fun of ui.

modify the gui_node.cpp with code:
'''
#include <gui_node.h>
#include "mainwindow.h"
#include <QApplication>

int main(int argc, char *argv[])
{

    QApplication a(argc, argv);
    MainWindow w;
    w.show();

    return a.exec();
}
'''

modify cmakelists.txt for qt cmake config:
'''
cmake_minimum_required(VERSION 2.8.3)
project(qt_ros_pcl)

 add_compile_options(-std=c++11)


find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
)
# 找到qt5中的widgets组件的关键步骤，使程序编译时能够找到qt5-widgets相关头文件和库函数。
find_package(Qt5  REQUIRED COMPONENTS  Widgets  )

# 指明需要参与编译的cpp及ui文件，分别给它们起个变量名叫SOURCES和FORMS，后续会用到。
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(SOURCES
  gui/mainwindow.cpp
  gui/gui_node.cpp
)

set(FORMS
  gui/mainwindow.ui
)

catkin_package()

include_directories( include  ${catkin_INCLUDE_DIRS} )
# 这是生成可执行文件，此时用到了SOURCES和FORMS所指明的cpp和ui文件。
 add_executable(gui_node ${SOURCES} ${FORMS} )
 add_executable(${PROJECT_NAME}_node  src/basic.cpp)
#在链接环节，告诉目标文件需要qt5的widgets组件作为动态链接库。
 target_link_libraries(${PROJECT_NAME}_node ${catkin_LIBRARIES} )
 target_link_libraries(gui_node  Qt5::Widgets )

'''

build make

modify run command as executive instru.
