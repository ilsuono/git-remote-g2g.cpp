cmake_minimum_required(VERSION 3.5)

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

project(git-remote-g2g VERSION 0.0.1 LANGUAGES C CXX)

hunter_add_package(libp2p)
find_package(libp2p CONFIG REQUIRED)

add_executable(main src/main.cpp)