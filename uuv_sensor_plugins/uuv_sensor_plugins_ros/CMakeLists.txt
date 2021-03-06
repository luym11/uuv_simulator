cmake_minimum_required(VERSION 2.8.3)
project(uuv_sensor_plugins_ros)

set(CMAKE_BUILD_TYPE RelWithDebInfo)

find_package(catkin REQUIRED uuv_gazebo_plugins sensor_msgs uuv_sensor_plugins
                             uuv_sensor_plugins_ros_msgs)

find_package(Boost REQUIRED COMPONENTS system)                             
#find_package(Eigen3 REQUIRED)
find_package(PkgConfig)
pkg_search_module(Eigen3 REQUIRED eigen3)

find_package(Boost REQUIRED COMPONENTS system)
# find_package(Eigen3 REQUIRED)

find_package(gazebo REQUIRED)

set(uuv_sensor_plugins_list "")

catkin_package(
  INCLUDE_DIRS include
    ${EIGEN3_INCLUDE_DIR}
  LIBRARIES uuv_gazebo_ros_camera
    uuv_gazebo_ros_dvl
    uuv_gazebo_ros_imu
    uuv_gazebo_ros_magnetometer
    uuv_gazebo_ros_rpt
    uuv_gazebo_ros_subseapressure
  CATKIN_DEPENDS uuv_gazebo_plugins
    sensor_msgs
    uuv_sensor_plugins
    uuv_sensor_plugins_ros_msgs
  DEPENDS system_lib
)

include_directories(
  ${PROJECT_SOURCE_DIR}/include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIR}
  ${EIGEN3_INCLUDE_DIR}
  ${GAZEBO_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS}
)

link_directories(
  ${GAZEBO_LIBRARY_DIRS}
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GAZEBO_CXX_FLAGS}")

add_library(uuv_gazebo_ros_camera src/UnderwaterCameraRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_camera ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_camera uuv_gazebo_camera ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_camera)

add_library(uuv_gazebo_ros_dvl src/DvlRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_dvl ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_dvl uuv_gazebo_dvl ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_dvl)

add_library(uuv_gazebo_ros_imu src/ImuRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_imu ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_imu uuv_gazebo_imu ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_imu)

add_library(uuv_gazebo_ros_magnetometer src/MagnetometerRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_magnetometer ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_magnetometer uuv_gazebo_magnetometer ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_magnetometer)

add_library(uuv_gazebo_ros_rpt src/RptRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_rpt ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_rpt
  uuv_gazebo_rpt ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_rpt)

add_library(uuv_gazebo_ros_gps src/GPSRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_gps ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES} ${Boost_LIBRARIES})
add_dependencies(uuv_gazebo_ros_gps
    ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_gps)

add_library(uuv_gazebo_ros_subseapressure src/SubseapressureRosPlugin.cc)
target_link_libraries(uuv_gazebo_ros_subseapressure ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(uuv_gazebo_ros_subseapressure uuv_gazebo_subseapressure ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_subseapressure)

# Gazebo ROS plugin adaptations with switch service function

add_library(uuv_gazebo_ros_switchable_gpu_ray_sensor 
  src/SwitchableROSGpuRaySensor.cc
  src/SwitchableROSSensorPlugin.cc)
target_link_libraries(uuv_gazebo_ros_switchable_gpu_ray_sensor 
  ${catkin_LIBRARIES} 
  ${GAZEBO_LIBRARIES} 
  ${Boost_LIBRARIES})
add_dependencies(uuv_gazebo_ros_switchable_gpu_ray_sensor
  ${catkin_EXPORTED_TARGETS})
list(APPEND uuv_sensor_plugins_list uuv_gazebo_ros_switchable_gpu_ray_sensor)

# install plug-ins
install(TARGETS ${uuv_sensor_plugins_list}
    DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hh"
  PATTERN "*~" EXCLUDE
)

# Install urdf files
install(DIRECTORY urdf
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
        PATTERN "*~" EXCLUDE)
