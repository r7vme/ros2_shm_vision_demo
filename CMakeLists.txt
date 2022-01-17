cmake_minimum_required(VERSION 3.8)
project(shm_vision_demo)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake_auto REQUIRED)
ament_auto_find_build_dependencies()
find_package(OpenCV REQUIRED)
find_package(iceoryx_posh CONFIG REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Image.msg"
)

ament_auto_add_executable(camera src/talker.cpp)
target_link_libraries(camera ${OpenCV_LIBS})
rosidl_target_interfaces(camera ${PROJECT_NAME} "rosidl_typesupport_cpp")

ament_auto_add_executable(display src/listener.cpp)
target_link_libraries(display ${OpenCV_LIBS})
rosidl_target_interfaces(display ${PROJECT_NAME} "rosidl_typesupport_cpp")

ament_auto_add_executable(filter src/filter_node.cpp)
target_link_libraries(filter ${OpenCV_LIBS})
rosidl_target_interfaces(filter ${PROJECT_NAME} "rosidl_typesupport_cpp")

ament_auto_add_executable(edge_detector src/edge_detector_node.cpp)
target_link_libraries(edge_detector ${OpenCV_LIBS})
rosidl_target_interfaces(edge_detector ${PROJECT_NAME} "rosidl_typesupport_cpp")

ament_auto_add_executable(optical_flow src/optical_flow_node.cpp)
target_link_libraries(optical_flow ${OpenCV_LIBS})
rosidl_target_interfaces(optical_flow ${PROJECT_NAME} "rosidl_typesupport_cpp")

ament_auto_add_executable(object_detector src/object_detector_node.cpp)
target_link_libraries(object_detector ${OpenCV_LIBS})
rosidl_target_interfaces(object_detector ${PROJECT_NAME} "rosidl_typesupport_cpp")

# download yolov3 weights to shm_vision_demo/config/
add_custom_command(TARGET object_detector
  PRE_BUILD
  COMMAND bash -c "wget -nc https://pjreddie.com/media/files/yolov3.weights"
  WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/config"
  COMMENT "YOLOv3 weights")
# download yolov3 cfg to shm_vision_demo/config/
add_custom_command(TARGET object_detector
  PRE_BUILD
  COMMAND bash -c "wget -nc https://raw.githubusercontent.com/pjreddie/darknet/master/cfg/yolov3.cfg"
  WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/config"
  COMMENT "YOLOv3 cfg")

ament_auto_package(
  INSTALL_TO_SHARE config scripts
)
