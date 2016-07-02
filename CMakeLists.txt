cmake_minimum_required(VERSION 2.8.3)
project(rtimulib_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/rtimulib)

ExternalProject_Add(rtimulib_src
  GIT_REPOSITORY git@github.com:RTIMULib/RTIMULib.git
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND ""
  CMAKE_ARGS -DCMAKE_BUILD_TYPE:STRING=Release
  BUILD_COMMAND
    cd ../rtimulib_src/RTIMULib &&
    ${CMAKE_COMMAND} -E copy_directory . ${CATKIN_DEVEL_PREFIX}/include/rtimulib &&
    mkdir build && 
    cd build && 
    cmake .. && 
    make -j4
  INSTALL_COMMAND cd ../rtimulib_src/RTIMULib/build && 
    cp libRTIMULib.so.7.2.1 ${CATKIN_DEVEL_PREFIX}/lib &&
    cp libRTIMULib.so.7 ${CATKIN_DEVEL_PREFIX}/lib &&
    cp libRTIMULib.so ${CATKIN_DEVEL_PREFIX}/lib
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} rtimulib_src)

cs_install()

cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/rtimulib
  CFG_EXTRAS rtimulib-extras.cmake)
