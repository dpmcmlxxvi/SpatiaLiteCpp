
# ==================================================
# Set header files
# --------------------------------------------------

SET(spatialitecpp_hdr 
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Auxiliary.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Blob.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Buffer.hpp"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Checksum.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Converter.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Dbf.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/DbfField.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/DbfList.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/DynamicLine.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/ExifTagList.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/GeometryCollection.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/LineString.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/OutputBuffer.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Point.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Polygon.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Ring.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Shapefile.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatialDatabase.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatiaLiteCpp.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatiaLiteCppAbi.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/VectorLayersList.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/WfsCatalog.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/WfsSchema.h")

# ==================================================
# Set source files
# --------------------------------------------------

SET(spatialitecpp_src
    "${spatialitecpp_dir}/src/Auxiliary.cpp"
    "${spatialitecpp_dir}/src/Blob.cpp"
    "${spatialitecpp_dir}/src/Checksum.cpp"
    "${spatialitecpp_dir}/src/Converter.cpp"
    "${spatialitecpp_dir}/src/Dbf.cpp"
    "${spatialitecpp_dir}/src/DbfField.cpp"
    "${spatialitecpp_dir}/src/DbfList.cpp"
    "${spatialitecpp_dir}/src/DynamicLine.cpp"
    "${spatialitecpp_dir}/src/ExifTagList.cpp"
    "${spatialitecpp_dir}/src/GeometryCollection.cpp"
    "${spatialitecpp_dir}/src/LineString.cpp"
    "${spatialitecpp_dir}/src/OutputBuffer.cpp"
    "${spatialitecpp_dir}/src/Point.cpp"
    "${spatialitecpp_dir}/src/Polygon.cpp"
    "${spatialitecpp_dir}/src/Ring.cpp"
    "${spatialitecpp_dir}/src/SpatialDatabase.cpp"
    "${spatialitecpp_dir}/src/Shapefile.cpp"
    "${spatialitecpp_dir}/src/VectorLayersList.cpp"
    "${spatialitecpp_dir}/src/WfsCatalog.cpp"
    "${spatialitecpp_dir}/src/WfsSchema.cpp")

# ==================================================
# Set xml2 specific files
# --------------------------------------------------

IF(${SPATIALITECPP_USE_LIBXML2})
    LIST(APPEND spatialitecpp_hdr
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/XmlBlob.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/XmlDocument.h")
    LIST(APPEND spatialitecpp_src
        "${spatialitecpp_dir}/src/XmlBlob.cpp"
        "${spatialitecpp_dir}/src/XmlDocument.cpp")
ENDIF()

# ==================================================
# Set documentation files
# --------------------------------------------------

SET(spatialitecpp_doc
    "${spatialitecpp_dir}/readme.md")

SOURCE_GROUP("Document Files" FILES ${spatialitecpp_doc})

# ==================================================
# Set script files
# --------------------------------------------------

SET(spatialitecpp_script
    "${spatialitecpp_dir}/build.bat"
    "${spatialitecpp_dir}/build.sh"
    "${spatialitecpp_dir}/Doxyfile")

SOURCE_GROUP("Script Files" FILES ${spatialitecpp_script})

# ==================================================
# Set include directories
# --------------------------------------------------

SET(spatialitecpp_inc
    ${sharedptr_dir_inc}
    ${spatialitecpp_dir_inc}
    ${spatialite_dir_inc}
    ${sqlitecpp_dir_inc})
INCLUDE_DIRECTORIES(${spatialitecpp_inc})

# ==================================================
# Add library and link dependencies
# --------------------------------------------------

IF(${SPATIALITECPP_BUILD_DYNAMIC})
    IF(WIN32)
        ADD_DEFINITIONS(-DSPATIALITECPP_EXPORT)
    ENDIF()
    ADD_LIBRARY(SpatiaLiteCpp SHARED ${spatialitecpp_src}
                                     ${spatialitecpp_hdr}
                                     ${spatialitecpp_doc}
                                     ${spatialitecpp_script})
    TARGET_LINK_LIBRARIES(SpatiaLiteCpp
        SQLiteCpp
        ${spatialite_lib}
        ${sqlite3_lib})
ELSE()
    ADD_LIBRARY(SpatiaLiteCpp STATIC ${spatialitecpp_src}
                                     ${spatialitecpp_hdr}
                                     ${spatialitecpp_doc}
                                     ${spatialitecpp_script})
ENDIF()
SET_TARGET_PROPERTIES(SpatiaLiteCpp PROPERTIES LINKER_LANGUAGE CXX)

# ==================================================
# Install library and header files
# --------------------------------------------------

SET(install_dir_lib "${install_dir}/lib")

IF(${SPATIALITECPP_BUILD_DYNAMIC})
    INSTALL(TARGETS SpatiaLiteCpp
        ARCHIVE DESTINATION ${install_dir_lib}/
        LIBRARY DESTINATION ${install_dir_lib}/
        RUNTIME DESTINATION ${install_dir_lib}/)
ELSE()
    INSTALL(TARGETS SpatiaLiteCpp
        ARCHIVE DESTINATION ${install_dir_lib}/)
ENDIF()

INSTALL(DIRECTORY "${spatialitecpp_dir_inc}" DESTINATION ${install_dir})
