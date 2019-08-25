enable_testing()

include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../api")
include_directories(${CMOCKA_INCLUDE_DIRS})

set(CHECK_PROGRAMS
    api_base_test
    api_config_test
    )

# api_base_test
set(api_base_SOURCES
    api_base_test.c
    daq_test_module.c
    mock_stdio.c
    )
add_executable(api_base_test ${api_base_SOURCES})
target_link_libraries(api_base_test PRIVATE daq ${CMAKE_DL_LIBS} ${CMOCKA_LIBRARIES})
target_link_libraries(api_base_test PRIVATE
    "-Wl,--wrap,printf"
    "-Wl,--wrap,__printf_chk"
	"-Wl,--wrap,fprintf"
	"-Wl,--wrap,__fprintf_chk"
	"-Wl,--wrap,opendir"
	"-Wl,--wrap,readdir"
	"-Wl,--wrap,closedir"
	"-Wl,--wrap,stat"
	"-Wl,--wrap,__xstat"
	"-Wl,--wrap,dlopen"
	"-Wl,--wrap,dlsym"
	"-Wl,--wrap,dlclose"
    )
add_test(NAME api_base_test COMMAND api_base_test)

set(api_config_SOURCES
    api_config_test.c
    daq_test_module.c
    )
add_executable(api_config_test ${api_config_SOURCES})
target_link_libraries(api_config_test PRIVATE daq ${CMAKE_DL_LIBS} ${CMOCKA_LIBRARIES})
add_test(NAME api_config_test COMMAND api_config_test)

add_custom_target(check COMMAND ${CMAKE_CTEST_COMMAND} DEPENDS ${CHECK_PROGRAMS})
