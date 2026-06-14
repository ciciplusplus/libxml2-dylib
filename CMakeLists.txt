cmake_minimum_required(VERSION 3.20)

project(libxml2 VERSION 2.7.8 LANGUAGES C)

set(LIB_NAME "libxml2.2.dylib")
set(INSTALL_PATH /usr/lib)
set(VERSION 10.8.0)
set(COMPAT_VERSION 10.0.0)

set(IMAGE_BASE 0x3311e000)

if(NOT EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/libxml2")
    message(
        FATAL_ERROR
        "libxml2 source is missing! Run 'git submodule update --init' to sync"
    )
endif()

set(libxml2_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/libxml2")

# Copy pregenerated files
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/pregenerated/config.h
    ${libxml2_SOURCE_DIR}/libxml2/config.h
    COPYONLY
)
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/pregenerated/xmlversion.h
    ${libxml2_SOURCE_DIR}/libxml2/include/libxml/xmlversion.h
    COPYONLY
)

set(LIBXML2_SOURCES
    ${libxml2_SOURCE_DIR}/libxml2/SAX.c
    ${libxml2_SOURCE_DIR}/libxml2/entities.c
    ${libxml2_SOURCE_DIR}/libxml2/encoding.c
    ${libxml2_SOURCE_DIR}/libxml2/error.c
    ${libxml2_SOURCE_DIR}/libxml2/parserInternals.c
    ${libxml2_SOURCE_DIR}/libxml2/parser.c
    ${libxml2_SOURCE_DIR}/libxml2/tree.c
    ${libxml2_SOURCE_DIR}/libxml2/hash.c
    ${libxml2_SOURCE_DIR}/libxml2/list.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlIO.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlmemory.c
    ${libxml2_SOURCE_DIR}/libxml2/uri.c
    ${libxml2_SOURCE_DIR}/libxml2/valid.c
    ${libxml2_SOURCE_DIR}/libxml2/xlink.c
    ${libxml2_SOURCE_DIR}/libxml2/HTMLparser.c
    ${libxml2_SOURCE_DIR}/libxml2/HTMLtree.c
    ${libxml2_SOURCE_DIR}/libxml2/debugXML.c
    ${libxml2_SOURCE_DIR}/libxml2/xpath.c
    ${libxml2_SOURCE_DIR}/libxml2/xpointer.c
    ${libxml2_SOURCE_DIR}/libxml2/xinclude.c
    ${libxml2_SOURCE_DIR}/libxml2/nanohttp.c
    ${libxml2_SOURCE_DIR}/libxml2/nanoftp.c
    ${libxml2_SOURCE_DIR}/libxml2/DOCBparser.c
    ${libxml2_SOURCE_DIR}/libxml2/catalog.c
    ${libxml2_SOURCE_DIR}/libxml2/globals.c
    ${libxml2_SOURCE_DIR}/libxml2/threads.c
    ${libxml2_SOURCE_DIR}/libxml2/c14n.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlstring.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlregexp.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlschemas.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlschemastypes.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlunicode.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlreader.c
    ${libxml2_SOURCE_DIR}/libxml2/relaxng.c
    ${libxml2_SOURCE_DIR}/libxml2/dict.c
    ${libxml2_SOURCE_DIR}/libxml2/SAX2.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlwriter.c
    ${libxml2_SOURCE_DIR}/libxml2/legacy.c
    ${libxml2_SOURCE_DIR}/libxml2/chvalid.c
    ${libxml2_SOURCE_DIR}/libxml2/pattern.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlsave.c
    ${libxml2_SOURCE_DIR}/libxml2/xmlmodule.c
    ${libxml2_SOURCE_DIR}/libxml2/schematron.c
)

add_library(${LIB_NAME} SHARED ${LIBXML2_SOURCES})

target_include_directories(${LIB_NAME}
    PUBLIC 
        ${libxml2_SOURCE_DIR}/libxml2/include
        ${CMAKE_CURRENT_SOURCE_DIR}/zlib-src/zlib
)

# # for LIBXML_THREAD_ENABLED
# add_definitions(-D_POSIX_C_SOURCE=200809L)

set_target_properties(${LIB_NAME} PROPERTIES
    OUTPUT_NAME "xml2.${VERSION}"
    VERSION ${VERSION}
    MACHO_COMPATIBILITY_VERSION ${COMPAT_VERSION}
    INSTALL_NAME_DIR "${INSTALL_PATH}"
    OSX_ARCHITECTURES "armv7"
    POSITION_INDEPENDENT_CODE OFF
    C_STANDARD 99
)

target_compile_options(${LIB_NAME} 
    PRIVATE
        -Wno-deprecated-non-prototype
        -Wno-expansion-to-defined
        -fno-stack-protector
        -Os
)

target_compile_definitions(${LIB_NAME} 
    PRIVATE
        __DARWIN_ONLY_64_BIT_INO_T=1
        USE_MMAP
        NDEBUG
)

target_link_options(${LIB_NAME}
    PRIVATE
        -exported_symbols_list
        ${CMAKE_CURRENT_SOURCE_DIR}/libxml2_cleaned.exp
        -dead_strip
        -dead_strip_dylibs
        -dylib
        -single_module
        -prebind
        -dynamic
        -twolevel_namespace
        -twolevel_namespace_hints
        -image_base
        ${IMAGE_BASE}
        -install_name
        ${INSTALL_PATH}/${LIB_NAME}
)

target_link_libraries(${LIB_NAME}
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/libz.1.2.3.dylib
)