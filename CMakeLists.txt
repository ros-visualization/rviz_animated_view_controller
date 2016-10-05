cmake_minimum_required(VERSION 2.8.3)
project(rviz_animated_view_controller)

find_package(cmake_modules REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED system filesystem)
find_package(PkgConfig)

# Ogre
pkg_check_modules(OGRE OGRE)

# Qt Stuff
set(CMAKE_AUTOMOC ON)
find_package(Qt5Widgets REQUIRED)

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
  CATKIN_DEPENDS view_controller_msgs
  )

include_directories(include
                    ${catkin_INCLUDE_DIRS}
                    ${Boost_INCLUDE_DIRS}
                    ${OGRE_INCLUDE_DIRS}
                    ${OPENGL_INCLUDE_DIR}
                    ${CMAKE_CURRENT_BINARY_DIR})

add_library(${PROJECT_NAME}
    src/rviz_animated_view_controller.cpp)

target_link_libraries(${PROJECT_NAME} ${OGRE_LIBRARIES}
                                      ${catkin_LIBRARIES} ${Boost_LIBRARIES} Qt5::Widgets)

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

