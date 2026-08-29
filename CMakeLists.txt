cmake_minimum_required(VERSION 3.16)
project(toshinden C CXX)

include(CheckIPOSupported)

# Set C standard
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(GBRECOMP_ENABLE_PERFORMANCE_COUNTERS "Compile generated/runtime performance counters" OFF)

set(GBRECOMP_GENERATED_COMPILE_JOBS "8" CACHE STRING "Maximum concurrent generated-source compiles under Ninja; 0 disables the pool")

# Migrate stale generated-project cache entries when the default profile changes.
set(GBRECOMP_PROFILE_CACHE_VERSION "2")
set(GBRECOMP_APPLIED_PROFILE_CACHE_VERSION "0" CACHE STRING "Internal generated-project profile version")
if(NOT GBRECOMP_APPLIED_PROFILE_CACHE_VERSION STREQUAL GBRECOMP_PROFILE_CACHE_VERSION)
    if(NOT DEFINED GBRECOMP_ENABLE_IPO)
        set(GBRECOMP_ENABLE_IPO OFF CACHE BOOL "Enable interprocedural optimization/LTO for non-Debug builds" FORCE)
    endif()
    if(NOT DEFINED GBRECOMP_ENABLE_STRIP)
        set(GBRECOMP_ENABLE_STRIP ON CACHE BOOL "Strip symbols from generated executables in non-Debug builds" FORCE)
    endif()
    if(NOT DEFINED GBRECOMP_GENERATED_OPT_LEVEL)
        set(GBRECOMP_GENERATED_OPT_LEVEL "1" CACHE STRING "Optimization level for the generated ROM source files" FORCE)
    endif()
    set(GBRECOMP_APPLIED_PROFILE_CACHE_VERSION "${GBRECOMP_PROFILE_CACHE_VERSION}" CACHE STRING "Internal generated-project profile version" FORCE)
endif()

# Default to a smaller, iteration-friendly profile for generated projects.
# Benchmark and release builds can still opt into Release/-O3/IPO explicitly.
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE MinSizeRel CACHE STRING "Build type" FORCE)
endif()
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        add_compile_options(-O0 -g)
    elseif(CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
        add_compile_options(-O2 -g)
    elseif(CMAKE_BUILD_TYPE STREQUAL "MinSizeRel")
        add_compile_options(-Os)
    else()
        add_compile_options(-O3)
    endif()
    add_compile_options(-ffunction-sections -fdata-sections)
endif()

# Self-contained runtime snapshot embedded by gbrecomp
set(GBRT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/runtime")

# Find SDL2
find_package(SDL2 REQUIRED)

# Create runtime library with PPU and platform support
add_library(gbrt STATIC
    ${GBRT_DIR}/src/gbrt.c
    ${GBRT_DIR}/src/gbrt_data_mod.c
    ${GBRT_DIR}/src/gbrt_hash.c
    ${GBRT_DIR}/src/gbrt_host_configuration.c
    ${GBRT_DIR}/src/gbrt_port.c
    ${GBRT_DIR}/src/gbrt_presentation.c
    ${GBRT_DIR}/src/gbrt_semantic.c
    ${GBRT_DIR}/src/differential.c
    ${GBRT_DIR}/src/ppu.c
    ${GBRT_DIR}/src/audio.c
    ${GBRT_DIR}/src/audio_stats.c
    ${GBRT_DIR}/src/interpreter.c
    ${GBRT_DIR}/src/platform_sdl.cpp
)

# Vendored Dear ImGui subset
target_sources(gbrt PRIVATE
    ${GBRT_DIR}/vendor/imgui/imgui.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_draw.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_tables.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_widgets.cpp
    ${GBRT_DIR}/vendor/imgui/backends/imgui_impl_sdl2.cpp
    ${GBRT_DIR}/vendor/imgui/backends/imgui_impl_sdlrenderer2.cpp
)
target_include_directories(gbrt PUBLIC 
    ${GBRT_DIR}/include
    ${GBRT_DIR}/vendor/imgui
)
target_link_libraries(gbrt PUBLIC SDL2::SDL2)
target_compile_definitions(gbrt PUBLIC GB_HAS_SDL2)

if(GBRECOMP_ENABLE_PERFORMANCE_COUNTERS)
    target_compile_definitions(gbrt PUBLIC GBRT_ENABLE_PERFORMANCE_COUNTERS)
endif()

# Main executable
add_executable(toshinden
    toshinden_main.c
    toshinden.c
    toshinden_dispatch_chunk_0.c
    toshinden_dispatch_chunk_1.c
    toshinden_dispatch_chunk_2.c
    toshinden_dispatch_chunk_3.c
    toshinden_dispatch_chunk_4.c
    toshinden_dispatch_chunk_5.c
    toshinden_dispatch_chunk_6.c
    toshinden_dispatch_chunk_7.c
    toshinden_dispatch_chunk_8.c
    toshinden_dispatch_chunk_9.c
    toshinden_dispatch_chunk_10.c
    toshinden_dispatch_chunk_11.c
    toshinden_dispatch_chunk_12.c
    toshinden_dispatch_chunk_13.c
    toshinden_dispatch_chunk_14.c
    toshinden_dispatch_chunk_15.c
    toshinden_dispatch_chunk_16.c
    toshinden_dispatch_chunk_17.c
    toshinden_dispatch_chunk_18.c
    toshinden_dispatch_chunk_19.c
    toshinden_dispatch_chunk_20.c
    toshinden_dispatch_chunk_21.c
    toshinden_dispatch_chunk_22.c
    toshinden_dispatch_chunk_23.c
    toshinden_dispatch_chunk_24.c
    toshinden_funcs_0.c
    toshinden_funcs_1.c
    toshinden_funcs_2.c
    toshinden_funcs_3.c
    toshinden_funcs_4.c
    toshinden_funcs_5.c
    toshinden_funcs_6.c
    toshinden_funcs_7.c
    toshinden_funcs_8.c
    toshinden_funcs_9.c
    toshinden_funcs_10.c
    toshinden_funcs_11.c
    toshinden_funcs_12.c
    toshinden_funcs_13.c
    toshinden_funcs_14.c
    toshinden_funcs_15.c
    toshinden_funcs_16.c
    toshinden_funcs_17.c
    toshinden_funcs_18.c
    toshinden_funcs_19.c
    toshinden_funcs_20.c
    toshinden_funcs_21.c
    toshinden_funcs_22.c
    toshinden_funcs_23.c
    toshinden_funcs_24.c
    toshinden_funcs_25.c
    toshinden_funcs_26.c
    toshinden_funcs_27.c
    toshinden_funcs_28.c
    toshinden_funcs_29.c
    toshinden_funcs_30.c
    toshinden_funcs_31.c
    toshinden_funcs_32.c
    toshinden_funcs_33.c
    toshinden_funcs_34.c
    toshinden_funcs_35.c
    toshinden_funcs_36.c
    toshinden_funcs_37.c
    toshinden_funcs_38.c
    toshinden_funcs_39.c
    toshinden_funcs_40.c
    toshinden_funcs_41.c
    toshinden_funcs_42.c
    toshinden_funcs_43.c
    toshinden_funcs_44.c
    toshinden_funcs_45.c
    toshinden_funcs_46.c
    toshinden_funcs_47.c
    toshinden_funcs_48.c
    toshinden_funcs_49.c
    toshinden_funcs_50.c
    toshinden_funcs_51.c
    toshinden_funcs_52.c
    toshinden_funcs_53.c
    toshinden_funcs_54.c
    toshinden_funcs_55.c
    toshinden_funcs_56.c
    toshinden_funcs_57.c
    toshinden_funcs_58.c
    toshinden_funcs_59.c
    toshinden_funcs_60.c
    toshinden_funcs_61.c
    toshinden_funcs_62.c
    toshinden_funcs_63.c
    toshinden_funcs_64.c
    toshinden_funcs_65.c
    toshinden_funcs_66.c
    toshinden_funcs_67.c
    toshinden_funcs_68.c
    toshinden_funcs_69.c
    toshinden_funcs_70.c
    toshinden_funcs_71.c
    toshinden_funcs_72.c
    toshinden_funcs_73.c
    toshinden_funcs_74.c
    toshinden_funcs_75.c
    toshinden_funcs_76.c
    toshinden_funcs_77.c
    toshinden_funcs_78.c
    toshinden_funcs_79.c
    toshinden_funcs_80.c
    toshinden_funcs_81.c
    toshinden_funcs_82.c
    toshinden_funcs_83.c
    toshinden_funcs_84.c
)

# Large generated translation units are memory-intensive. Keep runtime/UI
# compilation parallel while bounding only the generated executable target.
if(CMAKE_GENERATOR MATCHES "Ninja" AND GBRECOMP_GENERATED_COMPILE_JOBS MATCHES "^[1-9][0-9]*$")
    set_property(GLOBAL APPEND PROPERTY JOB_POOLS "gbrecomp_generated_compile_pool=${GBRECOMP_GENERATED_COMPILE_JOBS}")
    set_property(TARGET toshinden PROPERTY JOB_POOL_COMPILE gbrecomp_generated_compile_pool)
endif()

# Generated ROM code defaults to a smaller manual-testing profile.
# Raise the optimization level or enable IPO/LTO explicitly for benchmark/release builds.
set(GBRECOMP_GENERATED_OPT_LEVEL "1" CACHE STRING "Optimization level for the generated ROM source files")
set_property(CACHE GBRECOMP_GENERATED_OPT_LEVEL PROPERTY STRINGS 0 1 2 3)
option(GBRECOMP_ENABLE_IPO "Enable interprocedural optimization/LTO for non-Debug builds" OFF)
option(GBRECOMP_ENABLE_STRIP "Strip symbols from generated executables in non-Debug builds" ON)
set(GBRECOMP_GENERATED_SOURCES
    toshinden.c
    toshinden_dispatch_chunk_0.c
    toshinden_dispatch_chunk_1.c
    toshinden_dispatch_chunk_2.c
    toshinden_dispatch_chunk_3.c
    toshinden_dispatch_chunk_4.c
    toshinden_dispatch_chunk_5.c
    toshinden_dispatch_chunk_6.c
    toshinden_dispatch_chunk_7.c
    toshinden_dispatch_chunk_8.c
    toshinden_dispatch_chunk_9.c
    toshinden_dispatch_chunk_10.c
    toshinden_dispatch_chunk_11.c
    toshinden_dispatch_chunk_12.c
    toshinden_dispatch_chunk_13.c
    toshinden_dispatch_chunk_14.c
    toshinden_dispatch_chunk_15.c
    toshinden_dispatch_chunk_16.c
    toshinden_dispatch_chunk_17.c
    toshinden_dispatch_chunk_18.c
    toshinden_dispatch_chunk_19.c
    toshinden_dispatch_chunk_20.c
    toshinden_dispatch_chunk_21.c
    toshinden_dispatch_chunk_22.c
    toshinden_dispatch_chunk_23.c
    toshinden_dispatch_chunk_24.c
    toshinden_funcs_0.c
    toshinden_funcs_1.c
    toshinden_funcs_2.c
    toshinden_funcs_3.c
    toshinden_funcs_4.c
    toshinden_funcs_5.c
    toshinden_funcs_6.c
    toshinden_funcs_7.c
    toshinden_funcs_8.c
    toshinden_funcs_9.c
    toshinden_funcs_10.c
    toshinden_funcs_11.c
    toshinden_funcs_12.c
    toshinden_funcs_13.c
    toshinden_funcs_14.c
    toshinden_funcs_15.c
    toshinden_funcs_16.c
    toshinden_funcs_17.c
    toshinden_funcs_18.c
    toshinden_funcs_19.c
    toshinden_funcs_20.c
    toshinden_funcs_21.c
    toshinden_funcs_22.c
    toshinden_funcs_23.c
    toshinden_funcs_24.c
    toshinden_funcs_25.c
    toshinden_funcs_26.c
    toshinden_funcs_27.c
    toshinden_funcs_28.c
    toshinden_funcs_29.c
    toshinden_funcs_30.c
    toshinden_funcs_31.c
    toshinden_funcs_32.c
    toshinden_funcs_33.c
    toshinden_funcs_34.c
    toshinden_funcs_35.c
    toshinden_funcs_36.c
    toshinden_funcs_37.c
    toshinden_funcs_38.c
    toshinden_funcs_39.c
    toshinden_funcs_40.c
    toshinden_funcs_41.c
    toshinden_funcs_42.c
    toshinden_funcs_43.c
    toshinden_funcs_44.c
    toshinden_funcs_45.c
    toshinden_funcs_46.c
    toshinden_funcs_47.c
    toshinden_funcs_48.c
    toshinden_funcs_49.c
    toshinden_funcs_50.c
    toshinden_funcs_51.c
    toshinden_funcs_52.c
    toshinden_funcs_53.c
    toshinden_funcs_54.c
    toshinden_funcs_55.c
    toshinden_funcs_56.c
    toshinden_funcs_57.c
    toshinden_funcs_58.c
    toshinden_funcs_59.c
    toshinden_funcs_60.c
    toshinden_funcs_61.c
    toshinden_funcs_62.c
    toshinden_funcs_63.c
    toshinden_funcs_64.c
    toshinden_funcs_65.c
    toshinden_funcs_66.c
    toshinden_funcs_67.c
    toshinden_funcs_68.c
    toshinden_funcs_69.c
    toshinden_funcs_70.c
    toshinden_funcs_71.c
    toshinden_funcs_72.c
    toshinden_funcs_73.c
    toshinden_funcs_74.c
    toshinden_funcs_75.c
    toshinden_funcs_76.c
    toshinden_funcs_77.c
    toshinden_funcs_78.c
    toshinden_funcs_79.c
    toshinden_funcs_80.c
    toshinden_funcs_81.c
    toshinden_funcs_82.c
    toshinden_funcs_83.c
    toshinden_funcs_84.c
)
if(NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(MSVC)
        if(GBRECOMP_GENERATED_OPT_LEVEL STREQUAL "0")
            set(GBRECOMP_GENERATED_OPT_FLAG "/Od")
        elseif(GBRECOMP_GENERATED_OPT_LEVEL STREQUAL "1")
            set(GBRECOMP_GENERATED_OPT_FLAG "/O1")
        else()
            set(GBRECOMP_GENERATED_OPT_FLAG "/O2")
        endif()
        set_source_files_properties(${GBRECOMP_GENERATED_SOURCES} PROPERTIES COMPILE_OPTIONS "${GBRECOMP_GENERATED_OPT_FLAG}")
    else()
        set_source_files_properties(${GBRECOMP_GENERATED_SOURCES} PROPERTIES COMPILE_OPTIONS "-O${GBRECOMP_GENERATED_OPT_LEVEL}")
    endif()
endif()

if(NOT MSVC AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(APPLE)
        target_link_options(gbrt PRIVATE -Wl,-dead_strip)
        target_link_options(toshinden PRIVATE -Wl,-dead_strip)
    else()
        target_link_options(gbrt PRIVATE -Wl,--gc-sections)
        target_link_options(toshinden PRIVATE -Wl,--gc-sections)
    endif()
endif()

if(GBRECOMP_ENABLE_IPO AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    check_ipo_supported(RESULT GBRECOMP_IPO_SUPPORTED OUTPUT GBRECOMP_IPO_ERROR)
    if(GBRECOMP_IPO_SUPPORTED)
        set_property(TARGET gbrt PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
        set_property(TARGET toshinden PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
    else()
        message(STATUS "GB Recompiled: IPO/LTO not enabled (${GBRECOMP_IPO_ERROR})")
    endif()
endif()

target_link_libraries(toshinden gbrt)
if(TARGET SDL2::SDL2main)
    target_link_libraries(toshinden SDL2::SDL2main)
elseif(TARGET SDL2main)
    target_link_libraries(toshinden SDL2main)
endif()
if(GBRECOMP_ENABLE_STRIP AND NOT MSVC AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(APPLE)
        add_custom_command(TARGET toshinden POST_BUILD
            COMMAND ${CMAKE_STRIP} -Sx $<TARGET_FILE:toshinden>
            VERBATIM)
    elseif(CMAKE_STRIP)
        add_custom_command(TARGET toshinden POST_BUILD
            COMMAND ${CMAKE_STRIP} --strip-debug $<TARGET_FILE:toshinden>
            VERBATIM)
    endif()
endif()
