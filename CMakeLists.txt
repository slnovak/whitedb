cmake_minimum_required(VERSION 3.5)
project(whitedb LANGUAGES C)

set(VERSION 0.8-alpha)

include(GNUInstallDirs)

set(INSTALL_TARGETS_DEFAULT_ARGS
    EXPORT WhiteDBTargets
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
)

# See config.h.in for documentation.
option(CHECK "Use additional validation checks" 1)
option(LOCK_PROTO "Select locking protocol" 1)
option(USE_CHILD_DB "Enable child database support" on)

set(HAVE_64BIT_GINT off)
set(HAVE_PTHREAD off)
set(HAVE_RAPTOR off)
set(NO_MINUS_C_MINUS_O off)
set(PACKAGE_NAME "WhiteDB")
set(PACKAGE_STRING "WhiteDB 0.8-alpha")
set(PACKAGE_TARNAME "whitedb")
set(PTHREAD_CREATE_JOINABLE off)
set(STRHASH_SIZE 2)
set(TTREE_CHAINED_NODES 1)
set(TTREE_SINGLE_COMPARE 1)
set(USE_BACKLINKING 1)
set(USE_DBLOG off)
set(USE_INDEX_TEMPLATE 1)
set(USE_REASONER off)
set(VERSION_MAJOR 0)
set(VERSION_MINOR 8)
set(VERSION_REV 0)

find_package(Threads)
if(Threads_FOUND)
    set(HAVE_PTHREAD 1)
endif()

if(MSVC)
    set(DBLOG_DIR "c:\\windows\\temp")
else()
    set(DBLOG_DIR "/tmp")
endif()

if(MSVC)
    configure_file(config.h.in ${whitedb_SOURCE_DIR}/config-w32.h)
else()
    configure_file(config.h.in #{whitedb_SOURCE_DIR}/config.h)
endif()

set(whitedb_sources
    Db/dballoc.c
    Db/dbcompare.c
    Db/dbdata.c
    Db/dbdump.c
    Db/dbhash.c
    Db/dbindex.c
    Db/dbjson.c
    Db/dblock.c
    Db/dblog.c
    Db/dbmem.c
    Db/dbmpool.c
    Db/dbquery.c
    Db/dbschema.c
    Db/dbutil.c
    json/yajl_all.c
)

set(whitedb_headers
    Db/crc1.h
    Db/dballoc.h
    Db/dbapi.h
    Db/dbcompare.h
    Db/dbdata.h
    Db/dbdump.h
    Db/dbfeatures.h
    Db/dbhash.h
    Db/dbindex.h
    Db/dbjson.h
    Db/dblock.h
    Db/dblog.h
    Db/dbmem.h
    Db/dbmpool.h
    Db/dbquery.h
    Db/dbschema.h
    Db/dbutil.h
    Db/indexapi.h
    Db/rdfapi.h
)

add_library(whitedb ${whitedb_sources})
add_library(WhiteDB::whitedb ALIAS whitedb)
target_link_libraries(whitedb m)

add_executable(wgdb Main/wgdb.c)
target_link_libraries(wgdb whitedb m)

target_include_directories(whitedb INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Db>
    $<INSTALL_INTERFACE:include>
)

set_target_properties(whitedb PROPERTIES VERSION ${VERSION})
install(TARGETS whitedb ${INSTALL_TARGETS_DEFAULT_ARGS})
install(FILES Db/dbapi.h DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/whitedb")
