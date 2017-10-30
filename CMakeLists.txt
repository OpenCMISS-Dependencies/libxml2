cmake_minimum_required(VERSION 3.0)
project(libxml2 LANGUAGES C CXX VERSION 2.9.6)

option(BUILD_TESTS "${PROJECT_NAME} - Build tests" ON)
set(PACKAGE_CONFIG_DIR "lib/cmake" CACHE STRING "Directory for package config files (relative to CMAKE_INSTALL_PREFIX)")
option(ZLIB_WITH_ZLIB "Build in zlib support" ON)
option(ZLIB_WITH_TRIO "Build with trio support" OFF)
option(BUILD_SHARED_LIBS "Build shared library." ON)

set(WITH_ZLIB ${ZLIB_WITH_ZLIB})
if (WITH_ZLIB)
    find_package(ZLIB ${ZLIB_VERSION})
	if (NOT ZLIB_FOUND)
		find_package(ZLIB ${ZLIB_VERSION} CONFIG)
	endif ()
	if (NOT ZLIB_FOUND)
		message(FATAL_ERROR "zlib was NOT found!")
	endif ()
endif()

if(NOT BUILD_SHARED_LIBS)
	set(LIBRARY_INSTALL_TYPE ARCHIVE )
else()
	set(LIBRARY_INSTALL_TYPE LIBRARY )
	if(WIN32 )
		set(LIBRARY_INSTALL_TYPE RUNTIME )
	endif(WIN32 )
endif()

set(XML2_SRCS
    src/DOCBparser.c
    src/HTMLparser.c
    src/HTMLtree.c
    src/SAX.c
    src/SAX2.c
    src/buf.c
    src/c14n.c
    src/catalog.c
    src/chvalid.c
    src/debugXML.c
    src/dict.c
    src/encoding.c
    src/entities.c
    src/error.c
    src/globals.c
    src/hash.c
    src/legacy.c
    src/list.c
    src/nanoftp.c
    src/nanohttp.c
    src/parser.c
    src/parserInternals.c
    src/pattern.c
    src/relaxng.c
    src/schematron.c
    src/threads.c
    src/tree.c
    src/uri.c
    src/valid.c
    src/xinclude.c
    src/xlink.c
    src/xmlIO.c
    src/xmlmemory.c
    src/xmlmodule.c
    src/xmlreader.c
    src/xmlregexp.c
    src/xmlsave.c
    src/xmlschemas.c
    src/xmlschemastypes.c
    src/xmlstring.c
    src/xmlunicode.c
    src/xmlwriter.c
    src/xpath.c
    src/xpointer.c
    src/xzlib.c)

if (WITH_TRIO)
    list(APPEND XML2_SRCS
        src/trio.c
        #src/trionan.c // # included in xpath.c
        src/triostr.c)
endif ()

set(PRIVATE_HDRS
    src/buf.h
    src/elfgcchack.h
    src/enc.h
    src/libxml.h
    src/save.h
    src/timsort.h
    src/trio.h
    src/triodef.h
    src/trionan.h
    src/triop.h
    src/triostr.h
    src/xzlib.h)

set(PUBLIC_HDRS
	include/libxml/c14n.h
	include/libxml/catalog.h
	include/libxml/chvalid.h
	include/libxml/debugXML.h
	include/libxml/dict.h
	include/libxml/DOCBparser.h
	include/libxml/encoding.h
	include/libxml/entities.h
	include/libxml/globals.h
	include/libxml/hash.h
	include/libxml/HTMLparser.h
	include/libxml/HTMLtree.h
	include/libxml/list.h
	include/libxml/nanoftp.h
	include/libxml/nanohttp.h
	include/libxml/parser.h
	include/libxml/parserInternals.h
	include/libxml/pattern.h
	include/libxml/relaxng.h
	include/libxml/SAX.h
	include/libxml/SAX2.h
	include/libxml/schemasInternals.h
	include/libxml/schematron.h
	include/libxml/threads.h
	include/libxml/tree.h
	include/libxml/uri.h
	include/libxml/valid.h
	include/libxml/xinclude.h
	include/libxml/xlink.h
	include/libxml/xmlautomata.h
	include/libxml/xmlerror.h
	include/libxml/xmlexports.h
	include/libxml/xmlIO.h
	include/libxml/xmlmemory.h
	include/libxml/xmlmodule.h
	include/libxml/xmlreader.h
	include/libxml/xmlregexp.h
	include/libxml/xmlsave.h
	include/libxml/xmlschemas.h
	include/libxml/xmlschemastypes.h
	include/libxml/xmlstring.h
	include/libxml/xmlunicode.h
	include/libxml/xmlwriter.h
	include/libxml/xpath.h
	include/libxml/xpathInternals.h
	include/libxml/xpointer.h )
set(PUBLIC_HDRS ${PUBLIC_HDRS} ${CMAKE_CURRENT_BINARY_DIR}/include/libxml/xmlversion.h )

if(MACOSX )
	list(APPEND PRIVATE_HDRS
		macos/src/config-mac.h
		macos/src/xmltestprefix.h
		macos/src/xmltestprefix2.h )
endif()

if(WIN32)
	list(APPEND PRIVATE_HDRS
		include/win32config.h
		include/wsockcompat.h)
endif()

set(HAVE_PTHREAD_H ${CMAKE_HAVE_PTHREAD_H} )
set(HAVE_UNISTD_H ${CMAKE_HAVE_UNISTD_H} )
set(HAVE_LIMITS_H ${CMAKE_HAVE_LIMITS_H} )
set(HAVE_SYS_TYPES_H ${CMAKE_HAVE_SYS_PRCTL_H} )
set(ICONV_CONST const )

include(CheckIncludeFile)
set(CHECK_INC_FILES alloca ansidecl arpa/inet arpa/nameser
ctype dirent dl dlfcn errno fcntl float fp_class ieeefp
inttypes inttypes malloc math memory nan ndir netdb netinet/in
poll resolv signal stdarg stdint stdlib string strings
sys/dir sys/mman sys/ndir sys/select sys/socket sys/stat
sys/time sys/timeb sys/types time zlib)
foreach(incfile ${CHECK_INC_FILES})
    string(REPLACE "/" "_" VARPART ${incfile})
    string(TOUPPER ${VARPART} VARPART)
    set(VARNAME HAVE_${VARPART}_H)
    CHECK_INCLUDE_FILE(${incfile}.h ${VARNAME})
endforeach()
include(CheckIncludeFiles)
CHECK_INCLUDE_FILES("stdint.h;stddef.h;inttypes.h;stdlib.h;strings.h;string.h;float.h" StandardHeadersExist)

include(CheckSymbolExists)
set(CHECK_FUNCTIONS finite fpclass fprintf fp_class
    ftime gettimeofday isnand localtime printf signal
    snprintf sprintf sscanf stat strdup strerror
    strftime strndup vfprintf vsnprintf vsprintf 
    _stat)
set(HEADERS "stdio.h;stdint.h;stddef.h;inttypes.h;stdlib.h;string.h;float.h;time.h")
if (HAVE_SYS_TYPES_H)
	list(APPEND HEADERS "sys/types.h")
endif ()
if (HAVE_SYS_STAT_H)
	list(APPEND HEADERS "sys/stat.h")
endif ()
if (HAVE_SYS_TIMEB_H)
	list(APPEND HEADERS "sys/timeb.h")
endif ()
foreach(funcname ${CHECK_FUNCTIONS})
    string(TOUPPER ${funcname} FUNCNAME_UPPER)
    CHECK_SYMBOL_EXISTS(${funcname} "${HEADERS}" HAVE_${FUNCNAME_UPPER})
endforeach()

set(ADDRINFO_HEADERS stdio.h)
if (WIN32)
	list(APPEND ADDRINFO_HEADERS winsock2.h ws2tcpip.h)
	set(CMAKE_REQUIRED_LIBRARIES ws2_32)
endif ()
CHECK_SYMBOL_EXISTS(getaddrinfo "${ADDRINFO_HEADERS}" HAVE_GETADDRINFO)
unset(CMAKE_REQUIRED_LIBRARIES)

if(NOT (HAVE_PRINTF OR HAVE_SPRINTF OR HAVE_FPRINTF OR
   HAVE_SNPRINTF OR HAVE_VFPRINTF OR HAVE_VSPRINTF OR HAVE_SSCANF))
  message(STATUS "Not all required print functions found. Building with TRIO.")
  set(WITH_TRIO 1)
  # FYI trio source 'trionan.c' is #included in xpath.c
  list(APPEND XML2_SRCS src/trio.c src/triostr.c)
else()
  # Needed in CMake-configured files (xmlversion.h)
  set(WITH_TRIO 0)  
endif()

include(CheckSymbolExists)
if(StandardHeadersExist)
	CHECK_SYMBOL_EXISTS( memchr string.h memchrExists )
	if(memchrExists)
		CHECK_SYMBOL_EXISTS(free stdlib.h STDC_HEADERS)
	endif()
endif()
CHECK_SYMBOL_EXISTS(DIR "sys/dir.h" HAVE_SYS_DIR_H) 
CHECK_SYMBOL_EXISTS(isinf math.h HAVE_ISINF)
CHECK_SYMBOL_EXISTS(isnan math.h HAVE_ISNAN)
CHECK_SYMBOL_EXISTS(va_copy stdarg.h HAVE_VA_COPY)
if (HAVE_SYS_SOCKET_H)
    CHECK_SYMBOL_EXISTS(AF_INET6 "sys/types.h;sys/socket.h" SUPPORT_IP6)
endif()

include(CheckLibraryExists)
CHECK_LIBRARY_EXISTS(m exp "" HAVE_LIBM)
if(HAVE_DLFCN_H)
    CHECK_LIBRARY_EXISTS(dl dlopen "" HAVE_DLOPEN)
endif()
set(INET_LIBRARIES bsd socket inet)
if(NOT HAVE_GETADDRINFO)
    foreach(INET_LIB ${INET_LIBRARIES})
        CHECK_LIBRARY_EXISTS(${INET_LIB} "getaddrinfo" "" HAVE_INET_LIB_${INET_LIB})
        if (HAVE_INET_LIB_${INET_LIB})
            set(HAVE_GETADDRINFO YES)
        endif()
    endforeach()
endif()
if (HAVE_SYS_SOCKET_H)
    include(CheckStructHasMember)
    CHECK_STRUCT_HAS_MEMBER(sockaddr_storage ss_family "sys/types.h;sys/socket.h" HAVE_SS_FAMILY)
    if(NOT HAVE_SS_FAMILY)
        CHECK_STRUCT_HAS_MEMBER(sockaddr_storage __ss_family "sys/types.h;sys/socket.h" HAVE_BROKEN_SS_FAMILY)
    	if(HAVE_BROKEN_SS_FAMILY)
    		set(ss_family __ss_family)
    	endif()
    endif()
endif()

find_package(Threads)
if(CMAKE_USE_PTHREADS_INIT)
  set(HAVE_LIBPTHREAD YES)
  ADD_DEFINITIONS( -D_REENTRANT )
  set(WITH_THREADS 1 )
else()
  if(CMAKE_USE_WIN32_THREADS_INIT)
    ADD_DEFINITIONS( -D_REENTRANT -DHAVE_WIN32_THREADS )
    set(WITH_THREADS 1 )
  else()
    set(WITH_THREADS 0 )
  endif()
endif()

set(WITH_TREE 1 )
set(WITH_OUTPUT 1 )
set(WITH_PUSH 1 )
set(WITH_READER 1 )
set(WITH_PATTERN 1 )
set(WITH_WRITER 1 )
set(WITH_SAX1 1 )
set(WITH_FTP 1 )
set(WITH_HTTP 1 )
set(WITH_VALID 1 )
set(WITH_HTML 1 )
set(WITH_LEGACY 0 )
set(WITH_CATALOG 1 )
set(WITH_DOCB 0 )
set(WITH_XPATH 0 )
set(WITH_XPTR 0 )
set(WITH_XINCLUDE 0 )
set(WITH_ICONV 0 )
set(WITH_ISO8859X 1 )
set(WITH_DEBUG 0 )
set(WITH_MEM_DEBUG 0 )
set(WITH_RUN_DEBUG 0 )
set(WITH_REGEXPS 1 )
set(WITH_SCHEMAS 1 )
set(WITH_MODULES 1 )
set(MODULE_EXTENSION ${CMAKE_SHARED_LIBRARY_SUFFIX})
set(WITH_C14N ${WITH_XPATH} )
set(WITH_SCHEMATRON ${WITH_XPATH} )

if (WIN32)
	configure_file(
		${CMAKE_CURRENT_SOURCE_DIR}/include/win32config.h
		${CMAKE_CURRENT_BINARY_DIR}/include/xml2_config.h COPYONLY)
	configure_file(
		${CMAKE_CURRENT_SOURCE_DIR}/include/wsockcompat.h
		${CMAKE_CURRENT_BINARY_DIR}/include/wsockcompat.h COPYONLY)
else ()
	configure_file(
		${CMAKE_CURRENT_SOURCE_DIR}/xml2_config.h.cmake
		${CMAKE_CURRENT_BINARY_DIR}/include/xml2_config.h)
endif ()

# Best to use 'Header Files' and 'Source Files' because any ungrouped files will be placed there by default
source_group( "Header Files\\libxml" FILES ${PUBLIC_HDRS} ) 
source_group( "Header Files\\" FILES ${PRIVATE_HDRS} ) 
source_group( "Source Files\\" FILES ${XML2_SRCS} ) 

set(LIBXML_VERSION_NUMBER ${PROJECT_VERSION_MAJOR}0${PROJECT_VERSION_MINOR}0${PROJECT_VERSION_PATCH}0)
set(LIBXML_VERSION_EXTRA OpenCMISS)
configure_file( 
	${CMAKE_CURRENT_SOURCE_DIR}/include/libxml/xmlversion.h.cmake
	${CMAKE_CURRENT_BINARY_DIR}/include/libxml/xmlversion.h @ONLY )

set(CMAKE_DEBUG_POSTFIX "d" )
set(CMAKE_MFC_FLAG 0 )

add_library(xml2 ${XML2_SRCS} ${PUBLIC_HDRS} ${PRIVATE_HDRS} )
target_include_directories(xml2 PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>)
target_include_directories(xml2 PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)
target_compile_definitions(xml2 PUBLIC LIBXML_XPATH_ENABLED)
if (WIN32)
    target_compile_definitions(xml2 PUBLIC _WINDOWS)
    # Fix for nanoftp/nanohttp
    target_link_libraries(xml2 PUBLIC ws2_32)
    if(MSVC)
    	target_compile_definitions(xml2 PRIVATE _CRT_SECURE_NO_WARNINGS WINVER=0x0600)
    endif()
endif()
if(NOT BUILD_SHARED_LIBS)
	target_compile_definitions(xml2 PUBLIC LIBXML_STATIC)
endif()
if(WITH_THREADS)
    target_compile_definitions(xml2 PRIVATE _REENTRANT)
    if(CMAKE_USE_WIN32_THREADS_INIT)
        target_compile_definitions(xml2 PRIVATE HAVE_WIN32_THREADS)
    endif()
endif()

if(WITH_ZLIB)
    if (TARGET zlib)
        target_link_libraries(xml2 PUBLIC zlib)
    else ()
        target_link_libraries(xml2 PUBLIC ${ZLIB_LIBRARIES})
        target_include_directories(xml2 PUBLIC ${ZLIB_INCLUDE_DIR})
    endif ()
    target_compile_definitions(xml2 PRIVATE HAVE_ZLIB_H)
endif()

# We use dl functionality - other will need it too! (Its most likely libdl.so, so no symbols are pulled in)
if (HAVE_DLOPEN)
    target_link_libraries(xml2 PUBLIC dl)
endif()

add_executable(xmllint src/xmllint.c)
target_link_libraries(xmllint PRIVATE xml2)
add_executable(xmlcatalog src/xmlcatalog.c)
target_link_libraries(xmlcatalog PRIVATE xml2)

install(TARGETS xml2 xmllint xmlcatalog
    EXPORT libxml2-config
	DESTINATION lib
	RUNTIME DESTINATION bin
	INCLUDES DESTINATION include/libxml2)
install(FILES ${PUBLIC_HDRS}
	DESTINATION include/libxml2/libxml)
install(EXPORT libxml2-config DESTINATION ${PACKAGE_CONFIG_DIR})   
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-version.cmake
    COMPATIBILITY AnyNewerVersion)
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-dependencies.cmake 
    "include(CMakeFindDependencyMacro)\r\n"
    	"set(${PROJECT_NAME}_IMPORT_PREFIX \${_IMPORT_PREFIX})\r\n")
if (WITH_ZLIB)
    file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-dependencies.cmake "find_dependency(ZLIB ${ZLIB_VERSION})\r\n")
endif()
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-dependencies.cmake "set(_IMPORT_PREFIX \${${PROJECT_NAME}_IMPORT_PREFIX})")
install(FILES 
    ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-version.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/libxml2-config-dependencies.cmake
    DESTINATION ${PACKAGE_CONFIG_DIR})

set(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION "${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}")

set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/Copyright")
set(CPACK_NSIS_MODIFY_PATH ON)

include(CPack)

