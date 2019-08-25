set(PUBLIC_HEADERS
    daq.h
    daq_common.h
    daq_dlt.h
    daq_module_api.h
    )

set(DAQ_SOURCES
    daq_base.c
    daq_base_api.c
    daq_config.c
    daq_instance_api_defaults.c
    daq_mod_ops.c
    )

configure_file(daq_version.h.in daq_version.h)

add_library(daq ${DAQ_SOURCES})
target_link_libraries(daq PUBLIC ${CMAKE_DL_LIBS} ${CODE_COVERAGE_LIBS})
#target_link_libraries(daq PRIVATE
#"-version-info ${DAQ_MAJOR_VERSION}:${DAQ_MINOR_VERSION}:${DAQ_PATCH_VERSION}")
target_include_directories(daq PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
set_target_properties(daq
    PROPERTIES
    PUBLIC_HEADER "${PUBLIC_HEADERS};${CMAKE_CURRENT_BINARY_DIR}/daq_version.h"
    )

install(TARGETS daq
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    )
