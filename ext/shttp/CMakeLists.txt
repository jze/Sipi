cmake_minimum_required(VERSION 2.8.10)
project(libshttps_builder C CXX)
include(ExternalProject)

#
# httplib
#
ExternalProject_Add(project_shttps
    INSTALL_DIR ${COMMON_LOCAL}
    DOWNLOAD_COMMAND cp -r ${SIPI_SOURCE_DIR}/shttps ${COMMON_SRCS}
    SOURCE_DIR ${COMMON_SRCS}/shttps
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${COMMON_LOCAL}
    INSTALL_COMMAND make install
    BUILD_IN_SOURCE 1
)
ExternalProject_Get_Property(project_shttps install_dir)
if(MAKE_SHARED_SIPI)
    add_library(shttp SHARED IMPORTED GLOBAL)
    set_property(TARGET shttp PROPERTY IMPORTED_LOCATION ${CONFIGURE_LIBDIR}/libshttp${CMAKE_SHARED_LIBRARY_SUFFIX})
else()
    add_library(shttp STATIC IMPORTED GLOBAL)
    set_property(TARGET shttp PROPERTY IMPORTED_LOCATION ${CONFIGURE_LIBDIR}/libshttp${CMAKE_STATIC_LIBRARY_SUFFIX})
endif()
add_dependencies(shttp project_shttps)

