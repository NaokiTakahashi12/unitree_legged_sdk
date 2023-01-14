cmake_minimum_required(VERSION 3.8.0)
project(unitree_legged_sdk
  VERSION 3.8.0
  LANGUAGES CXX
)

option(unitree_legged_sdk_USE_STATIC_LIBRARY "Use statically linked library" FALSE)
option(unitree_legged_sdk_BUILD_EXAMPLES "Build example executables" TRUE)
option(unitree_legged_sdk_INSTALL_EXAMPLES "Install example executables" FALSE)

find_package(Threads REQUIRED)
find_package(Boost REQUIRED)

# check arch and os
message(STATUS "CMAKE_SYSTEM_PROCESSOR: ${CMAKE_SYSTEM_PROCESSOR}")
if("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "x86_64.*")
  set(ARCH amd64)
elseif("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "aarch64.*")
  set(ARCH arm64)
else()
  message(FATAL_ERROR "Not support processor type ${CMAKE_SYSTEM_PROCESSOR}")
endif()

list(APPEND unitree_legged_sdk_INCLUDE_DIRS ${Boost_INCLUDE_DIRS})
list(APPEND unitree_legged_sdk_INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include)

if(unitree_legged_sdk_USE_STATIC_LIBRARY)
  set(unitree_legged_sdk_LIBRARY_PATH
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/cpp/${ARCH}/libunitree_legged_sdk.a
  )
else()
  set(unitree_legged_sdk_LIBRARY_PATH
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/cpp/${ARCH}/libunitree_legged_sdk.so
  )
endif()

add_library(unitree_legged_sdk UNKNOWN IMPORTED)
set_target_properties(unitree_legged_sdk
  PROPERTIES
    IMPORTED_LOCATION ${unitree_legged_sdk_LIBRARY_PATH}
    INTERFACE_INCLUDE_DIRECTORIES "${unitree_legged_sdk_INCLUDE_DIRS}"
    VERSION ${PROJECT_VERSION}
)

if(unitree_legged_sdk_BUILD_EXAMPLES)
  # one pc one process
  function(add_example_executable executable_name)
    cmake_parse_arguments(example_executable "" "" "SOURCES" ${ARGN})
    add_executable(${executable_name}
        ${example_executable_SOURCES}
    )
    target_link_libraries(${executable_name}
      PUBLIC
        Threads::Threads
        unitree_legged_sdk
    )
    target_compile_features(${executable_name}
      PUBLIC
        cxx_std_14
    )
    set_target_properties(${executable_name}
      PROPERTIES
        VERSION ${PROJECT_VERSION}
    )
  endfunction()

  add_example_executable(example_position
    SOURCES
      example/example_position.cpp
  )
  add_example_executable(example_velocity
    SOURCES
      example/example_velocity.cpp
  )
  add_example_executable(example_torque
    SOURCES
      example/example_torque.cpp
  )
  add_example_executable(example_walk
    SOURCES
      example/example_walk.cpp
  )
  add_example_executable(example_joystick
    SOURCES
      example/example_joystick.cpp
  )
endif()

include(GNUInstallDirs)

# install
install(
  DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
install(
  FILES ${unitree_legged_sdk_LIBRARY_PATH}
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/unitree
)
if(unitree_legged_sdk_INSTALL_EXAMPLES AND unitree_legged_sdk_BUILD_EXAMPLES)
  install(
    TARGETS
      example_position
      example_velocity
      example_torque
      example_walk
      example_joystick
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}/unitree
  )
endif()
set(install_cmake_config_destination_path ${CMAKE_INSTALL_LIBDIR}/cmake/UnitreeLeggedSDK)
include(CMakePackageConfigHelpers)
configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/share/cmake/unitree_legged_sdk/UnitreeLeggedSDKConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/UnitreeLeggedSDKConfig.cmake
  INSTALL_DESTINATION ${install_cmake_config_destination_path}
)
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/UnitreeLeggedSDKConfig.cmake
  DESTINATION ${install_cmake_config_destination_path}
)
