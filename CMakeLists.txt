# Original work Copyright (c) 2013-2015, Roland Bock
# Modified work Copyright (c) 2016, Aaron Bishop
# All rights reserved.
# 
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
# 
#   Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
# 
#   Redistributions in binary form must reproduce the above copyright notice, this
#   list of conditions and the following disclaimer in the documentation and/or
#   other materials provided with the distribution.
# 
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
# ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
# ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

cmake_minimum_required(VERSION 2.6)
project (sqlpp11-connector-odbc)

add_definitions(-DNOMINMAX )

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMake)

find_package(ODBC REQUIRED)

set( ODBC_INCLUDE_DIRECTORIES   "c:/Program Files (x86)/Windows Kits/8.1/Include/um")

include_directories(${ODBC_INCLUDE_DIRECTORIES})

message(STATUS "Using ${CMAKE_CXX_COMPILER} (compiler id: ${CMAKE_CXX_COMPILER_ID})")
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
	set(CMAKE_CXX_FLAGS "-Wall -std=c++11 -stdlib=libc++ ${CMAKE_CXX_FLAGS}")
elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
	set(CMAKE_CXX_FLAGS "-Wall -std=c++11 ${CMAKE_CXX_FLAGS}")
endif ()

message(SEND_MESSAGE " PROJECT_SOURCE_DIR is '${PROJECT_SOURCE_DIR}'" )

set(SQLPP11_INCLUDE_DIR  "${PROJECT_SOURCE_DIR}/../sqlpp11/include" CACHE FILEPATH "Path to sqlpp11 includes")

if(NOT EXISTS ${SQLPP11_INCLUDE_DIR}/sqlpp11/exception.h)
  message(SEND_ERROR "Can't find exception.h in ${SQLPP11_INCLUDE_DIR}")
endif()

set(DATE_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/../date" CACHE FILEPATH "Path to Howard Hinnant's date library")

if(NOT EXISTS ${DATE_INCLUDE_DIR}/date.h)
	message(SEND_ERROR "Can't find date.h in ${DATE_INCLUDE_DIR}")
endif()

message(SEND_MESSAGE " including sqlpp11 from ${SQLPP11_INCLUDE_DIR}")
include_directories("${SQLPP11_INCLUDE_DIR}")
include_directories("${DATE_INCLUDE_DIR}")

set(include_dir "${PROJECT_SOURCE_DIR}/sqlpp11")
# file(GLOB_RECURSE sqlpp_headers ${include_dir}/*.h ${SQLPP11_INCLUDE_DIR}/*.h)
include_directories(${include_dir})
include_directories(${PROJECT_SOURCE_DIR}/include/)

install(DIRECTORY "${PROJECT_SOURCE_DIR}/include/sqlpp11" DESTINATION include)


add_subdirectory(src)

if(NOT ${CMAKE_SQLPP11_CONNECTOR_ODBC_TESTS_IGNORE})
    enable_testing()
    add_subdirectory(tests)
endif()

#target_link_libraries (sqlpp-odbc  odbc32.lib odbccp32.lib)