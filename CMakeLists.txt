cmake_minimum_required(VERSION 2.8.3)
project(rviz_animated_view_controller)

find_package(cmake_modules REQUIRED)
find_package(Eigen REQUIRED)
find_package(Boost REQUIRED system filesystem)

# Ogre
pkg_check_modules(OGRE OGRE)

find_package(OpenGL REQUIRED)

find_package(catkin REQUIRED COMPONENTS
 rviz
 pluginlib
 std_msgs
 view_controller_msgs
)

if(DEFINED rviz_QT_VERSION)
if(rviz_QT_VERSION VERSION_LESS "5")
  message(STATUS "Using Qt4 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt4 ${rviz_QT_VERSION} EXACT REQUIRED QtCore QtGui)
  ## pull in all required include dirs, define QT_LIBRARIES, etc.
  include(${QT_USE_FILE})

  
  qt4_wrap_cpp(MOC_FILES
    include/${PROJECT_NAME}/rviz_animated_view_controller.h
  )
else()
  message(STATUS "Using Qt5 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt5 ${rviz_QT_VERSION} EXACT REQUIRED Core Widgets)
  ## make target_link_libraries(${QT_LIBRARIES}) pull in all required dependencies
  set(QT_LIBRARIES Qt5::Widgets)

  qt5_wrap_cpp(MOC_FILES
    include/${PROJECT_NAME}/rviz_animated_view_controller.h
  )
endif()
else()
  message(FATAL_ERROR "Could not detect Rviz Qt version")
endif()

add_definitions(-DQT_NO_KEYWORDS)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS std_msgs view_controller_msgs
  )

include_directories(include
                    ${catkin_INCLUDE_DIRS}
                    ${Boost_INCLUDE_DIRS}
                    ${QT_INCLUDE_DIR}
                    ${OGRE_INCLUDE_DIRS}
                    ${OPENGL_INCLUDE_DIR}
                    ${CMAKE_CURRENT_BINARY_DIR})

add_library(${PROJECT_NAME}
  src/rviz_animated_view_controller.cpp
  ${MOC_FILES})
target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${OGRE_LIBRARIES}
                                      ${catkin_LIBRARIES} ${Boost_LIBRARIES})

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION} PATTERN ".svn" EXCLUDE)

install(FILES plugin_description.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(TARGETS ${PROJECT_NAME}
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )

