CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
IF(POLICY CMP0020)
    cmake_policy(SET CMP0020 NEW)
ENDIF()
IF(POLICY CMP0043)
    cmake_policy(SET CMP0043 NEW)
ENDIF()
IF(POLICY CMP0042)
    cmake_policy(SET CMP0042 NEW)
ENDIF()
IF(POLICY CMP0043)
    cmake_policy(SET CMP0043 NEW)
ENDIF()
IF(POLICY CMP0042)
    cmake_policy(SET CMP0042 NEW)
ENDIF()
IF(POLICY CMP0071)
    cmake_policy(SET CMP0071 NEW)
ENDIF()
# ======================================================================
# ======================================================================
PROJECT(ProjectQuaZip)
# ======================================================================

option(BUILD_WITH_QT4 "Build QuaZip with Qt4 no matter if Qt5 was found" OFF)
option(BUILD_TEST "Include test options" OFF)
# ======================================================================
SET(QT_BASE_DIRECTORY " "  CACHE PATH "Qt base Directory")
IF(NOT EXISTS ${QT_BASE_DIRECTORY})
    MESSAGE(FATAL_ERROR "\n${PROJECT_NAME}: Qt base directory error please define the directory")
ENDIF()

SET(QT_PLUGIN_DIRECTORY ${QT_BASE_DIRECTORY}/plugins)
SET(QT_BIN_DIRECTORY ${QT_BASE_DIRECTORY}/bin)

SET(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} ${QT_BASE_DIRECTORY})

if( NOT BUILD_WITH_QT4 )
    # try Qt5 first, and prefer that if found
    FIND_PACKAGE(Qt5 REQUIRED COMPONENTS Core)
endif()

if(Qt5Core_FOUND)
    set(CMAKE_CXX_STANDARD 11)
    set(QTCORE_LIBRARIES ${Qt5Core_LIBRARIES})
    set(QUAZIP_LIB_VERSION_SUFFIX 5)
    # if there is no QT_ROOT, try to deduce it from Qt QtCore include
    if("${QT_ROOT}" STREQUAL "")
        set(QT_ROOT ${QT_QTCORE_INCLUDE_DIR}/../..)
    endif()
    include_directories(${Qt5Core_INCLUDE_DIRS})

    macro(qt_wrap_cpp)
        qt5_wrap_cpp(${ARGN})
    endmacro()
else()
    set(qt_min_version "4.5.0")
    find_package(Qt4 REQUIRED)
    set(QT_USE_QTGUI false)
    include(${QT_USE_FILE})
    include_directories(${QT_INCLUDES})
    set(QTCORE_LIBRARIES ${QT_QTCORE_LIBRARY})

    macro(qt_wrap_cpp)
        qt4_wrap_cpp(${ARGN})
    endmacro()
endif()

# Use system zlib on unix and Qt ZLIB on Windows
if(UNIX OR MINGW)
    find_package(ZLIB REQUIRED)
else(UNIX OR MINGW)
    set(ZLIB_INCLUDE_DIRS "" CACHE PATH "Path to ZLIB headers of Qt")
    set(ZLIB_LIBRARIES "" CACHE FILEPATH "File to ZLIB bibrary of Qt")
    if(NOT EXISTS "${ZLIB_INCLUDE_DIRS}/zlib.h")
        message("Please specify a valid zlib include dir")
    endif(NOT EXISTS "${ZLIB_INCLUDE_DIRS}/zlib.h")
endif(UNIX OR MINGW)

# All build libraries are moved to this directory
# set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

# set(LIB_SUFFIX "" CACHE STRING "Define suffix of directory name (32/64)")
# set(LIB_DESTINATION "${CMAKE_INSTALL_PREFIX}/lib${LIB_SUFFIX}" CACHE STRING "Library directory name" FORCE)

add_subdirectory(quazip)
if( BUILD_TEST )
	add_subdirectory(qztest)
endif()
# install(FILES FindQuaZip.cmake RENAME FindQuaZip${QUAZIP_LIB_VERSION_SUFFIX}.cmake DESTINATION ${CMAKE_ROOT}/Modules)
