cmake_minimum_required(VERSION 3.31)

project(GlewGlfwTemplate)

include(CPM.cmake)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(USE_GLU ON)

CPMAddPackage(
	NAME              	glew
	GIT_TAG				origin/master
    GITHUB_REPOSITORY 	Perlmint/glew-cmake
	OPTIONS
		"glew-cmake_BUILD_SHARED OFF"
		"glew-cmake_BUILD_STATIC ON"
)

CPMAddPackage(
    NAME              glfw
    VERSION           3.4
    GIT_TAG           3.4
    GITHUB_REPOSITORY glfw/glfw
    OPTIONS
		"BUILD_SHARED_LIBS OFF"
		"GLFW_BUILD_EXAMPLES OFF"
		"GLFW_BUILD_TESTS OFF"
		"GLFW_BUILD_DOCS OFF"
		"GLFW_INSTALL OFF"
		"GLFW_VULKAN_STATIC OFF"
)

find_package(OpenGL REQUIRED)

# set the output directory for built objects.
# This makes sure that the dynamic library goes into the build directory automatically.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/$<CONFIGURATION>")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/$<CONFIGURATION>")

file(GLOB SRC
    "lib/*.cpp"
    "lib/*.h"
)

add_library(WindowGL STATIC ${SRC})
target_include_directories(WindowGL PUBLIC ${CMAKE_CURRENT_LIST_DIR}/lib)
target_include_directories(WindowGL PUBLIC "${GLFW_SOURCE_DIR}/deps")
target_include_directories(WindowGL PUBLIC "${glew_SOURCE_DIR}/include")
target_link_libraries(WindowGL PUBLIC OpenGL::GL)
target_link_libraries(WindowGL PUBLIC OpenGL::GLU)
target_link_libraries(WindowGL PUBLIC libglew_static)
target_link_libraries(WindowGL PUBLIC glfw)

add_executable(Example main.cpp)
target_link_libraries(Example WindowGL)

# List all variables and targets (just for info) 

get_cmake_property(_variableNames VARIABLES)
list (SORT _variableNames)
foreach (_variableName ${_variableNames})
    message(STATUS "${_variableName}=${${_variableName}}")
endforeach()

function(get_all_targets var)
    set(targets)
    get_all_targets_recursive(targets ${CMAKE_CURRENT_SOURCE_DIR})
    set(${var} ${targets} PARENT_SCOPE)
endfunction()

macro(get_all_targets_recursive targets dir)
    get_property(subdirectories DIRECTORY ${dir} PROPERTY SUBDIRECTORIES)
    foreach(subdir ${subdirectories})
        get_all_targets_recursive(${targets} ${subdir})
    endforeach()

    get_property(current_targets DIRECTORY ${dir} PROPERTY BUILDSYSTEM_TARGETS)
    list(APPEND ${targets} ${current_targets})
endmacro()

get_all_targets(all_targets)
message("All targets: ${all_targets}")
