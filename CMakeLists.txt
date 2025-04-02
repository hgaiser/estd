cmake_minimum_required(VERSION 4.0)
project(estd)
set(PROJECT_VERSION 0.6.5)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
add_compile_options(-Wall -Wextra -Wpedantic)
add_link_options(LINKER:--as-needed)

option(WERROR "Turn compiler warnings into errors." OFF)
if (WERROR)
	add_compile_options(-Werror)
endif()

add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(${PROJECT_NAME} INTERFACE
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)

option(BUILD_TESTS "Build tests" ON)
if (BUILD_TESTS)
	add_subdirectory(test)
endif()

include(GNUInstallDirs)
set(CMAKE_INSTALL_CMAKEDIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}" CACHE PATH "Installation directory for CMake files.")

include(CMakePackageConfigHelpers)
write_basic_package_version_file("${PROJECT_NAME}-config-version.cmake" COMPATIBILITY SameMajorVersion)

install(TARGETS ${PROJECT_NAME} EXPORT targets)
install(EXPORT targets DESTINATION "${CMAKE_INSTALL_CMAKEDIR}")
install(FILES
	"${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}-config.cmake"
	"${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake"
	DESTINATION "${CMAKE_INSTALL_CMAKEDIR}"
)

install(DIRECTORY "include/${PROJECT_NAME}"
	DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
	COMPONENT development
)
