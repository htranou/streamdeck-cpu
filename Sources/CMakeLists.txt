cmake_minimum_required(VERSION 3.10)

cmake_policy(SET CMP0074 NEW)

project(com.elgato.cpu VERSION 1.2)

if (APPLE)
  set(
    STREAMDECK_PLUGIN_DIR
    "$ENV{HOME}/Library/ApplicationSupport/com.elgato.StreamDeck/Plugins"
  )
endif()
set(
  CMAKE_INSTALL_PREFIX
  "${STREAMDECK_PLUGIN_DIR}/${CMAKE_PROJECT_NAME}"
  CACHE STRING "See cmake documentation"
)

set(CMAKE_CXX_STANDARD 11)
set(CXX_STANDARD_REQUIRED true)
add_definitions(-DUNICODE=1)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static")

if (MSVC)
  add_definitions("/Zc:__cplusplus" -DUNICODE=1)
endif()



#boost
find_package(Boost REQUIRED)

include_directories("${CMAKE_SOURCE_DIR}")
include_directories("${Boost_INCLUDE_DIR}")

include("StreamDeckSDK.cmake")

set(
  SOURCES
  MyStreamDeckPlugin.cpp
  main.cpp
)
if (APPLE)
  list(APPEND SOURCES macOS/CpuUsageHelper.cpp)
elseif (WIN32)
  list(APPEND SOURCES Windows/CpuUsageHelper.cpp)
endif()
add_executable(
  cpu
  ${SOURCES}
)

target_link_libraries(cpu StreamDeckSDK pdh ws2_32)
target_link_options(cpu PRIVATE -static-libgcc -static-libstdc++)

