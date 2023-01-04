cmake_minimum_required(VERSION 3.10)

project(unbalanced_pcsi)

set(CMAKE_CXX_STANDARD 17)

add_executable(test src/test.cpp src/filter.cpp)

find_package(TBB REQUIRED)

target_link_libraries(test TBB::tbb)

if (PARA)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DPARA")
    message("PARA")
    find_package(OpenMP REQUIRED)
    if (OpenMP_FOUND)
        target_link_libraries(test OpenMP::OpenMP_CXX)
    else()
        message(FATAL_ERROR "openmp not found")
    endif()
endif()

find_package(OpenSSL REQUIRED)

if (NOT OPENSSL_FOUND)
    include_directories(${OPENSSL_INCLUDE_DIRS})
endif()


find_package(SEAL)

if(NOT SEAL_FOUND)
    message("seal not found")
    set(SEAL_BUILD_EXAMPLES ON CACHE BOOL "test" FORCE)
    add_subdirectory(extern/SEAL)
endif()

target_link_libraries(test SEAL::seal)
target_link_libraries(test OpenSSL::Crypto OpenSSL::SSL)