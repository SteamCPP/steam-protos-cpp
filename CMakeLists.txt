cmake_minimum_required(VERSION 3.21)
project(SteamProtos VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Protobuf REQUIRED)

set(PROTO_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/protobufs)
set(PROTO_OUTPUT_DIR ${CMAKE_CURRENT_BINARY_DIR}/generated)
file(MAKE_DIRECTORY ${PROTO_OUTPUT_DIR})

file(GLOB_RECURSE PROTO_FILES CONFIGURE_DEPENDS ${PROTO_ROOT}/*.proto)

protobuf_generate(
    LANGUAGE cpp
    PROTOS ${PROTO_FILES}
    IMPORT_DIRS ${PROTO_ROOT}
    APPEND_PATH FALSE
    PROTOC_OUT_DIR ${PROTO_OUTPUT_DIR}
    OUT_VAR GENERATED_PROTO_SRCS
)

add_library(SteamProtos ${GENERATED_PROTO_SRCS})

target_include_directories(SteamProtos
    PUBLIC
        $<BUILD_INTERFACE:${PROTO_OUTPUT_DIR}>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(SteamProtos PUBLIC protobuf::libprotobuf)

# == PACKAGING == #
include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/SteamProtosConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    cmake/SteamProtosConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SteamProtosConfig.cmake
    INSTALL_DESTINATION lib/cmake/SteamProtos
)

install(TARGETS SteamProtos
    EXPORT SteamProtosTargets
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${PROTO_OUTPUT_DIR}/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)

install(EXPORT SteamProtosTargets
    FILE SteamProtosTargets.cmake
    NAMESPACE SteamProtos::
    DESTINATION lib/cmake/SteamProtos
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/SteamProtosConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SteamProtosConfigVersion.cmake
    DESTINATION lib/cmake/SteamProtos
)

add_library(SteamProtos::SteamProtos ALIAS SteamProtos)