cmake_minimum_required(VERSION 2.8.10)
project(curl_builder C CXX)
include(ExternalProject)

#
# get libcurl
#
ExternalProject_Add(project_curl
        INSTALL_DIR ${COMMON_LOCAL}
        URL https://curl.haxx.se/download/curl-7.51.0.tar.gz
        SOURCE_DIR ${COMMON_SRCS}/curl-7.51.0
        CONFIGURE_COMMAND ${COMMON_SRCS}/curl-7.51.0/configure --prefix=${COMMON_LOCAL} --enable-shared --enable-static --libdir=${CONFIGURE_LIBDIR} --with-ssl=${OPENSSL_ROOT_DIR} --disable-ldap
        BUILD_COMMAND make
        BUILD_IN_SOURCE 1
        )
ExternalProject_Get_Property(project_curl install_dir)
if(MAKE_SHARED_SIPI)
    add_library(curl SHARED IMPORTED GLOBAL)
    set_property(TARGET curl PROPERTY IMPORTED_LOCATION ${CONFIGURE_LIBDIR}/libcurl${CMAKE_SHARED_LIBRARY_SUFFIX})
    set(CMAKE_CREATE_SHARED "-DBUILD_SHARED_LIBS=ON")
else()
    add_library(curl STATIC IMPORTED GLOBAL)
    set_property(TARGET curl PROPERTY IMPORTED_LOCATION ${CONFIGURE_LIBDIR}/libcurl${CMAKE_STATIC_LIBRARY_SUFFIX})
    set(CMAKE_CREATE_SHARED "-DBUILD_SHARED_LIBS=OFF")
endif()
add_dependencies(curl project_curl)
