cmake_minimum_required(VERSION 3.10)

project(GLFW)

set(CMAKE_C_STANDARD 99)

option(GLFW_BUILD_EXAMPLES "Build the GLFW example programs" OFF)
option(GLFW_BUILD_TESTS "Build the GLFW test programs" OFF)
option(GLFW_BUILD_DOCS "Build the GLFW documentation" OFF)
option(GLFW_INSTALL "Generate installation target" OFF)

if (WIN32)
    set(SYSTEM_SOURCES
        src/win32_init.c
        src/win32_joystick.c
        src/win32_module.c
        src/win32_monitor.c
        src/win32_time.c
        src/win32_thread.c
        src/win32_window.c
        src/wgl_context.c
        src/egl_context.c
        src/osmesa_context.c
    )
    add_definitions(-D_GLFW_WIN32 -D_CRT_SECURE_NO_WARNINGS)
elseif (UNIX)
    set(SYSTEM_SOURCES
        src/x11_init.c
        src/x11_monitor.c
        src/x11_window.c
        src/xkb_unicode.c
        src/posix_time.c
        src/posix_thread.c
        src/glx_context.c
        src/egl_context.c
        src/osmesa_context.c
        src/linux_joystick.c
    )
    add_definitions(-D_GLFW_X11)
endif()

set(SOURCES
    include/GLFW/glfw3.h
    include/GLFW/glfw3native.h
    #src/glfw_config.h
    src/context.c
    src/init.c
    src/input.c
    src/monitor.c
    src/null_init.c
    src/null_joystick.c
    src/null_monitor.c
    src/null_window.c
    src/platform.c
    src/vulkan.c
    src/window.c
    ${SYSTEM_SOURCES}
)

add_library(GLFW STATIC ${SOURCES})

target_include_directories(GLFW PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

set_target_properties(GLFW PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin/${CMAKE_BUILD_TYPE}-${CMAKE_SYSTEM_NAME}-${ARCHITECTURE}/GLFW
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin/${CMAKE_BUILD_TYPE}-${CMAKE_SYSTEM_NAME}-${ARCHITECTURE}/GLFW
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin/${CMAKE_BUILD_TYPE}-${CMAKE_SYSTEM_NAME}-${ARCHITECTURE}/GLFW
)

if (CMAKE_SYSTEM_NAME MATCHES "Linux")
    set_target_properties(GLFW PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()

if (MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE $<$<CONFIG:Debug>:/MTd /Zi /RTC1 /Od> $<$<CONFIG:Release>:/MT /O2>)
else()
    target_compile_options(${PROJECT_NAME} PRIVATE $<$<CONFIG:Debug>:-g> $<$<CONFIG:Release>:-O3>)
endif()
# if (CMAKE_BUILD_TYPE MATCHES Debug)
#     target_compile_options(GLFW PRIVATE -D_DEBUG)
# elseif (CMAKE_BUILD_TYPE MATCHES Release)
#     target_compile_options(GLFW PRIVATE -O2)
# endif()
