﻿cmake_minimum_required (VERSION 3.9)
cmake_policy(SET CMP0048 NEW)
project(minimal_opengl_exmaple VERSION 1.0)

if(NOT MSVC)
	SET(COMP_FLAGS "-lGL -lglfw -lGLEW -std=c++11")
	SET(CMAKE_CXX_FLAGS "${COMP_FLAGS}")
endif(NOT MSVC)

###########################################################################
# OpenGL
find_package(OpenGL REQUIRED)
if( NOT OPENGL_FOUND )
	message(FATAL_ERROR 
		"OpenGL library could not be found on this system.")
endif()

###########################################################################

###########################################################################
# glfw
add_subdirectory(ext/glfw)
include_directories (
  ext/glfw/include
  ${CMAKE_BINARY_DIR}/ext/glfw
)
###########################################################################

###########################################################################
# glew
add_subdirectory(ext/glew-2.1.0/build/cmake)
include_directories (
  ext/glew-2.1.0/include/GL
  ${CMAKE_BINARY_DIR}/ext/glew/lib
)
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
	set(GLEW_LIB_NAME glew)
else()
	set(GLEW_LIB_NAME glew32)
endif()
###########################################################################

set(ALL_PROJECT_LIBRARIES
    OpenGL::GL
	OpenGL::GLU
	glfw
	${GLEW_LIB_NAME})

#link_directories( 
#    ${CMAKE_BINARY_DIR}/ext/glfw/src
#	${CMAKE_BINARY_DIR}/lib
#	${CMAKE_BINARY_DIR}/bin)

# Include sub-projects.
add_subdirectory ("Minimal OpenGL Example")

SET(PROJECT_SOURCES "Minimal OpenGL Example/main.cpp")

add_executable( minimal_opengl_example ${OPTIONS} ${PROJECT_SOURCES})

target_link_libraries( minimal_opengl_example ${ALL_PROJECT_LIBRARIES})

if(WIN32)
	# post build command for copying dll files
	set (GLEW_DLL_DIR "${CMAKE_BINARY_DIR}/bin/")
	
	if(CMAKE_BUILD_TYPE STREQUAL "Debug")
		set(GLEW_DLL_NAME glew32d)
	else()
		set(GLEW_DLL_NAME glew32)
	endif()
	
	add_custom_command(TARGET minimal_opengl_example POST_BUILD
		COMMAND ${CMAKE_COMMAND} -E copy_if_different
			"${GLEW_DLL_DIR}/${GLEW_DLL_NAME}.dll"
			$<TARGET_FILE_DIR:minimal_opengl_example>)
endif()
