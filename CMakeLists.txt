cmake_minimum_required(VERSION 3.5.0)

project(sesto_easy_traffic_light)

if (NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(yaml-cpp REQUIRED)


include(GNUInstallDirs)

set(dep_pkgs
  rclcpp
  rmf_utils
  rmf_dispenser_msgs
  rmf_ingestor_msgs
  rmf_door_msgs
  rmf_fleet_msgs
  rmf_lift_msgs
  rmf_task_msgs
  rmf_traffic
  rmf_traffic_ros2
  rmf_battery
  rmf_task
  std_msgs
  rmf_fleet_adapter
)
foreach(pkg ${dep_pkgs})
  find_package(${pkg} REQUIRED)
endforeach()

include_directories(include ${rmf_fleet_adapter_INCLUDE_DIRS})

# -----------------------------------------------------------------------------

add_executable(sesto_etf src/sesto_etf/main.cpp)

# ament_target_dependencies(${PROJECT_NAME} rmf_fleet_adapter rmf_fleet_msgs rmf_task_msgs)
# ament_target_dependencies(${PROJECT_NAME} rclcpp)
target_link_libraries(sesto_etf
  PRIVATE
    ${rmf_fleet_adapter_LIBRARIES}
    ${rmf_task_msgs_LIBRARIES}
    ${rmf_fleet_msgs_LIBRARIES}
    ${rclcpp_LIBRARIES}
)

target_include_directories(sesto_etf
  PRIVATE
    ${rmf_fleet_adapter_INCLUDE_DIRS}
    ${rmf_task_msgs_INCLUDE_DIRS}
    ${rmf_fleet_msgs_INCLUDE_DIRS}
    ${rclcpp_INCLUDE_DIRS}
)


# -----------------------------------------------------------------------------

ament_export_dependencies(
  rmf_fleet_adapter
  rmf_task
  rmf_traffic_ros2
  rmf_fleet_msgs
  rmf_door_msgs
  rmf_lift_msgs
  rmf_ingestor_msgs
  rmf_dispenser_msgs
)

install(
  TARGETS
    sesto_etf
  EXPORT sesto_etf
  RUNTIME DESTINATION lib/sesto_etf
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

# -----------------------------------------------------------------------------

install(DIRECTORY
  launch/
  DESTINATION share/${PROJECT_NAME}
)

install(
  DIRECTORY include/
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

ament_package()
