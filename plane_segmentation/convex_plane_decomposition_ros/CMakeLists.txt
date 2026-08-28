cmake_minimum_required(VERSION 3.10)
project(convex_plane_decomposition_ros)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 1. ament_cmake 및 의존성 패키지 찾기
find_package(ament_cmake REQUIRED)

set(AMENT_PACKAGE_DEPENDENCIES
  rclcpp
  grid_map_core
  grid_map_ros
  grid_map_cv
  grid_map_msgs
  geometry_msgs
  convex_plane_decomposition
  convex_plane_decomposition_msgs
  tf2_ros
  visualization_msgs
)

foreach(dep ${AMENT_PACKAGE_DEPENDENCIES})
  find_package(${dep} REQUIRED)
endforeach()

# 외부 의존성
find_package(OpenCV REQUIRED)
find_package(Eigen3 3.3 REQUIRED NO_MODULE)

# 2. 라이브러리 빌드
add_library(${PROJECT_NAME} SHARED
  src/ConvexPlaneDecompositionRos.cpp
  src/MessageConversion.cpp
  src/ParameterLoading.cpp
  src/RosVisualizations.cpp
)

target_include_directories(${PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${EIGEN3_INCLUDE_DIR}
)

foreach(dep ${AMENT_PACKAGE_DEPENDENCIES})
  ament_target_dependencies(${PROJECT_NAME} ${dep})
endforeach()

target_link_libraries(${PROJECT_NAME}
  ${OpenCV_LIBRARIES}
)

# 3. 실행 노드 빌드
add_executable(${PROJECT_NAME}_node
  src/ConvexPlaneDecompositionNode.cpp
)
ament_target_dependencies(${PROJECT_NAME}_node ${AMENT_PACKAGE_DEPENDENCIES})
target_link_libraries(${PROJECT_NAME}_node
  ${PROJECT_NAME}
  ${OpenCV_LIBRARIES}
)

add_executable(${PROJECT_NAME}_save_elevationmap
  src/SaveElevationMapAsImageNode.cpp
)
ament_target_dependencies(${PROJECT_NAME}_save_elevationmap ${AMENT_PACKAGE_DEPENDENCIES})
target_link_libraries(${PROJECT_NAME}_save_elevationmap
  ${PROJECT_NAME}
  ${OpenCV_LIBRARIES}
)

add_executable(${PROJECT_NAME}_approximation_demo_node
  src/ConvexApproximationDemoNode.cpp
)
ament_target_dependencies(${PROJECT_NAME}_approximation_demo_node ${AMENT_PACKAGE_DEPENDENCIES})
target_link_libraries(${PROJECT_NAME}_approximation_demo_node
  ${PROJECT_NAME}
  ${OpenCV_LIBRARIES}
)

# 4. export 및 install 설정
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_export_dependencies(${AMENT_PACKAGE_DEPENDENCIES})

install(TARGETS
  ${PROJECT_NAME}
  ${PROJECT_NAME}_node
  ${PROJECT_NAME}_save_elevationmap
  ${PROJECT_NAME}_approximation_demo_node
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)

install(DIRECTORY include/
  DESTINATION include
)

install(DIRECTORY config launch data rviz
  DESTINATION share/${PROJECT_NAME}
  FILES_MATCHING PATTERN "*"
  PATTERN "*.git*" EXCLUDE
)

ament_package()
