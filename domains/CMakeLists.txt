# ==============================================================================
# curv_domains Static Library Target (Geospatial Flood Domain)
# ==============================================================================

add_library(curv_domains STATIC
    geospatial/src/GeospatialFloodPack.cpp
)

add_library(curv_domain_geospatial ALIAS curv_domains)

target_include_directories(curv_domains
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/geospatial/include>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(curv_domains
    PUBLIC
        curv_core
)

vision_set_compiler_warnings(curv_domains)
