cmake_minimum_required(VERSION 2.8.3)
project(ximea_ros_cam)

## gnu++11 feature
set(CMAKE_CXX_FLAGS "-std=gnu++11 -Wall -Wextra ${CMAKE_CXX_FLAGS}")

## Set ximea environment variables
set(XIMEA_INCLUDE_PATH /usr/include)
set(XIMEA_LIBRARY m3api)
find_package(dynamic_reconfigure)
## Find catkin macros and libraries
set(catkin_deps
    roscpp
    roslib
    nodelet
    sensor_msgs
    image_transport
    camera_info_manager
    std_msgs
    dynamic_reconfigure
)

find_package(catkin REQUIRED ${catkin_deps})
find_package(OpenCV REQUIRED)
find_package(Boost REQUIRED)

generate_dynamic_reconfigure_options(
  config/ximea_ros_cam.cfg
)
catkin_package(INCLUDE_DIRS   include
               LIBRARIES ${PROJECT_NAME}
               CATKIN_DEPENDS ${catkin_deps} nodelet roscpp std_msgs
               DEPENDS        Boost system_lib
               LIBRARIES      ${PROJECT_NAME}
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include
    ${XIMEA_INCLUDE_PATH}
    ${catkin_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIR}
    ${OpenCV_INCLUDE_DIRS}
)

## Declare a C++ library
add_library(${PROJECT_NAME}
            src/ximea_ros_cam.cpp
)
add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}_gencfg)
## Link the catkin libraries
target_link_libraries(${PROJECT_NAME}
    ${XIMEA_LIBRARY}
    ${catkin_LIBRARIES}
    ${Boost_LIBRARIES}
    ${OpenCV_LIBRARIES}
    yaml-cpp
    ${dynamic_reconfigure_LIBRARIES}
)

## Declare a C++ executable
add_executable(${PROJECT_NAME}_node src/ximea_ros_cam_node.cpp)
add_dependencies(${PROJECT_NAME}_node ${PROJECT_NAME}_gencfg)
## Specify libraries to link a library or executable target against
target_link_libraries(${PROJECT_NAME}_node
                      ${XIMEA_LIBRARY}
                      ${catkin_LIBRARIES}
                      ${OpenCV_LIBRARIES}
                      yaml-cpp
                      ${PROJECT_NAME}
)

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp"
)


install(DIRECTORY launch config
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(FILES
  ${PROJECT_NAME}.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

# #############
# ## Testing ##
# #############
# 
# ## Add gtest based cpp test target and link libraries
# if (CATKIN_ENABLE_TESTING)
#   file(GLOB TEST_FILES "test/*.cpp")
#   catkin_add_gtest(${PROJECT_NAME}-test ${TEST_FILES})
#   target_link_libraries(${PROJECT_NAME}-test ${catkin_LIBRARIES})
# endif(CATKIN_ENABLE_TESTING)
