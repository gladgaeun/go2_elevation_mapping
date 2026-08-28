cmake_minimum_required(VERSION 3.10)
project(convex_plane_decomposition)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 1. ament_cmake 및 의존성 패키지 찾기
find_package(ament_cmake REQUIRED)

set(AMENT_PACKAGE_DEPENDENCIES
  cgal5_ament
  grid_map_core
  grid_map_cv
  grid_map_filters_rsl
)

foreach(dep ${AMENT_PACKAGE_DEPENDENCIES})
  find_package(${dep} REQUIRED)
endforeach()

# 2. 외부 의존성 찾기
find_package(GMP 4.2 QUIET)
find_package(MPFR 2.2.1 QUIET)
find_package(Boost 1.57 QUIET)
find_package(OpenCV REQUIRED)
find_package(Eigen3 3.3 REQUIRED NO_MODULE)

# 3. 라이브러리 빌드
add_library(${PROJECT_NAME} SHARED
  src/contour_extraction/ContourExtraction.cpp
  src/contour_extraction/Upsampling.cpp
  src/ransac/RansacPlaneExtractor.cpp
  src/sliding_window_plane_extraction/SlidingWindowPlaneExtractor.cpp
  src/ConvexRegionGrowing.cpp
  src/Draw.cpp
  src/GridMapPreprocessing.cpp
  src/LoadGridmapFromImage.cpp
  src/PlanarRegion.cpp
  src/PlaneDecompositionPipeline.cpp
  src/Postprocessing.cpp
  src/SegmentedPlaneProjection.cpp
)

# 4. Include 디렉토리 설정 (grid_map_filters_rsl include 경로 포함)
target_include_directories(${PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../grid_map_filters_rsl/include>
  $<INSTALL_INTERFACE:include>
  ${EIGEN3_INCLUDE_DIR}
  ${grid_map_filters_rsl_INCLUDE_DIRS}
)

foreach(dep ${AMENT_PACKAGE_DEPENDENCIES})
  ament_target_dependencies(${PROJECT_NAME} ${dep})
endforeach()

# 5. 링크 설정
target_link_libraries(${PROJECT_NAME}
  ${OpenCV_LIBRARIES}
  gmp
  mpfr
)

target_compile_options(${PROJECT_NAME} PUBLIC -DCGAL_HAS_THREADS)

# 6. ament_export 설정
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_export_dependencies(
  ${AMENT_PACKAGE_DEPENDENCIES}
)

# 7. 설치(Install) 설정
install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/
  DESTINATION include
)

ament_package()
