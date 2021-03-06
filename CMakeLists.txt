cmake_minimum_required(VERSION 3.1)

#set(SERVICE "Facebook")

project(${SERVICE}Client)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

option(BUILD_SHARED_LIBS "Build shared instead of static library" OFF)
option(BUILD_TESTS "Build tests for webdavclient" ON)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include/)

add_library(${PROJECT_NAME} STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/client.cpp)

find_package(OpenSSL REQUIRED)
find_package(CURL REQUIRED)
include_directories(${CURL_INCLUDE_DIR})

if(UNIX)
set(DEPENDS_LIBRARIES ${OPENSSL_LIBRARIES} ${CURL_LIBRARY})
elseif(MSVC)
set(DEPENDS_LIBRARIES ${OPENSSL_LIBRARIES} ${CURL_LIBRARY} ws2_32)
endif()

add_executable(Examples ${CMAKE_CURRENT_SOURCE_DIR}/examples/main.cpp)

target_link_libraries(Examples ${PROJECT_NAME} ${DEPENDS_LIBRARIES})

if(BUILD_TESTS)
    enable_testing()
    file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
    add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
    target_link_libraries(check ${PROJECT_NAME} ${DEPENDS_LIBRARIES})
    add_test(NAME check COMMAND check "-s" "-r" "compact" "--use-colour" "yes") 
endif()
