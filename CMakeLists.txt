cmake_minimum_required(VERSION 3.28.0)

# Allow MSVC_RUNTIME_LIBRARY
cmake_policy(SET CMP0091 NEW)

option(DETOURS_DEBUG "Build Detours in Debug mode?" OFF)

# No c++ Standard specified by Microsoft, so ... whatever!
set(DETOURS_ROOT "${CMAKE_CURRENT_LIST_DIR}")
project(detours
  LANGUAGES
    CXX)

# detours is built as a static library
add_library(detours STATIC)

# Static MSVC Runtime
set_property(TARGET detours PROPERTY
  MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

# Detours source files :)
set(DETOURS_SRC_ROOT "${DETOURS_ROOT}/src")
set(DETOURS_SRC "${DETOURS_SRC_ROOT}/creatwth.cpp"
                "${DETOURS_SRC_ROOT}/detours.cpp"
                "${DETOURS_SRC_ROOT}/disasm.cpp"
                "${DETOURS_SRC_ROOT}/disolarm.cpp"
                "${DETOURS_SRC_ROOT}/disolarm64.cpp"
                "${DETOURS_SRC_ROOT}/disolia64.cpp"
                "${DETOURS_SRC_ROOT}/disolx64.cpp"
                "${DETOURS_SRC_ROOT}/disolx86.cpp"
                "${DETOURS_SRC_ROOT}/image.cpp"
                "${DETOURS_SRC_ROOT}/modules.cpp"
                "${DETOURS_SRC_ROOT}/uimports.cpp")
target_sources(detours PRIVATE ${DETOURS_SRC})

# Public include directory...
target_include_directories(detours PUBLIC "${DETOURS_ROOT}/src")

# Compile options
target_compile_options(detours PRIVATE /nologo /W4 /WX /we4777 /we4800 /Zi /MT /Gy /Gm- /Zl /Od)
# Compile definitions
target_compile_definitions(detours PRIVATE WIN32_LEAN_AND_MEAN _WIN32_WINNT=0x501)

if(DETOURS_DEBUG)
  target_compile_definitions(detours PRIVATE DETOUR_DEBUG=1)
else()
  target_compile_definitions(detours PRIVATE DETOUR_DEBUG=0)
endif()
