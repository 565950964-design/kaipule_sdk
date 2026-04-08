cmake_minimum_required(VERSION 3.8)
project(control)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_BUILD_TYPE Release)

set(ros_packages
        rcl_interfaces
        rosidl_typesupport_interface
        rmw
        rclcpp
        rclcpp_action
        std_msgs
        sensor_msgs
        ruckig
        tf2_geometry_msgs
)

list(APPEND ros_INCLUDE_DIRS "/opt/ros/humble/include")
list(APPEND ros_INCLUDE_DIRS "/opt/ros/humble/include/tf2_geometry_msgs")

foreach (ros_package ${ros_packages})
    find_package(${ros_package} REQUIRED)
    list(APPEND ros_INCLUDE_DIRS ${${ros_package}_INCLUDE_DIRS})
    list(APPEND ros_LIBRARIES ${${ros_package}_LIBRARIES})
endforeach ()

list(REMOVE_DUPLICATES ros_LIBRARIES)
list(REMOVE_DUPLICATES ros_INCLUDE_DIRS)

#message(WARNING "tf2_geometry_msgs_INCLUDE_DIRS=${tf2_geometry_msgs_INCLUDE_DIRS}")
#message(WARNING "rclcpp_action_LIBRARIES=${rclcpp_action_LIBRARIES}")

find_package(Boost REQUIRED COMPONENTS system)
find_package(yaml-cpp REQUIRED)

list(APPEND CMAKE_PREFIX_PATH "/opt/openrobots")
find_package(pinocchio REQUIRED)
find_package(ruckig REQUIRED)
find_package(yaml-cpp REQUIRED)


set(kpl_INCLUDE_DIRS
        "/opt/kpl/include"
        "/opt/kpl/include/kpl_msgs"
        "/opt/kpl/include/sharedmemory"
)

set(kpl_LIBRARIES
        /opt/kpl/lib/libethercat_comm.so
        /opt/kpl/lib/libcommon.so
        /opt/kpl/lib/libtrac_ik_kdl.so
)


message(CMAKE_BINARY_DIR=${CMAKE_BINARY_DIR})
message(CMAKE_CURRENT_BINARY_DIR=${CMAKE_CURRENT_BINARY_DIR})
include_directories(
        ${CMAKE_CURRENT_BINARY_DIR}
        ${catkin_INCLUDE_DIRS}
        include
        /opt/kpl/include
        /usr/include/eigen3
        /usr/include/python3.8
        /opt/openrobots/include
        /usr/local/webots/include/controller/c
        /usr/local/webots/include/controller/cpp
        /usr/local/webots/lib/controller
)

#include_directories(SYSTEM ${CMAKE_BINARY_DIR}/devel/include)

add_executable(${PROJECT_NAME}_node
    src/control_node.cpp
    src/whole_body.cpp
    src/manipulation/arm_control.cpp
    src/manipulation/kinematics.cpp
    src/manipulation/end_effector_force_sensor.cpp
    src/manipulation/trajectory_planner.cpp
    src/manipulation/user_program_processing.cpp
    src/manipulation/finger_control.cpp
    src/manipulation/vision_application.cpp
    src/manipulation/key_move.cpp
    src/manipulation/robot_calib.cpp
    src/manipulation/demo.cpp
    src/manipulation/remote_action_demo.cpp
    src/manipulation/upper_body_self_test.cpp
    src/manipulation/pub_joint_status.cpp
    src/manipulation/security_protection.cpp
    src/manipulation/teleoperation_xsens.cpp
    src/manipulation/vision_calib.cpp
    src/manipulation/vr_remote.cpp
    src/common/butterworth.cpp
    src/common/mathtool.cpp
    src/common/low_pass_filter.cpp
    src/common/mean_filter.cpp
    src/common/PID_controller.cpp
    src/common/Lower_Body.cpp
    src/common/csv_io.cpp
    src/manipulation/dynamics_control.cpp
    src/locomotion/body_ctrl.cpp
    src/locomotion/capturePointCal.cpp
    src/locomotion/foot_plan.cpp
    src/locomotion/footPlanner.cpp
    src/locomotion/waistPlanner.cpp
    src/locomotion/walk_control.cpp
    src/locomotion/ZMPCal.cpp
    src/locomotion/pinocchio_interface/pinocchio_interface.cpp
    src/locomotion/JAScontroller.cpp
    )

get_filename_component(PROJECT_ROOT "${CMAKE_CURRENT_SOURCE_DIR}" DIRECTORY)
target_compile_definitions(${PROJECT_NAME}_node PRIVATE
    RECORD_DATA_DIR="${CMAKE_CURRENT_SOURCE_DIR}/record_data/"
    CSV_DATA_DIR="${CMAKE_CURRENT_SOURCE_DIR}/csv_file/"
)

target_include_directories(${PROJECT_NAME}_node PRIVATE
        ${CMAKE_CURRENT_BINARY_DIR}
        include
        ${ros_INCLUDE_DIRS}
        ${kpl_INCLUDE_DIRS}
        ${EIGEN3_INCLUDE_DIRS}
        ${pinocchio_INCLUDE_DIRS}
)

target_link_libraries(${PROJECT_NAME}_node PRIVATE
        ${ros_LIBRARIES}
        ${Boost_LIBRARIES}
        ${kpl_LIBRARIES}
        ${Protobuf_LIBRARIES}
        /opt/ros/humble/lib/x86_64-linux-gnu/libruckig.so
        nlopt
        ${pinocchio_LIBRARIES}
        yaml-cpp
        fmt
        /opt/kpl/lib/libkpl_msgs__rosidl_typesupport_cpp.so
        /opt/kpl/lib/libSharedMemory.a
)

install(TARGETS ${PROJECT_NAME}_node
        RUNTIME DESTINATION /opt/kpl/bin
)
