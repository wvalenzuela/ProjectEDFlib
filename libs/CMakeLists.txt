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
PROJECT(EDFlib)
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
    edflib.c 
)
SET(HEADERS 
	include/edflib.h 
)
SOURCE_GROUP("Source Files" FILES ${SRCS})
SOURCE_GROUP("Header Files" FILES ${HEADERS})

# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
MESSAGE("${CMAKE_CURRENT_SOURCE_DIR}/include ")
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}
					${CMAKE_CURRENT_SOURCE_DIR}/include 
					${CMAKE_CURRENT_BINARY_DIR}
				)


if(CMAKE_COMPILER_IS_GNUCXX)
    EXECUTE_PROCESS(COMMAND ${CMAKE_C_COMPILER} -dumpversion
                    OUTPUT_VARIABLE GCC_VERSION)
    if(GCC_VERSION VERSION_GREATER 4.8 OR GCC_VERSION VERSION_EQUAL 4.8)
        set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wpedantic")
    else(GCC_VERSION VERSION_GREATER 4.8 OR GCC_VERSION VERSION_EQUAL 4.8)
        set(CMAKE_CXX_FLAGS "-Wall -Wextra -pedantic")
    endif(GCC_VERSION VERSION_GREATER 4.8 OR GCC_VERSION VERSION_EQUAL 4.8)
endif(CMAKE_COMPILER_IS_GNUCXX)

if(CMAKE_CXX_COMPILER MATCHES "clang")
    set(CMAKE_CXX_FLAGS "-Wall -Wextra -pedantic -Wformat-nonliteral")
    set(CMAKE_C_FLAGS    "${CMAKE_C_FLAGS} -Wformat-nonliteral")
endif(CMAKE_CXX_COMPILER MATCHES "clang")


if("${CMAKE_C_COMPILER_ID}" MATCHES "GNU")
	set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS} -Wcast-align -Werror-implicit-function-declaration -Wchar-subscripts -Wall -W")
	set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS} -Wpointer-arith -Wwrite-strings -Wformat-security -Wmissing-format-attribute -fno-common")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wnon-virtual-dtor -ansi -Wcast-align -Wchar-subscripts -Wall -W -Wshadow")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wpointer-arith -Wformat-security")
	if (NOT APPLE)
		set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--fatal-warnings -Wl,--no-undefined ${CMAKE_SHARED_LINKER_FLAGS}")
		set(CMAKE_MODULE_LINKER_FLAGS "-Wl,--fatal-warnings -Wl,--no-undefined ${CMAKE_MODULE_LINKER_FLAGS}")
	endif ()
endif()

# Must be added to enable export macro
	
#
# Check to see if we have large file support
#
if (APPLE)
  add_definitions(-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64)
  add_definitions(-DNCBI_XCODE_BUILD -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64)
elseif (UNIX)
  add_definitions(-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64)
endif (APPLE)

SET(CMAKE_REQUIRED_DEFINITIONS -D_LARGEFILE64_SOURCE=1)
# We add these other definitions here because CheckTypeSize.cmake
# in CMake 2.4.x does not automatically do so and we want
# compatibility with CMake 2.4.x.
IF(HAVE_SYS_TYPES_H)
    LIST(APPEND CMAKE_REQUIRED_DEFINITIONS -DHAVE_SYS_TYPES_H)
ENDIF()
IF(HAVE_STDINT_H)
    LIST(APPEND CMAKE_REQUIRED_DEFINITIONS -DHAVE_STDINT_H)
ENDIF()
if(HAVE_STDDEF_H)
    LIST(APPEND CMAKE_REQUIRED_DEFINITIONS -DHAVE_STDDEF_H)
ENDIF()
INCLUDE (CheckTypeSize)
check_type_size(off64_t OFF64_T)
IF(HAVE_OFF64_T)
   ADD_DEFINITIONS(-D_LARGEFILE64_SOURCE=1)
ENDIF()
SET(CMAKE_REQUIRED_DEFINITIONS) # clear variable
		
# ======================================================================
# Instruct CMake to run moc automatically when needed.

add_library(${PROJECT_NAME} SHARED ${SRCS} ${HEADERS})
add_library(${PROJECT_NAME}-static STATIC ${SRCS} ${HEADERS})
 
SET_TARGET_PROPERTIES(${PROJECT_NAME}-static PROPERTIES OUTPUT_NAME ${PROJECT_NAME} CLEAN_DIRECT_OUTPUT 1)
SET_TARGET_PROPERTIES(${PROJECT_NAME} ${PROJECT_NAME}-static PROPERTIES VERSION ${${APP_NAME}_VERSION} SOVERSION 1 DEBUG_POSTFIX _d)

# Link against ZLIB_LIBRARIES if needed (on Windows this variable is empty)
# TARGET_LINK_LIBRARIES(${PROJECT_NAME} Qt5::Core ${ZLIB_LIBRARIES})
# TARGET_LINK_LIBRARIES(${PROJECT_NAME}-static Qt5::Core ${ZLIB_LIBRARIES})

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