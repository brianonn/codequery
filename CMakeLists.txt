cmake_minimum_required (VERSION 2.8.12)
project(CodeQuery)


option(BUILD_QT5 OFF)

set( CMAKE_CXX_STANDARD 11 )
set( CMAKE_CXX_STANDARD_REQUIRED ON )

if( ${CMAKE_MAJOR_VERSION} LESS 3.1
AND ${CMAKE_CXX_STANDARD_REQUIRED}
AND ( NOT MSVC ) )
  set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -std=c++${CMAKE_CXX_STANDARD}" )
endif()

set( CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "" FORCE )
if( NOT CMAKE_BUILD_TYPE )
  set( CMAKE_BUILD_TYPE "Release" CACHE STRING
      "Choose the type of build, options are: ${CMAKE_CONFIGURATION_TYPES}" FORCE )
endif( NOT CMAKE_BUILD_TYPE )
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

if(MSVC)
  add_definitions(/D _CRT_SECURE_NO_WARNINGS)
  set( CMAKE_CXX_FLAGS_DEBUG  "/ZI /MDd" )
  set( CMAKE_CXX_FLAGS_RELEASE  "/Zi /O2 /MD" )
  set( CMAKE_EXE_LINKER_FLAGS "/nologo /INCREMENTAL")
  set( CMAKE_EXE_LINKER_FLAGS_DEBUG "/DEBUG /OPT:NOREF /SUBSYSTEM:CONSOLE")
  set( CMAKE_EXE_LINKER_FLAGS_RELEASE "/MAP /OPT:REF /OPT:ICF /SUBSYSTEM:WINDOWS")
else(MSVC)
  set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -Wno-unused-result" )
  set( CMAKE_CXX_FLAGS_DEBUG  "-g" )
  set( CMAKE_CXX_FLAGS_RELEASE  "-O2" )
endif(MSVC)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmakefind/")

set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/${CMAKE_BUILD_TYPE} )

add_subdirectory( "querylib" )
add_subdirectory( "makedb" )
add_subdirectory( "cli" )
add_subdirectory( "scintilla/qt/ScintillaEdit" )
add_subdirectory( "showgraph" )
add_subdirectory( "gui" )



if (UNIX)

if (BUILD_QT5)

set( CPACK_PACKAGE_NAME "codequery-qt5" )
set( CPACK_DEBIAN_PACKAGE_DEPENDS "qtbase5-dev (>= 5.4.0) , libsqlite3-0 (>= 3.7.0) " )

else (BUILD_QT5)

set( CPACK_PACKAGE_NAME "codequery-qt4" )
set( CPACK_DEBIAN_PACKAGE_DEPENDS "libqtcore4 (>= 4.8.0), libqtgui4 (>= 4.8.0) , libqt4-xml (>= 4.8.0) , libsqlite3-0 (>= 3.7.0) " )

endif (BUILD_QT5)


  if( CMAKE_SIZEOF_VOID_P EQUAL 8 )
    set( CPACK_DEBIAN_PACKAGE_ARCHITECTURE "amd64" )
  else( CMAKE_SIZEOF_VOID_P EQUAL 8 )
    set( CPACK_DEBIAN_PACKAGE_ARCHITECTURE "i386" )
  endif( CMAKE_SIZEOF_VOID_P EQUAL 8 )

set( CPACK_GENERATOR "DEB;TGZ")
set( CPACK_PACKAGE_VERSION  "0.19.0" )
set( CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-Linux-${CPACK_DEBIAN_PACKAGE_ARCHITECTURE}" ) 
set( CPACK_PACKAGE_DESCRIPTION_SUMMARY "Code understanding and searching tool")
set( CPACK_PACKAGE_DESCRIPTION "Code understanding and searching tool")
set( CPACK_PACKAGE_VENDOR "ruben2020")
set( CPACK_DEBIAN_PACKAGE_RECOMMENDS "cscope , exuberant-ctags" )
set( CPACK_DEBIAN_PACKAGE_SECTION "devel" )
set( CPACK_DEBIAN_PACKAGE_MAINTAINER "ruben2020 ruben2020-git@users.sourceforge.net")
set( CPACK_DEBIAN_PACKAGE_HOMEPAGE "http://ruben2020.github.io/codequery/")

include(CPack)

endif(UNIX)

