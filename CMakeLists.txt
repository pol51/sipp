cmake_minimum_required(VERSION 2.8.11)

set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS TRUE)
set(CMAKE_VERBOSE_MAKEFILE OFF)

project(sipp)

file(GLOB SIPP_SOURCES *.hpp *.cpp *.h *.c sipp_unittest.cpp)
file(GLOB IGNORED_SOURCES sipp_unittest.cpp)
list(REMOVE_ITEM SIPP_SOURCES ${IGNORED_SOURCES})

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  add_definitions(-D__LINUX)
endif()

add_definitions(-DRTP_STREAM)
add_definitions(-D_USE_OPENSSL)
add_definitions(-DPCAPPLAY)

find_library(OPENSSL_LIB ssl $ENV{OPENSSL_LIB_PATH})
find_library(PTHREAD_LIB pthread $ENV{PTHREAD_LIB_PATH})
find_library(PCAP_LIB pcap $ENV{PCAP_LIB_PATH})
find_library(CURSES_LIB curses $ENV{CURSES_LIB_PATH})

add_executable(sipp ${SIPP_SOURCES})
target_link_libraries(sipp ${OPENSSL_LIB} ${PTHREAD_LIB} ${PCAP_LIB} ${CURSES_LIB})
