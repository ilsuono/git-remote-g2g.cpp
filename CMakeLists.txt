cmake_minimum_required(VERSION 3.5)

if(${CMAKE_VERSION} VERSION_GREATER_EQUAL "3.27")
    cmake_policy(SET CMP0144 NEW)
endif()

set(CMAKE_POSITION_INDEPENDENT_CODE TRUE)
set(CMAKE_TOOLCHAIN_FILE
    "${CMAKE_SOURCE_DIR}/cmake/cxx20.cmake"
    CACHE
    FILEPATH
    "Default toolchain"
)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/qdrvm/hunter/archive/refs/tags/v0.25.3-qdrvm28.tar.gz"
    SHA1 "a4f1b0f42464e07790b7f90b783a822d71be6c6d"
    FILEPATH "${CMAKE_CURRENT_LIST_DIR}/cmake/HunterConfig.cmake"
)

cmake_policy(SET CMP0048 NEW)
cmake_policy(SET CMP0135 NEW)

project(git-remote-g2g VERSION 0.0.1 LANGUAGES C CXX)

hunter_add_package(libp2p)
find_package(libp2p CONFIG REQUIRED)

add_executable(main src/main.cpp)
target_link_libraries(main
    p2p::p2p_multiaddress
)