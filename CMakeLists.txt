cmake_minimum_required(VERSION 3.25)

project(glad)
if(PROJECT_BINARY_DIR STREQUAL PROJECT_SOURCE_DIR)
  message(
    FATAL_ERROR
      "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there."
  )
endif()

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)

  set(CMAKE_CXX_STANDARD 20)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  set(CMAKE_CXX_EXTENSIONS OFF)

  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib") # .lib
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib") # .dll
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin") # .exe

  option(BUILD_SHARED_LIBS "Use shared libraries" OFF)
endif()

find_package(Vulkan REQUIRED)

add_library(glad "src/vulkan.c")
if(WIN32)
  target_compile_definitions(glad PUBLIC "VK_USE_PLATFORM_WIN32_KHR")
elseif(LINUX)
  target_compile_definitions(glad PUBLIC "VK_USE_PLATFORM_XCB_KHR")
endif()

target_include_directories(
  glad PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
              $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
target_link_libraries(glad PUBLIC Vulkan::Vulkan)

add_library(glad::vk ALIAS glad)

include(GNUInstallDirs)
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
