cmake_minimum_required ( VERSION 3.30 )
project ( luasql VERSION 2.6.0
                 LANGUAGES C )

set(CMAKE_RC_FLAGS "/nologo ${CMAKE_RC_FLAGS}")
set(CMAKE_C_FLAGS "/nologo ${CMAKE_C_FLAGS}")

set ( LUASQL_DIR "src" CACHE PATH "Source Directory of 'lunarmodules/luasql' module" )
set ( ENV{LUA_DIR} "externals/lua" )
set ( SQLITE_HOME "externals/sqlite" CACHE PATH "Where to search sqlite3 sources." )
if ( CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT )
  set_property ( CACHE CMAKE_INSTALL_PREFIX PROPERTY VALUE ${PROJECT_SOURCE_DIR} )
endif ()

message( STATUS "CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE}")
message( STATUS "CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}")
message( STATUS "LUA_DIR $ENV{LUA_DIR}")
message( STATUS "LUASQL_DIR ${LUASQL_DIR}")

find_package( Lua REQUIRED )
message( STATUS "Lua Version ${LUA_VERSION_STRING}")
message( STATUS "Lua Includes ${LUA_INCLUDE_DIR}")
message( STATUS "Lua Library ${LUA_LIBRARY}")
message( STATUS "Lua Libraries ${LUA_LIBRARIES}")

set ( SRC_CORE
        src/luasql.c src/ls_sqlite3.c )
set ( SRC_DEF src/sqlite3.def )

list( TRANSFORM SRC_CORE PREPEND "${LUASQL_DIR}/" )
list( TRANSFORM SRC_DEF PREPEND "${LUASQL_DIR}/" )

add_library ( luasql MODULE ${SRC_CORE} )
target_include_directories ( luasql PRIVATE ${LUA_INCLUDE_DIR} ${SQLITE_HOME}/include )
# target_compile_definitions (luasql PRIVATE )
target_link_options ( luasql PRIVATE ${CMAKE_LINK_DEF_FILE_FLAG}${PROJECT_SOURCE_DIR}/${SRC_DEF} )
target_link_libraries ( luasql ${LUA_LIBRARIES} 
        ${PROJECT_SOURCE_DIR}/${SQLITE_HOME}/lib/winsqlite3.lib )
set_target_properties ( luasql PROPERTIES OUTPUT_NAME "sqlite3" )

install ( TARGETS luasql LIBRARY DESTINATION "bin/luasql" )
install ( FILES ${SQLITE_HOME}/bin/winsqlite3.dll DESTINATION bin )
