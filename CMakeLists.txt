cmake_minimum_required(VERSION 4.0)
project(NotSoSoundy C)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Find GTK4 with pkg-config
find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK4 REQUIRED gtk4)
pkg_check_modules(CURL REQUIRED libcurl)
# Find PortAudio - try pkg-config first, fall back to manual search for MSYS2 compatibility
pkg_check_modules(PORTAUDIO portaudio)
if(NOT PORTAUDIO_FOUND)
    find_library(PORTAUDIO_LIBRARY NAMES portaudio portaudio_x64 portaudio_x86)
    find_path(PORTAUDIO_INCLUDE_DIR NAMES portaudio.h)
    if(PORTAUDIO_LIBRARY AND PORTAUDIO_INCLUDE_DIR)
        set(PORTAUDIO_LIBRARIES ${PORTAUDIO_LIBRARY})
        set(PORTAUDIO_INCLUDE_DIRS ${PORTAUDIO_INCLUDE_DIR})
        set(PORTAUDIO_LIBRARY_DIRS "")
        message(STATUS "PortAudio found manually: ${PORTAUDIO_LIBRARY}")
    else()
        message(FATAL_ERROR "PortAudio not found. Please install portaudio development libraries.")
    endif()
endif()

include_directories(${GTK4_INCLUDE_DIRS})
include_directories(${CURL_INCLUDE_DIRS})
include_directories(${PORTAUDIO_INCLUDE_DIRS})

link_directories(${GTK4_LIBRARY_DIRS})
link_directories(${CURL_LIBRARY_DIRS})
link_directories(${PORTAUDIO_LIBRARY_DIRS})


add_executable(NotSoSoundy main.c
        playSound.c
        playSound.h
)

target_link_libraries(${PROJECT_NAME}
        ${GTK4_LIBRARIES}
        ${CURL_LIBRARIES}
        ${PORTAUDIO_LIBRARIES}
)
target_compile_options(${PROJECT_NAME}
        PRIVATE ${GTK4_CFLAGS_OTHER})