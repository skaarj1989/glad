cmake_minimum_required(VERSION 3.19)

project(glad)
if(PROJECT_BINARY_DIR STREQUAL PROJECT_SOURCE_DIR)
  message(
    FATAL_ERROR
      "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there."
  )
endif()

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)
  #option(BUILD_SHARED_LIBS "Use shared libraries" ON)
  set(BUILD_SHARED_LIBS ON)
endif()

find_package(OpenGL REQUIRED)

#
# ---
#

add_library(glad
  src/glad.c
  $<IF:${WIN32},
    src/glad_wgl.c,
    src/glad_glx.c
  >
)
target_include_directories(glad
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
target_link_libraries(glad
  PUBLIC OpenGL::GL
)

add_library(glad::glad ALIAS glad)

#
# --- Packaging ---
#

#[[

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

install(TARGETS glad
  EXPORT gladTargets
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  FRAMEWORK DESTINATION ${CMAKE_INSTALL_PREFIX}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/glad
)

write_basic_package_version_file(
    Config.cmake.in
    VERSION 1.0
    COMPATIBILITY AnyNewerVersion
)
configure_package_config_file(
    ${EnTT_SOURCE_DIR}/cmake/in/EnTTConfig.cmake.in
    glad.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/EnTT/cmake
)


export(EXPORT gladTargets NAMESPACE glad:: FILE gladTargets.cmake)

set(CMAKE_INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/glad)
install(EXPORT gladTargets
  DESTINATION ${CMAKE_INSTALL_CONFIGDIR}
  NAMESPACE glad::
)
install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

export(PACKAGE glad)
]]