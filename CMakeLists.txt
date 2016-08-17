CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

# ======================================================================
# Set the install prefix to build directory
# --------------------------------------------------
IF(NOT DEFINED CMAKE_INSTALL_PREFIX)
    SET(CMAKE_INSTALL_PREFIX
        "${CMAKE_BINARY_DIR}" CACHE INTERNAL
        "Prefix prepended to install directories" FORCE)
ENDIF(NOT DEFINED CMAKE_INSTALL_PREFIX)

# ======================================================================
# Check for command line arguments
# ----------------------------------------------------------------------

OPTION(SPATIALITECPP_USE_LIBXML2 "Expect libxml2 from spatialite" ON)
OPTION(SPATIALITECPP_BUILD_EXAMPLES "Build examples project" OFF)
OPTION(SPATIALITECPP_BUILD_DYNAMIC "Build as dynamic library" OFF)
OPTION(SPATIALITECPP_BUILD_TEST "Build as test project" OFF)

# ======================================================================
# Define project
# ----------------------------------------------------------------------

PROJECT(SpatiaLiteCpp)

# ======================================================================
# Set SpatiaLiteCpp directory
# ----------------------------------------------------------------------

SET(spatialitecpp_dir "${CMAKE_SOURCE_DIR}")
SET(spatialitecpp_dir_inc "${spatialitecpp_dir}/include")

# ======================================================================
# Find external libraries. Directories can be set by developer via
# command line arguments or environment variables. Default is the
# libraries are in 'ext', a submodule directory.
# ----------------------------------------------------------------------

# Set shared_ptr directory
SET(sharedptr_dir_inc $ENV{sharedptr_dir_inc})
IF (NOT sharedptr_dir_inc)
    SET(sharedptr_dir_inc "${spatialitecpp_dir}/ext/shared_ptr/src")
ENDIF()

# Set SpatiaLite directory and libraries
SET(spatialite_dir_inc $ENV{spatialite_dir_inc})
IF (NOT spatialite_dir_inc)
    IF(WIN32)
        SET(spatialite_dir_inc "C:/OSGeo4W/include")
    ELSE()
        SET(spatialite_dir_inc "/usr/include")
    ENDIF()
ENDIF()
SET(spatialite_dir_lib $ENV{spatialite_dir_lib})
IF (NOT spatialite_dir_lib)
    IF(WIN32)
        SET(spatialite_dir_lib "C:/OSGeo4W/lib")
    ELSE()
        SET(spatialite_dir_lib "/usr/lib/x86_64-linux-gnu")
    ENDIF()
ENDIF()
IF(WIN32)
    SET(spatialite_lib "${spatialite_dir_lib}/spatialite_i.lib")
    SET(sqlite3_lib "${spatialite_dir_lib}/sqlite3_i.lib")
ELSE()
    SET(spatialite_lib "${spatialite_dir_lib}/libspatialite.so")
    SET(sqlite3_lib "${spatialite_dir_lib}/libsqlite3.so")
ENDIF()

# Set SQLiteCpp directory and libraries
SET(sqlitecpp_dir_inc $ENV{sqlitecpp_dir_inc})
IF (NOT sqlitecpp_dir_inc)
    SET(sqlitecpp_dir_inc "${spatialitecpp_dir}/ext/SQLiteCpp/include")
ENDIF()

# ======================================================================
# Define installation directory for all projects
# ----------------------------------------------------------------------

SET(install_dir ".")

# ======================================================================
# Define library linking and flags for testing
# ----------------------------------------------------------------------

IF (${SPATIALITECPP_BUILD_TEST})
    IF(WIN32 AND NOT ${SPATIALITECPP_BUILD_DYNAMIC})
        SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
        SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
    ENDIF()
    IF (CMAKE_COMPILER_IS_GNUCXX)
        IF (${CMAKE_BUILD_TYPE} MATCHES "Debug")
            ADD_DEFINITIONS(-fprofile-arcs -ftest-coverage)
            SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fprofile-arcs -ftest-coverage")
        ENDIF()
    ENDIF()
    ADD_SUBDIRECTORY(test)
ENDIF()

# ======================================================================
# Define project sub-directories
# ----------------------------------------------------------------------

ADD_SUBDIRECTORY(ext/SQLiteCppLocal)
ADD_SUBDIRECTORY(src)

IF(${SPATIALITECPP_BUILD_EXAMPLES})
    ADD_SUBDIRECTORY(examples)
ENDIF()

# ======================================================================
# Define package installer
# ----------------------------------------------------------------------

INCLUDE("package.cmake")
