cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(ardupilot_gazebo)

find_package(
  catkin REQUIRED
  COMPONENTS roscpp
             rospy
             std_msgs
             dynamic_reconfigure
             sensor_msgs
             mav_msgs)

# ##############################################################################
# Find Dependencies ##
# ##############################################################################

find_package(gazebo REQUIRED)
set(CMAKE_CXX_FLAGS "-std=c++17")

if("${GAZEBO_VERSION}" VERSION_LESS "7.0")
  message(
    FATAL_ERROR "You need at least Gazebo 7.0. Your version: ${GAZEBO_VERSION}")
else()
  message("Gazebo version: ${GAZEBO_VERSION}")
endif()

# ##############################################################################
# Build ##
# ##############################################################################

include_directories(${PROJECT_SOURCE_DIR} include ${GAZEBO_INCLUDE_DIRS}
                    ${catkin_INCLUDE_DIRS})

link_libraries(${CMAKE_CURRENT_BINARY_DIR} ${GAZEBO_LIBRARIES}
               ${catkin_LIBRARIES})

link_directories(${GAZEBO_LIBRARY_DIRS})

set(plugins_single_header
    ArduPilotPlugin ArduCopterIRLockPlugin ArduCopterPlugin
    # GimbalSmall2dPlugin
    # WindPlugin
)

add_library(ArduCopterIRLockPlugin SHARED src/ArduCopterIRLockPlugin.cc)
target_link_libraries(ArduCopterIRLockPlugin ${GAZEBO_LIBRARIES})

add_library(ArduPilotPlugin SHARED src/ArduPilotPlugin.cc)
target_link_libraries(ArduPilotPlugin ${GAZEBO_LIBRARIES} ${catkin_LIBRARIES})

add_library(ArduCopterPlugin SHARED src/ArduCopterPlugin.cc)
target_link_libraries(ArduCopterPlugin ${GAZEBO_LIBRARIES})

add_library(GazeboGimbalPlugin src/GazeboGimbal.cpp)
target_link_libraries(GazeboGimbalPlugin ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
add_dependencies(GazeboGimbalPlugin ${catkin_EXPORTED_TARGETS})

if("${GAZEBO_VERSION}" VERSION_LESS "8.0")
  add_library(GimbalSmall2dPlugin SHARED src/GimbalSmall2dPlugin.cc)
  target_link_libraries(GimbalSmall2dPlugin ${GAZEBO_LIBRARIES})
  install(TARGETS GimbalSmall2dPlugin DESTINATION ${GAZEBO_PLUGIN_PATH})
endif()

install(TARGETS ArduCopterIRLockPlugin DESTINATION ${GAZEBO_PLUGIN_PATH})
install(TARGETS ArduPilotPlugin DESTINATION ${GAZEBO_PLUGIN_PATH})
install(TARGETS ArduCopterPlugin DESTINATION ${GAZEBO_PLUGIN_PATH})
install(TARGETS GazeboGimbalPlugin DESTINATION ${GAZEBO_PLUGIN_PATH})

