# cmake settings
cmake_minimum_required(VERSION 2.8.11)

set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS TRUE)
set(CMAKE_VERBOSE_MAKEFILE OFF)

# project
project(sipp)

# params
set(WITH_SSL  ON  CACHE BOOL "use ssl")
set(WITH_PCAP ON  CACHE BOOL "use pcap")
set(WITH_SCTP OFF CACHE BOOL "use sctp")

# sources
file(GLOB SIPP_SOURCES *.hpp *.cpp *.h *.c sipp_unittest.cpp)
file(GLOB IGNORED_SOURCES sipp_unittest.cpp)

# system-specific
if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  add_definitions(-D__LINUX)
endif()

# minimal definitions
add_definitions(-DRTP_STREAM)

# find required libs
find_library(PTHREAD_LIB pthread)
find_library(CURSES_LIB curses)
set(LIBRARIES ${LIBRARIES} ${PTHREAD_LIB} ${CURSES_LIB})

# option: ssl
if(WITH_SSL)
  add_definitions(-D_USE_OPENSSL)
  find_library(OPENSSL_LIB ssl)
  set(LIBRARIES ${LIBRARIES} ${OPENSSL_LIB})
else()
  file(GLOB IGNORED_SOURCES ssl* ${IGNORED_SOURCES})
endif()

# option: pcap
if(WITH_PCAP)
  add_definitions(-DPCAPPLAY)
  find_library(PCAP_LIB pcap)
  set(LIBRARIES ${LIBRARIES} ${PCAP_LIB})
else()
  file(GLOB IGNORED_SOURCES prepare_pcap.* send_packets.* ${IGNORED_SOURCES})
endif()

# option: sctp
if(WITH_SCTP)
  add_definitions(-DUSE_SCTP)
  find_library(SCTP_LIB sctp)
  set(LIBRARIES ${LIBRARIES} ${SCTP_LIB})
endif()

# print summary
message(STATUS "=== Options ====================================================================")
message(STATUS "SSL : " ${WITH_SSL})
message(STATUS "PCAP: " ${WITH_PCAP})
message(STATUS "SCTP: " ${WITH_SCTP})
message(STATUS "================================================================================")

# hide internal values
mark_as_advanced(PTHREAD_LIB CURSES_LIB OPENSSL_LIB PCAP_LIB SCTP_LIB)

# remove unused sources
list(REMOVE_ITEM SIPP_SOURCES ${IGNORED_SOURCES})

# project target
add_executable(sipp ${SIPP_SOURCES})
target_link_libraries(sipp ${LIBRARIES})
