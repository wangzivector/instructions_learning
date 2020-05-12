* 点云的显示主要有以下的方式：QVTKwidget(PLC_qt_visualizer_tutorial base)    librviz(testRVIZ)	openGL
* 如果已经完成对ROS-Qt的搭建，可以使用rviz库直接显示，过程可以参靠 librviz.md。
* 如果想要搭建PCL-Qt，可以使用opengl或者QVTKwidget进行可视化。，但是opengl处理逻辑较为高级，而使用QVTKwidget需要注意库的相容。（库不相容可能执行出错）
* 如果想使用ROS和QT和PCL，而想要可视化的话，除非编译的 VTK 的VTK库符合自己的Qt版本，否则无法在QT4 中使用 不一样的库。
* ROS_qt 的catkin_create_qt_pack 插件 是基于qt4的配置。可能无法使用基于qt5的库。如果需要使用vtk相关，建议使用QT5库 + PCL库（去除内含的VTK）+ VTK库
* 上述所说的库，满足条件是，安装较新版本的QT库后安装QT_ROS_creator:qt57creator-ros 来进行开发。生成的rosqt模板将会满足上述要求。
* 使用以下的步骤能够完成基于 QT 的点云显示工作。
* https://pcl-tutorials.readthedocs.io/en/master/qt_colorize_cloud.html#user-interface-ui

* 或者使用以下方式构建一个简单的自定义类：
'''

##myclass.h

#include "QVTKWidget.h"


class vtkRenderer;
class vtkRenderWindowInteractor;


class myVTKWidget :
public QVTKWidget
{
Q_OBJECT
public:
explicit myVTKWidget(QWidget*parent = 0);

vtkRenderer *ren1;
vtkRenderWindowInteractor *iren;
signals:
public slots:
};


##myclass.cpp

#include "myclass.h"
#include "myVTKWidget.h"

#include "vtkRenderer.h"
#include "vtkRenderWindow.h"
#include "vtkRenderWindowInteractor.h"


myVTKWidget::myVTKWidget(QWidget *parent) :
QVTKWidget(parent)
{
//
this->ren1 = vtkRenderer::New();
this->GetRenderWindow()->AddRenderer(ren1);
this->iren =this->GetInteractor();
}


##main_window.cpp

#include "myclass.h"
ui.setupUi(this);
this->ui.verticalLayout->addWidget(new myVTKWidget(this)); // note that you need to add a verticalLayout obj in ui.

'''

## CMakeLists.txt 添加以下库依赖
'''
# 找到qt5中的widgets组件的关键步骤，使程序编译时能够找到qt5-widgets相关头文件和库函数。
find_package(Qt5  REQUIRED COMPONENTS  Widgets)

find_package(VTK REQUIRED)
find_package(PCL 1.7.1 REQUIRED)

# Fix a compilation bug under ubuntu 16.04 (Xenial)
list(REMOVE_ITEM PCL_LIBRARIES "vtkproj4")
include_directories(${PCL_INCLUDE_DIRS})
add_definitions(${PCL_DEFINITIONS})

set(SOURCES
  gui/mainwindow.cpp
  gui/gui_node.cpp
  gui/qvtk.cpp
)


set(FORMS
  gui/mainwindow.ui
)

catkin_package()

include_directories( include  ${catkin_INCLUDE_DIRS} )
# 这是生成可执行文件，此时用到了SOURCES和FORMS所指明的cpp和ui文件。
 add_executable(gui_node ${SOURCES} ${FORMS} )
 target_link_libraries(gui_node  Qt5::Widgets ${catkin_LIBRARIES} ${PCL_LIBRARIES})

'''

* 按照最后的指令逻辑，可能有部分vtk的link库文件需要到pcl的PCL_LIBRARIES库 的3rdparty去找，否则会报vtk相关的错误。

参考链接：
index: https://tieba.baidu.com/p/4101387026?red_tag=0095648010
