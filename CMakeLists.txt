cmake_minimum_required(VERSION 3.21)

if(${CMAKE_VERSION} VERSION_GREATER_EQUAL "3.27")
    cmake_policy(SET CMP0144 NEW)
endif()

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

hunter_add_package(libsecp256k1)
find_package(libsecp256k1 CONFIG REQUIRED)

hunter_add_package(Boost)
find_package(Boost CONFIG REQUIRED)

include(ExternalProject)
ExternalProject_Add(
    libgit2_PROJECT
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third_party/libgit2
    CMAKE_ARGS
        -DBUILD_SHARED_LIBS=OFF
        -DBUILD_TESTS=OFF
        -DBUILD_CLI=OFF
        -DCMAKE_INSTALL_BINDIR=<BINARY_DIR>
        -DCMAKE_INSTALL_LIBDIR=<INSTALL_DIR>/lib
        -DCMAKE_INSTALL_INCLUDEDIR=<INSTALL_DIR>/include
    CMAKE_CACHE_ARGS
        -DUSE_HTTPS:STRING=OFF
        -DUSE_AUTH_NTLM:STRING=OFF
        -DUSE_COMPRESSION:STRING=builtin
)
ExternalProject_Get_Property(libgit2_PROJECT INSTALL_DIR)

add_executable(main src/main.cpp)
target_link_libraries(main p2p::p2p_multiaddress)

add_executable(libp2p_echo_server
    src/libp2p_echo_server.cpp
)
target_link_libraries(libp2p_echo_server
    Boost::Boost.DI
    p2p::p2p_basic_host
    p2p::p2p_default_network
    p2p::p2p_peer_repository
    p2p::p2p_inmem_address_repository
    p2p::p2p_inmem_key_repository
    p2p::p2p_inmem_protocol_repository
    p2p::p2p_protocol_echo
    p2p::p2p_literals
)

add_executable(libp2p_echo_client
    src/libp2p_echo_client.cpp
)

target_link_libraries(libp2p_echo_client
    Boost::Boost.DI
    p2p::p2p_basic_host
    p2p::p2p_default_network
    p2p::p2p_peer_repository
    p2p::p2p_inmem_address_repository
    p2p::p2p_inmem_key_repository
    p2p::p2p_inmem_protocol_repository
    p2p::p2p_protocol_echo
    p2p::p2p_literals
)

file(GLOB SRC_EXAMPLES src/examples/*.c sec/examples/*.h)
add_executable(lg2 ${SRC_EXAMPLES})
target_include_directories(lg2 PRIVATE ${INSTALL_DIR}/include)
target_link_libraries(lg2 ${INSTALL_DIR}/lib/libgit2.a pthread)