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


# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}
					${CMAKE_CURRENT_SOURCE_DIR}/include 
					${CMAKE_CURRENT_BINARY_DIR}
				)

# Must be added to enable export macro
# ADD_DEFINITIONS(-DQUAZIP_BUILD)

# ======================================================================
ADD_EXECUTABLE(sweep_generator sweep_generator.c)
TARGET_LINK_LIBRARIES(sweep_generator EDFlib)
# ======================================================================
ADD_EXECUTABLE(sine_generator sine_generator.c)
TARGET_LINK_LIBRARIES(sine_generator EDFlib)
# ======================================================================
ADD_EXECUTABLE(test_edflib test_edflib.c)
TARGET_LINK_LIBRARIES(test_edflib EDFlib)
# ======================================================================
ADD_EXECUTABLE(test_generator test_generator.c)
TARGET_LINK_LIBRARIES(test_generator EDFlib)
# ======================================================================
# ======================================================================
INCLUDE(InstallRequiredSystemLibraries)
INSTALL(FILES ${CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS}
		DESTINATION bin
		COMPONENT ${PROJECT_NAME})
# ======================================================================
INSTALL(TARGETS sweep_generator  sine_generator  test_edflib test_generator
		# IMPORTANT: Add the foo library to the "export-set"
		EXPORT ${PROJECT_NAME}Targets
		BUNDLE DESTINATION  bin COMPONENT bin
		RUNTIME DESTINATION bin COMPONENT bin
	)