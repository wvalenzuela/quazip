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
PROJECT(test)
# ======================================================================
# ======================================================================
SET(APP_NAME )
STRING(TOUPPER ${PROJECT_NAME} APP_NAME)
MESSAGE("\n------------------------${PROJECT_NAME}------------------------")
# ======================================================================
SET(${APP_NAME}_VERSION_MAJOR 0)
SET(${APP_NAME}_VERSION_MINOR 0)
SET(${APP_NAME}_VERSION_PATCH 0)
SET(${APP_NAME}_VERSION_COUNT 0)
SET(${APP_NAME}_VERSION "${${APP_NAME}_VERSION_MAJOR}.${${APP_NAME}_VERSION_MINOR}.${${APP_NAME}_VERSION_PATCH}.${${APP_NAME}_VERSION_COUNT}" CACHE INTERNAL "")
# ===================================================================================

SET(QT_COMPONENTS_REQUIRED Core Gui Widgets OpenGL OpenGLExtensions Concurrent Sql Network PrintSupport)
FIND_PACKAGE(Qt5 REQUIRED COMPONENTS ${QT_COMPONENTS_REQUIRED})

FIND_PACKAGE(Qt5Test REQUIRED)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOMOC ON)

enable_testing(true)

#define qt libraries to use
SET(QT_LIBRARIES Qt5::Core Qt5::Gui Qt5::Widgets Qt5::OpenGL Qt5::OpenGLExtensions Qt5::Concurrent Qt5::Sql Qt5::Network Qt5::PrintSupport)

# The Qt5Widgets_INCLUDES also includes the include directories for
# dependencies QtCore and QtGui
INCLUDE_DIRECTORIES(${Qt5Widgets_INCLUDES})

# We need add -DQT_WIDGETS_LIB when using QtWidgets in Qt 5.
ADD_DEFINITIONS(${Qt5Widgets_DEFINITIONS})

# Executables fail to build with Qt 5 in the default configuration
# without -fPIE. We add that here.
SET(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} ${Qt5Widgets_EXECUTABLE_COMPILE_FLAGS})


# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}
					${CMAKE_CURRENT_SOURCE_DIR}/include 
					${CMAKE_CURRENT_BINARY_DIR}
					${CMAKE_SOURCE_DIR}/quazip/include
					${CMAKE_SOURCE_DIR}/quazip
				)

# Must be added to enable export macro
# ADD_DEFINITIONS(-DQUAZIP_BUILD)

SET(test_header qztest.h
	testjlcompress.h
	testquachecksum32.h
	testquagzipfile.h
	testquaziodevice.h
	testquazipdir.h
	testquazipfile.h
	testquazip.h
    testquazipnewinfo.h
    testquazipfileinfo.h
)
SET(test_sources 
	qztest.cpp 
	testjlcompress.cpp
	testquachecksum32.cpp
	testquagzipfile.cpp
	testquaziodevice.cpp
	testquazip.cpp
	testquazipdir.cpp
	testquazipfile.cpp
    testquazipnewinfo.cpp
    testquazipfileinfo.cpp
)


# ======================================================================
ADD_EXECUTABLE(${PROJECT_NAME} WIN32 MACOSX_BUNDLE
				${test_header} 
				${test_sources}
)
# ADD_EXECUTABLE(${PROJECT_NAME}_test WIN32 MACOSX_BUNDLE
# 				${test_header}
# 				${test_sources}
# )
add_test(NAME ${PROJECT_NAME} COMMAND ${PROJECT_NAME})

# target_link_libraries(${PROJECT_NAME}_test PRIVATE ${QT_LIBRARIES} Qt5::Test QuaZip)

TARGET_LINK_LIBRARIES(${PROJECT_NAME} ${QT_LIBRARIES} QuaZip Qt5::Test)
# ======================================================================
# ======================================================================
INCLUDE(InstallRequiredSystemLibraries)
INSTALL(FILES ${CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS}
		DESTINATION bin
		COMPONENT ${PROJECT_NAME})
# ======================================================================
INSTALL(TARGETS ${PROJECT_NAME}
		# IMPORTANT: Add the foo library to the "export-set"
		EXPORT ${PROJECT_NAME}Targets
		BUNDLE DESTINATION  bin COMPONENT bin
		RUNTIME DESTINATION bin COMPONENT bin
	)
