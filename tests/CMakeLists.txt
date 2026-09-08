# ==============================================================================
# tests/CMakeLists.txt - GoogleTest Suite for openCV-flood
# ==============================================================================

find_package(GTest QUIET)
if(NOT GTest_FOUND)
    message(STATUS "[openCV-flood] GTest not found locally. Fetching GoogleTest via FetchContent...")
    include(FetchContent)
    FetchContent_Declare(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG        v1.14.0
    )
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
    set(INSTALL_GTEST OFF CACHE BOOL "" FORCE)
    set(BUILD_GMOCK OFF CACHE BOOL "" FORCE)
    FetchContent_MakeAvailable(googletest)
else()
    message(STATUS "[openCV-flood] Found local GTest: ${GTest_DIR}")
endif()

# ------------------------------------------------------------------------------
# Unit Test: test_quality_analyzer
# ------------------------------------------------------------------------------
add_executable(test_quality_analyzer
    unit/test_quality_analyzer.cpp
)
target_link_libraries(test_quality_analyzer
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_quality_analyzer)
add_test(NAME test_quality_analyzer COMMAND test_quality_analyzer)

# ------------------------------------------------------------------------------
# Unit Test: test_steger_extractor
# ------------------------------------------------------------------------------
add_executable(test_steger_extractor
    unit/test_steger_extractor.cpp
)
target_link_libraries(test_steger_extractor
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_steger_extractor)
add_test(NAME test_steger_extractor COMMAND test_steger_extractor)

# ------------------------------------------------------------------------------
# Unit Test: test_geospatial_pack
# ------------------------------------------------------------------------------
add_executable(test_geospatial_pack
    unit/test_geospatial_pack.cpp
)
target_link_libraries(test_geospatial_pack
    PRIVATE
        curv_domains
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_geospatial_pack)
add_test(NAME test_geospatial_pack COMMAND test_geospatial_pack)

# ------------------------------------------------------------------------------
# Unit Test: test_overlay_renderer
# ------------------------------------------------------------------------------
add_executable(test_overlay_renderer
    unit/test_overlay_renderer.cpp
)
target_link_libraries(test_overlay_renderer
    PRIVATE
        curv_adapters
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_overlay_renderer)
add_test(NAME test_overlay_renderer COMMAND test_overlay_renderer)

# ------------------------------------------------------------------------------
# Unit Test: test_roi_warper
# ------------------------------------------------------------------------------
add_executable(test_roi_warper
    unit/test_roi_warper.cpp
)
target_link_libraries(test_roi_warper
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_roi_warper)
add_test(NAME test_roi_warper COMMAND test_roi_warper)

# ------------------------------------------------------------------------------
# Unit Test: test_capi
# ------------------------------------------------------------------------------
add_executable(test_capi
    unit/test_capi.cpp
)
target_link_libraries(test_capi
    PRIVATE
        curv_capi
        curv_domains
        GTest::gtest_main
        nlohmann_json::nlohmann_json
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_capi)
add_test(NAME test_capi COMMAND test_capi)

# ------------------------------------------------------------------------------
# Unit Test: test_zone_intersector
# ------------------------------------------------------------------------------
add_executable(test_zone_intersector
    unit/test_zone_intersector.cpp
)
target_link_libraries(test_zone_intersector
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_zone_intersector)
add_test(NAME test_zone_intersector COMMAND test_zone_intersector)

# ------------------------------------------------------------------------------
# Unit Test: test_geometry_types
# ------------------------------------------------------------------------------
add_executable(test_geometry_types
    unit/test_geometry_types.cpp
)
target_link_libraries(test_geometry_types
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_geometry_types)
add_test(NAME test_geometry_types COMMAND test_geometry_types)

# ------------------------------------------------------------------------------
# Unit Test: test_regression_audit
# ------------------------------------------------------------------------------
add_executable(test_regression_audit
    unit/test_regression_audit.cpp
)
target_link_libraries(test_regression_audit
    PRIVATE
        curv_capi
        curv_domains
        curv_adapters
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_regression_audit)
add_test(NAME test_regression_audit COMMAND test_regression_audit)

# ------------------------------------------------------------------------------
# Unit Test: test_evaluate_lib
# ------------------------------------------------------------------------------
add_executable(test_evaluate_lib
    unit/test_evaluate_lib.cpp
)
target_link_libraries(test_evaluate_lib
    PRIVATE
        curv_tools_lib
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_evaluate_lib)
add_test(NAME test_evaluate_lib COMMAND test_evaluate_lib)

# ------------------------------------------------------------------------------
# Unit Test: test_synth_lib
# ------------------------------------------------------------------------------
add_executable(test_synth_lib
    unit/test_synth_lib.cpp
)
target_link_libraries(test_synth_lib
    PRIVATE
        curv_tools_lib
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_synth_lib)
add_test(NAME test_synth_lib COMMAND test_synth_lib)

# ------------------------------------------------------------------------------
# Unit Test: test_benchmark_lib
# ------------------------------------------------------------------------------
add_executable(test_benchmark_lib
    unit/test_benchmark_lib.cpp
)
target_link_libraries(test_benchmark_lib
    PRIVATE
        curv_tools_lib
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_benchmark_lib)
add_test(NAME test_benchmark_lib COMMAND test_benchmark_lib)

# ------------------------------------------------------------------------------
# Unit Test: test_cli_lib
# ------------------------------------------------------------------------------
add_executable(test_cli_lib
    unit/test_cli_lib.cpp
)
target_link_libraries(test_cli_lib
    PRIVATE
        curv_tools_lib
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_cli_lib)
add_test(NAME test_cli_lib COMMAND test_cli_lib)

# ------------------------------------------------------------------------------
# Unit Test: test_demo_lib
# ------------------------------------------------------------------------------
add_executable(test_demo_lib
    unit/test_demo_lib.cpp
)
target_link_libraries(test_demo_lib
    PRIVATE
        curv_tools_lib
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_demo_lib)
add_test(NAME test_demo_lib COMMAND test_demo_lib)

# ------------------------------------------------------------------------------
# Unit Test: test_parameter_resolver
# ------------------------------------------------------------------------------
add_executable(test_parameter_resolver
    unit/test_parameter_resolver.cpp
)
target_link_libraries(test_parameter_resolver
    PRIVATE
        curv_core
        GTest::gtest_main
        ${OpenCV_LIBS}
)
vision_set_compiler_warnings(test_parameter_resolver)
add_test(NAME test_parameter_resolver COMMAND test_parameter_resolver)
