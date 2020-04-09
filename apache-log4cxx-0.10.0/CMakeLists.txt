cmake_minimum_required(VERSION 3.5)
project (Log4cxx)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

option(FORCE_BUILD_VENDOR_PKG
  "Build Log4cxx from source, even if system-installed package is available"
  ON)

if(NOT FORCE_BUILD_VENDOR_PKG)
  find_package(Log4cxx)
endif()

find_package(ament_cmake REQUIRED)


set(LOG4CXX_INCLUDE_DIRECTORIES
  ${PROJECT_SOURCE_DIR}/src/main/include
  ${PROJECT_SOURCE_DIR}/../apr-1.7.0/include
  ${PROJECT_SOURCE_DIR}/../apr-util-1.6.1/include
  )

include_directories(${LOG4CXX_INCLUDE_DIRECTORIES})

file(GLOB_RECURSE LOG4CXX_SRC
  "${PROJECT_SOURCE_DIR}/src/main/cpp/*.cpp"
)

add_library(Log4cxx SHARED ${LOG4CXX_SRC})

target_include_directories(Log4cxx PUBLIC ${LOG4CXX_INCLUDE_DIRECTORIES})

SET_TARGET_PROPERTIES(Log4cxx PROPERTIES
  COMPILE_DEFINITIONS "_USRDLL;DLL_EXPORTS;LOG4CXX;STDC_HEADERS;HAVE_MBSRTOWCS;HAVE_WCSTOMBS;HAVE_SYSLOG;HAVE_FWIDE;")

target_link_libraries(Log4cxx)

#install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ DESTINATION ${CMAKE_INSTALL_PREFIX})
#INSTALL(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ DESTINATION include)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

install(
  TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

# Install launch files.
#install(DIRECTORY
#  launch
#  DESTINATION share/${PROJECT_NAME}/
#)

#install(
#  DIRECTORY src/main/include/log4cxx
#  DESTINATION include
#)
# specific order: dependents before dependencies
#ament_export_include_directories(include)
#ament_export_libraries(${PROJECT_NAME})
ament_package()
