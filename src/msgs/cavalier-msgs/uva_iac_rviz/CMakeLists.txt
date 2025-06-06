cmake_minimum_required(VERSION 3.5)
project(uva_iac_rviz)

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)

find_package(rviz_common REQUIRED)
find_package(rviz_rendering REQUIRED)
find_package(rviz_ogre_vendor REQUIRED)
find_package(pluginlib REQUIRED)
find_package(Armadillo REQUIRED)

find_package(uva_iac_msgs REQUIRED)

set(CMAKE_AUTOMOC ON)
find_package(Qt5 ${rviz_QT_VERSION} REQUIRED Core Widgets)
set(QT_LIBRARIES Qt5::Widgets)

include_directories(include ${OGRE_INCLUDE_DIRS} ${Qt5Widgets_INCLUDE_DIRS})

set(uva_iac_rviz_headers_to_moc
  include/uva_iac_rviz/polynomial_display.hpp
  include/uva_iac_rviz/polynomial_visual.hpp
  include/uva_iac_rviz/batch_detection_display.hpp
  include/uva_iac_rviz/batch_tracks_display.hpp
)


foreach(header "${uva_iac_rviz_headers_to_moc}")
  qt5_wrap_cpp(uva_iac_rviz_moc_files "${header}")
endforeach()

set(uva_iac_rviz_source_files
  src/polynomial_display.cpp
  src/polynomial_visual.cpp
  src/batch_detection_display.cpp
  src/batch_tracks_display.cpp
)

add_library(uva_iac_rviz SHARED
  ${uva_iac_rviz_moc_files}
  ${uva_iac_rviz_source_files}
)
target_link_libraries(uva_iac_rviz ${QT_LIBRARIES} ${ARMADILLO_LIBRARIES})

pluginlib_export_plugin_description_file(rviz_common plugin_description.xml)

ament_target_dependencies(uva_iac_rviz
  rviz_common
  rviz_ogre_vendor
  rviz_rendering
  uva_iac_msgs
)

install(
  TARGETS uva_iac_rviz
  EXPORT uva_iac_rviz
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)

install(
  DIRECTORY include/
  DESTINATION include
)

install(
  DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/icons"
  DESTINATION "share/${PROJECT_NAME}"
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
