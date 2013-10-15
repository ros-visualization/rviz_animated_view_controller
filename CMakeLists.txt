cmake_minimum_required(VERSION 2.8.3)
project(rviz_animated_view_controller)

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/" ${CMAKE_MODULE_PATH})

if (NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

find_package(Eigen REQUIRED)
find_package(Boost REQUIRED system filesystem)

# Ogre
pkg_check_modules(OGRE OGRE)
link_directories(${OGRE_LIBRARY_DIRS})

# Qt Stuff
find_package(Qt4 REQUIRED)
include(${QT_USE_FILE})
add_definitions(-DQT_NO_KEYWORDS)

find_package(OpenGL REQUIRED)

find_package(catkin REQUIRED COMPONENTS 
 rviz
 pluginlib
 view_controller_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS 
  )

qt4_wrap_cpp(MOC_FILES
  include/${PROJECT_NAME}/rviz_animated_view_controller.h
)

include_directories(include)
include_directories(${catkin_INCLUDE_DIRS})
include_directories(SYSTEM
                    ${Boost_INCLUDE_DIRS}
                    ${QT_INCLUDE_DIR}
                    ${OGRE_INCLUDE_DIRS}
		    ${OPENGL_INCLUDE_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR})

link_directories(${Boost_LIBRARY_DIRS})
link_directories(${catkin_LIBRARY_DIRS})



add_library(${PROJECT_NAME}
  src/rviz_animated_view_controller.cpp
  ${MOC_FILES})
target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${OGRE_LIBRARIES} ${catkin_LIBRARIES} ${Boost_LIBRARIES})

install(FILES
  plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(TARGETS ${PROJECT_NAME}
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )

