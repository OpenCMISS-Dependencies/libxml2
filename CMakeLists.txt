
 # ***** BEGIN LICENSE BLOCK *****
 # Version: MPL 1.1/GPL 2.0/LGPL 2.1
 #
 # The contents of this file are subject to the Mozilla Public License Version
 # 1.1 (the "License"); you may not use this file except in compliance with
 # the License. You may obtain a copy of the License at
 # http://www.mozilla.org/MPL/
 #
 # Software distributed under the License is distributed on an "AS IS" basis,
 # WITHOUT WARRANTY OF ANY KIND, either express or implied. See the License
 # for the specific language governing rights and limitations under the
 # License.
 #
 # The Original Code is cmgui
 #
 # The Initial Developer of the Original Code is
 # Auckland Uniservices Ltd, Auckland, New Zealand.
 # Portions created by the Initial Developer are Copyright (C) 2005
 # the Initial Developer. All Rights Reserved.
 #
 # Contributor(s): 
 #
 # Alternatively, the contents of this file may be used under the terms of
 # either the GNU General Public License Version 2 or later (the "GPL"), or
 # the GNU Lesser General Public License Version 2.1 or later (the "LGPL"),
 # in which case the provisions of the GPL or the LGPL are applicable instead
 # of those above. If you wish to allow use of your version of this file only
 # under the terms of either the GPL or the LGPL, and not to allow others to
 # use your version of this file under the terms of the MPL, indicate your
 # decision by deleting the provisions above and replace them with the notice
 # and other provisions required by the GPL or the LGPL. If you do not delete
 # the provisions above, a recipient may use your version of this file under
 # the terms of any one of the MPL, the GPL or the LGPL.
 #
 # ***** END LICENSE BLOCK ***** */

CMAKE_MINIMUM_REQUIRED( VERSION 3.0 )

PROJECT( libxml2 LANGUAGES C CXX VERSION 2.7.6)

option(BUILD_TESTS "${PROJECT_NAME} - Build tests" ON)
option(PACKAGE_CONFIG_DIR "Directory for package config files (relative to CMAKE_INSTALL_PREFIX)" "lib/cmake")
option(WITH_ZLIB "Build in zlib support" TRUE )

if (WITH_ZLIB)
    find_package(ZLIB REQUIRED)
endif()

SET( LIBRARY_TARGET_NAME xml2 )
STRING( TOUPPER ${LIBRARY_TARGET_NAME} UPPERCASE_LIBRARY_TARGET_NAME )
SET( XML2_MAJOR_VERSION "2" )
SET( XML2_MINOR_VERSION "7" )
SET( XML2_PATCH_VERSION "6" )

MACRO( OPTION_WITH_DEFAULT OPTION_NAME OPTION_STRING OPTION_DEFAULT )
	IF( NOT DEFINED ${OPTION_NAME} )
		SET( ${OPTION_NAME} ${OPTION_DEFAULT} )
	ENDIF( NOT DEFINED ${OPTION_NAME} )

	OPTION( ${OPTION_NAME} "${OPTION_STRING}" ${${OPTION_NAME}} )
ENDMACRO( OPTION_WITH_DEFAULT OPTION_NAME OPTION_STRING OPTION_DEFAULT )

MACRO( CACHE_VAR_WITH_DEFAULT OPTION_NAME OPTION_DEFAULT OPTION_TYPE OPTION_STRING )
	IF( NOT DEFINED ${OPTION_NAME} )
		SET( ${OPTION_NAME} ${OPTION_DEFAULT} )
	ENDIF( NOT DEFINED ${OPTION_NAME} )

	SET( ${OPTION_NAME} "${OPTION_DEFAULT}" CACHE ${OPTION_TYPE} "${OPTION_STRING}" )
ENDMACRO( CACHE_VAR_WITH_DEFAULT OPTION_NAME OPTION_DEFAULT OPTION_TYPE OPTION_STRING )

# Set up library options
# Generic library options
CACHE_VAR_WITH_DEFAULT( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_TYPE "Release" STRING 
	"Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel" )
SET( CMAKE_BUILD_TYPE ${${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_TYPE} CACHE
	INTERNAL "Internalise this variable and manipulate with
	${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_TYPE" FORCE )
CACHE_VAR_WITH_DEFAULT( ${UPPERCASE_LIBRARY_TARGET_NAME}_INSTALL_PREFIX
	"${CMAKE_INSTALL_PREFIX}" PATH 
	"Install path prefix, prepended onto install directories." )
SET( CMAKE_INSTALL_PREFIX ${${UPPERCASE_LIBRARY_TARGET_NAME}_INSTALL_PREFIX}
	CACHE INTERNAL "Internalise this variable and manipulate with
	${UPPERCASE_LIBRARY_TARGET_NAME}_INSTALL_PREFIX" FORCE )
OPTION_WITH_DEFAULT( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB "Build static zlib" TRUE )
OPTION_WITH_DEFAULT( ${UPPERCASE_LIBRARY_TARGET_NAME}_INSTALL_CONFIG "Install a config file for the library" FALSE )


IF( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB )
	SET( LIBRARY_BUILD_TYPE STATIC )
	SET( LIBRARY_INSTALL_TYPE ARCHIVE )
ELSE( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB )
	SET( LIBRARY_BUILD_TYPE SHARED )
	SET( LIBRARY_INSTALL_TYPE LIBRARY )
	IF( WIN32 )
		SET( LIBRARY_INSTALL_TYPE RUNTIME )
	ENDIF( WIN32 )
ENDIF( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB )

IF( MSVC )
	ADD_DEFINITIONS( -D_CRT_SECURE_NO_WARNINGS -DWINVER=0x0600 )
	IF( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB )
		ADD_DEFINITIONS( -DLIBXML_STATIC )
	ENDIF( ${UPPERCASE_LIBRARY_TARGET_NAME}_BUILD_STATIC_LIB )
ENDIF( MSVC )

SET( XML2_SRCS
	c14n.c
	catalog.c
	chvalid.c
	debugXML.c
	dict.c
	DOCBparser.c
	encoding.c
	entities.c
	error.c
	globals.c
	hash.c
	HTMLparser.c
	HTMLtree.c	
	legacy.c
	list.c
	nanoftp.c
	nanohttp.c
	parser.c
	parserInternals.c
	pattern.c
	relaxng.c
	SAX.c
	SAX2.c
	schematron.c
	threads.c
	tree.c
	uri.c
	valid.c
	xinclude.c
	xlink.c
	xmlIO.c
	xmlmemory.c
	xmlmodule.c
	xmlreader.c
	xmlregexp.c
	xmlsave.c
	xmlschemas.c
	xmlschemastypes.c
	xmlstring.c
	xmlunicode.c
	xmlwriter.c
	xpath.c
	xpointer.c )

SET( PRIVATE_HDRS
	acconfig.h
	elfgcchack.h
	libxml.h
	trio.h
	triodef.h
	trionan.h
	triop.h
	triostr.h )

SET( PUBLIC_HDRS
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
SET( PUBLIC_HDRS ${PUBLIC_HDRS} ${CMAKE_CURRENT_BINARY_DIR}/include/libxml/xmlversion.h )

IF( MACOSX )
	SET( PRIVATE_HDRS ${PRIVATE_HDRS}
		macos/src/config-mac.h
		macos/src/xmltestprefix.h
		macos/src/xmltestprefix2.h )
ENDIF( MACOSX )

IF( WIN32 )
	SET( PRIVATE_HDRS ${PRIVATE_HDRS}
		include/win32config.h
		include/wsockcompat.h )
ENDIF( WIN32 )

# Best to use 'Header Files' and 'Source Files' because any ungrouped files will be placed there by default
SOURCE_GROUP( "Header Files\\libxml" FILES ${PUBLIC_HDRS} ) 
SOURCE_GROUP( "Header Files\\" FILES ${PRIVATE_HDRS} ) 
SOURCE_GROUP( "Source Files\\" FILES ${XML2_SRCS} ) 

SET( VERSION ${XML2_MAJOR_VERSION}.${XML2_MINOR_VERSION}.${XML2_PATCH_VERSION} )
SET( LIBXML_VERSION_NUMBER ${XML2_MAJOR_VERSION}0${XML2_MINOR_VERSION}0${XML2_PATCH_VERSION} )
SET( LIBXML_VERSION_EXTRA
	-GITv${XML2_MAJOR_VERSION}.${XML2_MINOR_VERSION}.${XML2_PATCH_VERSION} )

SET(PACKAGE "libxml2")
SET(PACKAGE_STRING "")
SET(PACKAGE_NAME "")
SET(PACKAGE_TARNAME "")
SET(PACKAGE_VERSION "")
SET(PACKAGE_BUGREPORT "")
SET(PROTOTYPES 1)


IF( M_LIBRARY )
  SET( HAVE_LIBM TRUE )
ENDIF( M_LIBRARY )

SET( HAVE_PTHREAD_H ${CMAKE_HAVE_PTHREAD_H} )
SET( HAVE_UNISTD_H ${CMAKE_HAVE_UNISTD_H} )
SET( HAVE_LIMITS_H ${CMAKE_HAVE_LIMITS_H} )
SET( HAVE_SYS_TYPES_H ${CMAKE_HAVE_SYS_PRCTL_H} )
SET( ICONV_CONST const )

INCLUDE( CheckIncludeFile )
CHECK_INCLUDE_FILE( alloca.h HAVE_ALLOCA_H )
CHECK_INCLUDE_FILE( ansidecl.h HAVE_ANSIDECL_H )
CHECK_INCLUDE_FILE( arpa/inet.h HAVE_ARPA_INET_H )
CHECK_INCLUDE_FILE( arpa/nameser.h HAVE_ARPA_NAMESER_H )
CHECK_INCLUDE_FILE( ctype.h HAVE_CTYPE_H )
CHECK_INCLUDE_FILE( dirent.h HAVE_DIRENT_H )
CHECK_INCLUDE_FILE( dl.h HAVE_DL_H )
CHECK_INCLUDE_FILE( dlfcn.h HAVE_DLFCN_H )
CHECK_INCLUDE_FILE( errno.h HAVE_ERRNO_H )
CHECK_INCLUDE_FILE( fcntl.h HAVE_FCNTL_H )
CHECK_INCLUDE_FILE( float.h HAVE_FLOAT_H )
CHECK_INCLUDE_FILE( fp_class.h HAVE_FP_CLASS_H )
CHECK_INCLUDE_FILE( ieeefp.h HAVE_IEEEFP_H )
CHECK_INCLUDE_FILE( inttypes.h HAVE_INTTYPES_H )
CHECK_INCLUDE_FILE( inttypes.h.h HAVE_INTTYPES_H_H )
CHECK_INCLUDE_FILE( malloc.h HAVE_MALLOC_H )
CHECK_INCLUDE_FILE( math.h HAVE_MATH_H )
CHECK_INCLUDE_FILE( memory.h HAVE_MEMORY_H )
CHECK_INCLUDE_FILE( nan.h HAVE_NAN_H )
CHECK_INCLUDE_FILE( ndir.h HAVE_NDIR_H )
CHECK_INCLUDE_FILE( netdb.h HAVE_NETDB_H )
CHECK_INCLUDE_FILE( netinet/in.h HAVE_NETINET_IN_H )
CHECK_INCLUDE_FILE( poll.h HAVE_POLL_H )
CHECK_INCLUDE_FILE( resolv.h HAVE_RESOLV_H )
CHECK_INCLUDE_FILE( signal.h HAVE_SIGNAL_H )
CHECK_INCLUDE_FILE( stdarg.h HAVE_STDARG_H )
CHECK_INCLUDE_FILE( stdint.h HAVE_STDINT_H )
CHECK_INCLUDE_FILE( stdlib.h HAVE_STDLIB_H )
CHECK_INCLUDE_FILE( string.h HAVE_STRING_H )
CHECK_INCLUDE_FILE( strings.h HAVE_STRINGS_H )
CHECK_INCLUDE_FILE( sys/dir.h HAVE_SYS_DIR_H )
CHECK_INCLUDE_FILE( sys/mman.h HAVE_SYS_MMAN_H )
CHECK_INCLUDE_FILE( sys/ndir.h HAVE_SYS_NDIR_H )
CHECK_INCLUDE_FILE( sys/select.h HAVE_SYS_SELECT_H )
CHECK_INCLUDE_FILE( sys/socket.h HAVE_SYS_SOCKET_H )
CHECK_INCLUDE_FILE( sys/stat.h HAVE_SYS_STAT_H )
CHECK_INCLUDE_FILE( sys/time.h HAVE_SYS_TIME_H )
CHECK_INCLUDE_FILE( sys/timeb.h HAVE_SYS_TIMEB_H )
CHECK_INCLUDE_FILE( time.h HAVE_TIME_H )
CHECK_INCLUDE_FILE( zlib.h HAVE_ZLIB_H )
INCLUDE(CheckFunctionExists)
CHECK_FUNCTION_EXISTS( class HAVE_CLASS )
CHECK_FUNCTION_EXISTS( finite HAVE_FINITE )
CHECK_FUNCTION_EXISTS( fpclass HAVE_FPCLASS )
CHECK_FUNCTION_EXISTS( fprintf HAVE_FPRINTF )
CHECK_FUNCTION_EXISTS( fp_class HAVE_FP_CLASS )
CHECK_FUNCTION_EXISTS( ftime HAVE_FTIME )
CHECK_FUNCTION_EXISTS( gettimeofday HAVE_GETTIMEOFDAY )
CHECK_FUNCTION_EXISTS( isnand HAVE_ISNAND )
CHECK_FUNCTION_EXISTS( localtime HAVE_LOCALTIME )
CHECK_FUNCTION_EXISTS( printf HAVE_PRINTF )
CHECK_FUNCTION_EXISTS( signal HAVE_SIGNAL )
CHECK_FUNCTION_EXISTS( snprintf HAVE_SNPRINTF )
CHECK_FUNCTION_EXISTS( sprintf HAVE_SPRINTF )
CHECK_FUNCTION_EXISTS( sscanf HAVE_SSCANF )
CHECK_FUNCTION_EXISTS( stat HAVE_STAT )
CHECK_FUNCTION_EXISTS( strdup HAVE_STRDUP )
CHECK_FUNCTION_EXISTS( strerror HAVE_STRERROR )
CHECK_FUNCTION_EXISTS( strftime HAVE_STRFTIME )
CHECK_FUNCTION_EXISTS( strndup HAVE_STRNDUP )
CHECK_FUNCTION_EXISTS( vfprintf HAVE_VFPRINTF )
CHECK_FUNCTION_EXISTS( vsnprintf HAVE_VSNPRINTF )
CHECK_FUNCTION_EXISTS( vsprintf HAVE_VSPRINTF )
CHECK_FUNCTION_EXISTS( _stat HAVE__STAT )
CHECK_FUNCTION_EXISTS( getaddrinfo HAVE_GETADDRINFO )
INCLUDE( CheckIncludeFiles )
CHECK_INCLUDE_FILES( "dlfcn.h;stdint.h;stddef.h;inttypes.h;stdlib.h;strings.h;string.h;float.h" StandardHeadersExist )
INCLUDE( CheckSymbolExists )
IF( StandardHeadersExist )
	CHECK_SYMBOL_EXISTS( memchr string.h memchrExists )
	IF( memchrExists )
		CHECK_SYMBOL_EXISTS( free stdlib.h STDC_HEADERS )
	ENDIF( memchrExists )
ENDIF( StandardHeadersExist )
CHECK_SYMBOL_EXISTS( DIR "sys/dir.h" HAVE_SYS_DIR_H ) 
CHECK_SYMBOL_EXISTS( isinf math.h HAVE_ISINF )
CHECK_SYMBOL_EXISTS( isnan math.h HAVE_ISNAN )
INCLUDE (CheckLibraryExists)
SET(INET_LIBRARIES "bsd socket inet")
IF(NOT HAVE_GETADDRINFO)
  CHECK_LIBRARY_EXISTS(${INET_LIBRARIES} "getaddrinfo" "" HAVE_GETADDRINFO)
ENDIF(NOT HAVE_GETADDRINFO)
INCLUDE(CheckCSourceCompiles)
IF( HAVE_DLFCN_H )
	FIND_LIBRARY( DL_LIBRARY dl )
	SET( CMAKE_REQUIRED_LIBRARIES ${DL_LIBRARY} )
	CHECK_C_SOURCE_COMPILES(
		"
		#include <dlfcn.h>
	
		int main(int argc, char* argv[])
		{
			dlopen(0, 0);
			return 0;
		}
		"
		HAVE_DLOPEN )
ENDIF( HAVE_DLFCN_H )

CHECK_C_SOURCE_COMPILES(
	"
	#include <stdarg.h>
	va_list ap1, ap2;

	int main(int argc, char* argv[])
	{
		va_copy(ap1, ap2);
		return 0;
	}
	"
	HAVE_VA_COPY )

CHECK_C_SOURCE_COMPILES(
	"
	#include <sys/types.h>
	#include <sys/socket.h>
	int main(int argc, char* argv[])
	{
		struct sockaddr_storage ss;
		socket(AF_INET6, SOCK_STREAM, 0);
		return 0;
	}
	"
	SUPPORT_IP6 )

CHECK_C_SOURCE_COMPILES(
	"
	#include <sys/types.h>
	#include <sys/socket.h>
	int main(int argc, char* argv[])
	{
		struct sockaddr_storage ss;
		ss.ss_family = 0;
		return 0;
	}
	"
	HAVE_SS_FAMILY )

IF(NOT HAVE_SS_FAMILY)
	CHECK_C_SOURCE_COMPILES(
		"
		#include <sys/types.h>
		#include <sys/socket.h>
		int main(int argc, char* argv[])
		{
			struct sockaddr_storage ss;
			ss.__ss_family = 0;
			return 0;
		}
		"
		HAVE_BROKEN_SS_FAMILY )
	IF(HAVE_BROKEN_SS_FAMILY)
		SET(ss_family __ss_family)
	ENDIF(HAVE_BROKEN_SS_FAMILY)
ENDIF(NOT HAVE_SS_FAMILY)

IF(NOT HAVE_PRINTF AND
   NOT HAVE_SPRINTF AND
   NOT HAVE_FPRINTF AND
   NOT HAVE_SNPRINTF AND
   NOT HAVE_VFPRINTF AND
   NOT HAVE_VSPRINTF AND
   NOT HAVE_PRINTF AND
   NOT HAVE_SSCANF)
  SET(WITH_TRIO 1)
ELSE(NOT HAVE_PRINTF AND
     NOT HAVE_SPRINTF AND
     NOT HAVE_FPRINTF AND
     NOT HAVE_SNPRINTF AND
     NOT HAVE_VFPRINTF AND
     NOT HAVE_VSPRINTF AND
     NOT HAVE_PRINTF AND
     NOT HAVE_SSCANF)
  SET(WITH_TRIO 0)
ENDIF(NOT HAVE_PRINTF AND
      NOT HAVE_SPRINTF AND
      NOT HAVE_FPRINTF AND
      NOT HAVE_SNPRINTF AND
      NOT HAVE_VFPRINTF AND
      NOT HAVE_VSPRINTF AND
      NOT HAVE_PRINTF AND
      NOT HAVE_SSCANF)

INCLUDE(FindThreads)
IF( CMAKE_USE_PTHREADS_INIT )

  SET( HAVE_LIBPTHREAD ${CMAKE_USE_PTHREADS_INIT} )
  ADD_DEFINITIONS( -D_REENTRANT )
  SET( WITH_THREADS 1 )

ELSE( CMAKE_USE_PTHREADS_INIT )

  IF( CMAKE_USE_WIN32_THREADS_INIT )
    #SET( HAVE_WIN32_THREADS ${CMAKE_USE_WIN32_THREADS_INIT} )
    ADD_DEFINITIONS( -D_REENTRANT -DHAVE_WIN32_THREADS )
    SET( WITH_THREADS 1 )
  ELSE( CMAKE_USE_WIN32_THREADS_INIT )
    SET( WITH_THREADS 0 )
  ENDIF( CMAKE_USE_WIN32_THREADS_INIT )

ENDIF( CMAKE_USE_PTHREADS_INIT )

SET( WITH_TREE 1 )
SET( WITH_OUTPUT 1 )
SET( WITH_PUSH 1 )
SET( WITH_READER 1 )
SET( WITH_PATTERN 1 )
SET( WITH_WRITER 1 )
SET( WITH_SAX1 1 )
SET( WITH_FTP 1 )
SET( WITH_HTTP 1 )
SET( WITH_VALID 1 )
SET( WITH_HTML 1 )
SET( WITH_LEGACY 0 )
SET( WITH_CATALOG 1 )
SET( WITH_DOCB 0 )
SET( WITH_XPATH 0 )
SET( WITH_XPTR 0 )
SET( WITH_XINCLUDE 0 )
SET( WITH_ICONV 0 )
SET( WITH_ISO8859X 1 )
SET( WITH_DEBUG 0 )
SET( WITH_MEM_DEBUG 0 )
SET( WITH_RUN_DEBUG 0 )
SET( WITH_REGEXPS 1 )
SET( WITH_SCHEMAS 1 )
SET( WITH_MODULES 1 )
SET( MODULE_EXTENSION ${CMAKE_SHARED_LIBRARY_SUFFIX} )
IF( ZLIB_FOUND )
	SET( WITH_ZLIB 1 )
ELSE( ZLIB_FOUND )
	SET( WITH_ZLIB 0 )
ENDIF( ZLIB_FOUND )
SET( WITH_C14N ${WITH_XPATH} )
SET( WITH_SCHEMATRON ${WITH_XPATH} )

# Don't need this already a #define to include win32config.h when on windows
#IF(WIN32)
#	CONFIGURE_FILE(
#		${CMAKE_CURRENT_SOURCE_DIR}/include/win32config.h
#		${CMAKE_CURRENT_BINARY_DIR}/include/xml2_config.h
#		COPYONLY )
#ELSE(WIN32)
IF( NOT WIN32 )
	CONFIGURE_FILE(
		${CMAKE_CURRENT_SOURCE_DIR}/xml2_config.h.cmake
		${CMAKE_CURRENT_BINARY_DIR}/include/xml2_config.h )
ENDIF( NOT WIN32 )

CONFIGURE_FILE( 
	${CMAKE_CURRENT_SOURCE_DIR}/include/libxml/xmlversion.h.cmake
	${CMAKE_CURRENT_BINARY_DIR}/include/libxml/xmlversion.h @ONLY )
INCLUDE_DIRECTORIES( ${CMAKE_CURRENT_BINARY_DIR}/include )
INCLUDE_DIRECTORIES( include )


SET( CMAKE_DEBUG_POSTFIX "d" )
SET( CMAKE_MFC_FLAG 0 )

IF( ${CMAKE_SYSTEM_NAME} STREQUAL "Linux" AND ${CMAKE_SYSTEM_PROCESSOR} STREQUAL "x86_64" )
	SET_SOURCE_FILES_PROPERTIES(${XML2_SRCS} 
		PROPERTIES COMPILE_FLAGS "-fPIC" )
ENDIF( ${CMAKE_SYSTEM_NAME} STREQUAL "Linux" AND ${CMAKE_SYSTEM_PROCESSOR} STREQUAL "x86_64" )

ADD_LIBRARY( ${LIBRARY_TARGET_NAME} ${LIBRARY_BUILD_TYPE} ${XML2_SRCS} ${PUBLIC_HDRS} ${PRIVATE_HDRS} )
# Add the necessary flag to tell that XPATH is enabled
target_compile_definitions(${LIBRARY_TARGET_NAME} PUBLIC LIBXML_XPATH_ENABLED)
if (WIN32)
    target_compile_definitions(${LIBRARY_TARGET_NAME} PUBLIC _WINDOWS)
endif()

IF( ZLIB_FOUND )
	TARGET_LINK_LIBRARIES( ${LIBRARY_TARGET_NAME} zlib)
	target_compile_definitions(${LIBRARY_TARGET_NAME} PRIVATE HAVE_ZLIB_H)
ENDIF()

INSTALL( TARGETS ${LIBRARY_TARGET_NAME}
    EXPORT libxml2-config
	DESTINATION lib
	INCLUDES DESTINATION include/libxml2 )
INSTALL( FILES ${PUBLIC_HDRS}
	DESTINATION include/libxml2/libxml )
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


