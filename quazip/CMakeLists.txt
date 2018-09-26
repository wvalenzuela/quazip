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
PROJECT(QuaZip)
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

SET(SRCS
    JlCompress.cpp 
    qioapi.cpp 
    quaadler32.cpp 
    quacrc32.cpp 
    quagzipfile.cpp 
    quaziodevice.cpp 
    quazip.cpp 
    quazipdir.cpp 
    quazipfile.cpp 
    quazipfileinfo.cpp 
    quazipnewinfo.cpp 
    unzip.c 
    zip.c 
)
SET(HEADERS 
	include/ioapi.h 
	include/JlCompress.h 
	include/minizip_crypt.h 
	include/quaadler32.h 
	include/quachecksum32.h 
	include/quacrc32.h 
	include/quagzipfile.h 
	include/quaziodevice.h 
	include/quazip_global.h 
	include/quazip.h 
	include/quazipdir.h 
	include/quazipfile.h 
	include/quazipfileinfo.h 
	include/quazipnewinfo.h 
	include/unzip.h 
	include/zip.h
)
SOURCE_GROUP("Source Files" FILES ${SRCS})
SOURCE_GROUP("Header Files" FILES ${HEADERS})

# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
MESSAGE("${CMAKE_CURRENT_SOURCE_DIR}/include ")
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}
					${CMAKE_CURRENT_SOURCE_DIR}/include 
					${CMAKE_CURRENT_BINARY_DIR}
					${ZLIB_INCLUDE_DIRS}
				)

# Must be added to enable export macro
ADD_DEFINITIONS(-DQUAZIP_BUILD)

# ======================================================================
# Instruct CMake to run moc automatically when needed.
SET(CMAKE_AUTOMOC ON)
# qt_wrap_cpp(MOC_SRCS ${HEADERS})
# set(SRCS ${SRCS} ${MOC_SRCS})
GET_TARGET_PROPERTY(QtCore_location Qt5::Core LOCATION)

add_library(${PROJECT_NAME} SHARED ${SRCS} ${HEADERS})
add_library(${PROJECT_NAME}-static STATIC ${SRCS} ${HEADERS})
 
SET_TARGET_PROPERTIES(${PROJECT_NAME}-static PROPERTIES OUTPUT_NAME ${PROJECT_NAME} CLEAN_DIRECT_OUTPUT 1)
SET_TARGET_PROPERTIES(${PROJECT_NAME} ${PROJECT_NAME}-static PROPERTIES VERSION ${${APP_NAME}_VERSION} SOVERSION 1 DEBUG_POSTFIX _d)

# Link against ZLIB_LIBRARIES if needed (on Windows this variable is empty)
TARGET_LINK_LIBRARIES(${PROJECT_NAME} Qt5::Core ${ZLIB_LIBRARIES})
TARGET_LINK_LIBRARIES(${PROJECT_NAME}-static Qt5::Core ${ZLIB_LIBRARIES})

QT5_USE_MODULES(${PROJECT_NAME} Core)
QT5_USE_MODULES(${PROJECT_NAME}-static Core)
# ======================================================================
TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} PUBLIC 
						$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>  
						$<INSTALL_INTERFACE:include>
					)
IF(WIN32)
	# Properties->General->Output Directory
	SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES
						  RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
ENDIF(WIN32)
# ======================================================================
# ======================================================================
SET_PROPERTY(TARGET ${PROJECT_NAME} PROPERTY VERSION ${${APP_NAME}_VERSION})
SET_PROPERTY(TARGET ${PROJECT_NAME} PROPERTY SOVERSION 1)
SET_PROPERTY(TARGET ${PROJECT_NAME} PROPERTY INTERFACE_${APP_NAME}_MAJOR_VERSION 1)
SET_PROPERTY(TARGET ${PROJECT_NAME} APPEND PROPERTY COMPATIBLE_INTERFACE_STRING ${APP_NAME}_MAJOR_VERSION)	
# ======================================================================
INCLUDE(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(
  "${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${${APP_NAME}_VERSION}
  COMPATIBILITY AnyNewerVersion
)
EXPORT(TARGETS ${PROJECT_NAME}
  	   FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Exports.cmake"
)
CONFIGURE_FILE( ${CMAKE_CURRENT_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake.in
  				${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  			  	@ONLY
)
SET(ConfigPackageLocation lib/cmake/${PROJECT_NAME})

INSTALL(EXPORT ${PROJECT_NAME}Targets
  		FILE ${PROJECT_NAME}Targets.cmake DESTINATION ${ConfigPackageLocation}
)

INSTALL(
	FILES 			"${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
	DESTINATION 	${ConfigPackageLocation}
	COMPONENT 		dev
)
# ======================================================================
INCLUDE(InstallRequiredSystemLibraries)
INSTALL(FILES ${CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS}
		DESTINATION bin
		COMPONENT ${PROJECT_NAME})
# ======================================================================
SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES
					  PUBLIC_HEADER "${HEADERS}")
SET_TARGET_PROPERTIES(${PROJECT_NAME}-static PROPERTIES
					  PUBLIC_HEADER "${HEADERS}")
# ======================================================================
INSTALL(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}-static
		# IMPORTANT: Add the foo library to the "export-set"
		EXPORT ${PROJECT_NAME}Targets
		BUNDLE DESTINATION  bin COMPONENT bin
		RUNTIME DESTINATION bin COMPONENT bin
		LIBRARY DESTINATION lib COMPONENT lib
		ARCHIVE DESTINATION lib COMPONENT lib
		PUBLIC_HEADER DESTINATION include/${PROJECT_NAME}
		)