cmake_minimum_required(VERSION 3.0)
set(project_name cinatra_example)
project(${project_name})
#set(CMAKE_BUILD_TYPE Release)
if (MSVC)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++latest")
else ()
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -pthread -msse4.2 -std=c++20")
endif ()

#option(ENABLE_SSL   "Set to switch to build use ssl"  OFF)
#option(ENABLE_GZIP  "Set to switch to build use gzip" OFF)
SET(ENABLE_GZIP OFF)
SET(ENABLE_SSL OFF)
SET(ENABLE_CLIENT_SSL OFF)
SET(ENABLE_ASIO_STANDALONE ON)

if (ENABLE_SSL)
	add_definitions(-DCINATRA_ENABLE_SSL)
	message(STATUS "Use SSL")
endif()

if(ENABLE_GZIP)
	add_definitions(-DCINATRA_ENABLE_GZIP)
endif()

if(ENABLE_CLIENT_SSL)
	add_definitions(-DCINATRA_ENABLE_CLIENT_SSL)
endif()

if(ENABLE_ASIO_STANDALONE)
	add_definitions(-DASIO_STANDALONE)
else()
	find_package(Boost 1.60 REQUIRED COMPONENTS system)
endif()

if (ENABLE_SSL)
find_package(OpenSSL REQUIRED)
endif()
if (ENABLE_CLIENT_SSL)
	find_package(OpenSSL REQUIRED)
endif()

if (ENABLE_GZIP)
	find_package(ZLIB REQUIRED)
endif()

set(CINATRA_EXAMPLE
	main.cpp
	)

add_executable(${project_name} ${CINATRA_EXAMPLE})
include_directories(${Boost_INCLUDE_DIRS} ${OPENSSL_INCLUDE_DIR})
if(LINUX)
	target_link_libraries(${project_name} ${Boost_LIBRARIES} -lstdc++fs)
endif()
if (ENABLE_SSL)
	target_link_libraries(${project_name} ${OPENSSL_LIBRARIES} pthread -ldl)
endif()

if (ENABLE_CLIENT_SSL)
	target_link_libraries(${project_name} ${OPENSSL_LIBRARIES} pthread -ldl)
endif()

if (ENABLE_GZIP)
	target_link_libraries(${project_name} ${ZLIB_LIBRARIES})
endif()
install(TARGETS ${project_name} DESTINATION include)
