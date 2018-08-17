if (${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    message(FATAL_ERROR "Prevented in-tree built. Please create a build directory outside of the source code and call cmake from there")
endif ()

project(sfml-imgui)
cmake_minimum_required(VERSION 3.9)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

##! Package
find_package(SFML CONFIG REQUIRED graphics)
find_package(imgui CONFIG REQUIRED)
find_package(OpenGL REQUIRED)

##! Library
add_library(${PROJECT_NAME} SHARED sfml-imgui/imgui-SFML.cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE imgui::imgui sfml-graphics OpenGL::GL)

target_include_directories(${PROJECT_NAME} PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/sfml-imgui>
        $<INSTALL_INTERFACE:include/sfml-imgui>  # <prefix>/include/mylib
        )

include(GNUInstallDirs)

include(CMakePackageConfigHelpers)

install(TARGETS
        ${PROJECT_NAME}
        EXPORT sfml-imgui-targets
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        )

install(EXPORT ${PROJECT_NAME}-targets
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
        )

configure_package_config_file(
        "${PROJECT_SOURCE_DIR}/cmake/${PROJECT_NAME}-config.cmake.in"
        "${PROJECT_BINARY_DIR}/${PROJECT_NAME}-config.cmake"
        INSTALL_DESTINATION lib/cmake/${PROJECT_NAME}
)

install(FILES
        "${PROJECT_BINARY_DIR}/${PROJECT_NAME}-config.cmake"
        DESTINATION lib/cmake/${PROJECT_NAME})

install(DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}/sfml-imgui
        DESTINATION
        ${CMAKE_INSTALL_INCLUDEDIR}/sfml-imgui
        FILES_MATCHING PATTERN "*.h*"
        )