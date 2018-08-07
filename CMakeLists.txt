cmake_minimum_required(VERSION 3.5)
project(whitedb LANGUAGES C)

set(VERSION 0.7.3)

include(GNUInstallDirs)

set(INSTALL_TARGETS_DEFAULT_ARGS
    EXPORT WhiteDBTargets
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
)

option(USE_CHILD_DB "Enable child database support" on)

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

add_executable(wgdb Main/wgdb.c)
target_link_libraries(wgdb whitedb)

target_include_directories(whitedb INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Db>
    $<INSTALL_INTERFACE:include>
)

set_target_properties(whitedb PROPERTIES VERSION ${VERSION})
install(TARGETS whitedb ${INSTALL_TARGETS_DEFAULT_ARGS})
install(FILES Db/dbapi.h DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/whitedb")
