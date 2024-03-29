cmake_minimum_required(VERSION 2.8.3)
project(rviz_plugins)

add_compile_options(-std=c++11 -Wall)

find_package(catkin REQUIRED COMPONENTS rviz rospy sensor_msgs std_msgs)
catkin_package(INCLUDE_DIRS include)
include_directories(${catkin_INCLUDE_DIRS} include sensor_msgs)
link_directories(${catkin_LIBRARY_DIRS})

# This setting causes Qt's "MOC" generation to happen automatically.
set(CMAKE_AUTOMOC ON)

# This plugin includes Qt widgets, so we must include Qt. We'll use the version
# that rviz used so they are compatible.
if(rviz_QT_VERSION VERSION_LESS "5")
  message(STATUS "Using Qt4 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt4 ${rviz_QT_VERSION} EXACT REQUIRED QtCore QtGui)
  # pull in all required include dirs, define QT_LIBRARIES, etc.
  include(${QT_USE_FILE})
else()
  message(STATUS "Using Qt5 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt5 ${rviz_QT_VERSION} REQUIRED Core Widgets)
  # make target_link_libraries(${QT_LIBRARIES}) pull in all required
  # dependencies
  set(QT_LIBRARIES Qt5::Widgets)
endif()

add_definitions(-DQT_NO_KEYWORDS)

set(SRC_FILES
    src/point_cloud_transformers.cpp
)
set(HEADER_FILES
    include/rviz_plugins/point_cloud_transformers.h
)

add_library(${PROJECT_NAME} ${SRC_FILES} ${HEADER_FILES})

target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${catkin_LIBRARIES})

# Install rules
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(FILES plugin_description.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

catkin_install_python(PROGRAMS
        DESTINATION
        ${CATKIN_PACKAGE_BIN_DESTINATION})
