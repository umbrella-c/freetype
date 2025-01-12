cmake_minimum_required (VERSION 3.12)

project (freetype C)

option (FREETYPE_BUILD_STATIC "Build the static version of libfreetype" OFF)
option (FREETYPE_BUILD_SHARED "Build the shared library version of libfreetype" ON)

find_package(ZLIB REQUIRED)
find_package(PNG REQUIRED)

set (FT_INCLUDES ${ZLIB_INCLUDE_DIRS} ${PNG_INCLUDE_DIRS})
set (FT_LIBS ${ZLIB_LIBRARIES} ${PNG_LIBRARIES})
set (SRCS "")

macro (add_sources)
    file (RELATIVE_PATH _relPath "${PROJECT_SOURCE_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}")
    foreach (_src ${ARGN})
        if (_relPath)
            list (APPEND SRCS "${_relPath}/${_src}")
        else()
            list (APPEND SRCS "${_src}")
        endif()
    endforeach()
endmacro()

add_sources(
    src/autofit/autofit.c
    src/base/ftbase.c
    src/base/ftbbox.c
    src/base/ftbdf.c
    src/base/ftbitmap.c
    src/base/ftcid.c
    src/base/ftfntfmt.c
    src/base/ftfstype.c
    src/base/ftgasp.c
    src/base/ftglyph.c
    src/base/ftgxval.c
    src/base/ftinit.c
    src/base/ftlcdfil.c
    src/base/ftmm.c
    src/base/ftotval.c
    src/base/ftpatent.c
    src/base/ftpfr.c
    src/base/ftstroke.c
    src/base/ftsynth.c
    src/base/ftsystem.c
    src/base/fttype1.c
    src/base/ftwinfnt.c
    src/base/ftdebug.c
    src/bdf/bdf.c
    src/bzip2/ftbzip2.c
    src/cache/ftcache.c
    src/cff/cff.c
    src/cid/type1cid.c
    src/gzip/ftgzip.c
    src/lzw/ftlzw.c
    src/pcf/pcf.c
    src/pfr/pfr.c
    src/psaux/psaux.c
    src/pshinter/pshinter.c
    src/psnames/psnames.c
    src/raster/raster.c
    src/sfnt/sfnt.c
    src/smooth/smooth.c
    src/truetype/truetype.c
    src/type1/type1.c
    src/type42/type42.c
    src/winfonts/winfnt.c
)

add_definitions (-DFT2_BUILD_LIBRARY)

if (FREETYPE_BUILD_STATIC)
    add_library(freetype_static STATIC ${SHARED_SRCS})
    target_include_directories(freetype_static PRIVATE ${FT_INCLUDES})
    target_include_directories(freetype_static PUBLIC include)
    
    install(TARGETS freetype_static
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
    )
endif ()

if (FREETYPE_BUILD_SHARED)
    set (SHARED_SRCS ${SRCS})
    if (MOLLENOS)
        set (SHARED_SRCS ${SHARED_SRCS} main.c)
    endif ()

    add_library(freetype SHARED ${SHARED_SRCS})
    target_include_directories(freetype PRIVATE ${FT_INCLUDES})
    target_include_directories(freetype PUBLIC include)
    target_compile_definitions(freetype PUBLIC -DFT_SHARED_LIBRARY)
    target_link_libraries(freetype ${FT_LIBS})

    install(TARGETS freetype
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
    )
endif ()

install(DIRECTORY include/ DESTINATION include)
