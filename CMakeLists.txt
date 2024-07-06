#  This is a fork of Sigrok's libserialport that is compatible with cmake so that it can be compiled using MinGW on Windows
#	with no need for Cygwin or MSYS2. I have only tested this file with MinGW, so if you wish to use it with other systems
#	I would suggest you adjusthe "target_link_libraries" accordinly
#
#	Scott Mudge - mail@scottmudge.com

cmake_minimum_required(VERSION 3.2)
project(serialport)

set(CMAKE_DEPENDS)
set(PKGCONFIG_DEPENDS)


if (WIN32)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c99 -Wall -Wextra -pedantic -Wmissing-prototypes -Wshadow ")
    set(CMAKE_EXE_LINKER_FLAGS --enable-auto-import)
else(WIN32)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c99 -Wall -Wextra -pedantic -Wmissing-prototypes -Wshadow")
endif(WIN32)
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${CMAKE_CXX_FLAGS}")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ${CMAKE_CXX_FLAGS} -s -Os")

set(SOURCE_FILES serialport.c timing.c libserialport_internal.h libserialport.h config.h)
if (WIN32)
set (SOURCE_FILES ${SOURCE_FILES} windows.c)
endif(WIN32)
if (UNIX)
set (SOURCE_FILES ${SOURCE_FILES} linux.c linux_termios.c linux_termios.h)
endif()
if(APPLE)
set (SOURCE_FILES ${SOURCE_FILES} macosx.c)
endif()
if (CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
set (SOURCE_FILES ${SOURCE_FILES} freebsd.c)
endif (CMAKE_SYSTEM_NAME MATCHES "FreeBSD")


set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix "\${prefix}")
set(libdir "\${exec_prefix}/${CMAKE_INSTALL_LIBDIR}")
set(bindir "\${exec_prefix}/${CMAKE_INSTALL_BINDIR}")
set(includedir "\${prefix}/${CMAKE_INSTALL_INCLUDEDIR}")
configure_file("libserialport.pc.in" "${CMAKE_INSTALL_PREFIX}/lib/pkgconfig/libserialport.pc" @ONLY)


  
add_library(${PROJECT_NAME}-static STATIC ${SOURCE_FILES})
set_target_properties(${PROJECT_NAME}-static PROPERTIES OUTPUT_NAME ${PROJECT_NAME}) 
add_library(${PROJECT_NAME}-shared SHARED ${SOURCE_FILES})
set_target_properties(${PROJECT_NAME}-shared PROPERTIES OUTPUT_NAME ${PROJECT_NAME}) 
include_directories(./)
if(WIN32)
target_link_libraries(${PROJECT_NAME}-static cfgmgr32 SetupAPI)
target_link_libraries(${PROJECT_NAME}-shared cfgmgr32 SetupAPI)
endif()

install(TARGETS ${PROJECT_NAME}-static ${PROJECT_NAME}-shared
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)

    
install(FILES libserialport.h
    DESTINATION include)

install(FILES libserialport.pc
    DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/pkgconfig")
