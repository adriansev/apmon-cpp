cmake_minimum_required(VERSION 2.8.12 FATAL_ERROR)

foreach(p CMP0005  # Properly escape preprocessor definitions (v2.6, v3.0.2)
          CMP0025  # Compiler id for Apple Clang is now AppleClang (v3.0)
          CMP0042  # MACOSX_RPATH is enabled by default (v3.0)
          CMP0048  # set VERSION as documented by the project() command.
          CMP0053  # Simplify variable reference and escape sequence evaluation (v3.1.3)
          CMP0068  # RPATH settings on macOS do not affect install_name (v3.9 and newer)
       )
  if(POLICY ${p})
    cmake_policy(SET ${p} NEW)
  endif()
endforeach()

project(apmoncpp VERSION 2.0.6 DESCRIPTION "ApMon_cpp library")

message(STATUS "CMake platform: ${CMAKE_SYSTEM}")
message(STATUS "Build folder: ${AliPhysics_BINARY_DIR}")
message(STATUS "Source folder: ${AliPhysics_SOURCE_DIR}")
message(STATUS "Installation folder: ${CMAKE_INSTALL_PREFIX}")

include(GNUInstallDirs)

set (srclist src/xdr.cpp src/mon_constants.cpp src/monitor_utils.cpp src/proc_utils.cpp src/ApMon.cpp src/utils.cpp)

add_library(objlib OBJECT ${srclist})

# shared libraries need PIC
set_property(TARGET objlib PROPERTY POSITION_INDEPENDENT_CODE 1)

# shared and static libraries built from the same object files
add_library(apmoncpp SHARED $<TARGET_OBJECTS:objlib>)
##add_library(apmoncpp_static STATIC $<TARGET_OBJECTS:objlib>)

##SET_TARGET_PROPERTIES(apmoncpp_shared PROPERTIES OUTPUT_NAME libapmoncpp CLEAN_DIRECT_OUTPUT 1)
##SET_TARGET_PROPERTIES(apmoncpp_static PROPERTIES OUTPUT_NAME libapmoncpp CLEAN_DIRECT_OUTPUT 1)

set_target_properties ( apmoncpp PROPERTIES VERSION ${PROJECT_VERSION} SOVERSION 2 )

configure_file(apmoncpp.pc.in apmoncpp.pc @ONLY)

include_directories (AFTER /usr/include/tirpc )
target_include_directories(apmoncpp PRIVATE src)

install(TARGETS apmoncpp
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(FILES ${CMAKE_BINARY_DIR}/apmoncpp.pc
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig)

