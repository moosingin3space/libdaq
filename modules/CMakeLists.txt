# TODO add shared modules support

include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../api")

if(${BUILD_AFPACKET_MODULE})
    add_library(daq_static_afpacket
        afpacket/daq_afpacket.c
        )
    target_link_libraries(daq_static_afpacket PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_afpacket
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_PCAP_MODULE})
    add_library(daq_static_pcap
        pcap/daq_pcap.c
        )
    target_include_directories(daq_static_pcap PRIVATE "${LIBPCAP_INCLUDE_DIRS}")
    target_link_libraries(daq_static_pcap PRIVATE ${LIBPCAP_LIBRARIES} ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_pcap
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_BPF_MODULE})
    add_library(daq_static_bpf
        bpf/daq_bpf.c
        )
    target_link_libraries(daq_static_bpf PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_bpf
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_DIVERT_MODULE})
    add_library(daq_static_divert
        divert/daq_divert.c
        )
    target_link_libraries(daq_static_divert PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_divert
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_DUMP_MODULE})
    add_library(daq_static_dump
        dump/daq_dump.c)
    target_include_directories(daq_static_dump PRIVATE "${LIBPCAP_INCLUDE_DIRS}")
    target_link_libraries(daq_static_dump PRIVATE ${LIBPCAP_LIBRARIES} ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_dump
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_FST_MODULE})
    add_library(daq_static_fst
        fst/daq_fst.cc
        fst/PMurHash.c
        )
    target_include_directories(daq_static_fst PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../example")
    target_link_libraries(daq_static_fst PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_fst
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_NETMAP_MODULE})
    add_library(daq_static_netmap
        netmap/daq_netmap.c
        )
    target_link_libraries(daq_static_netmap PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_netmap
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_NFQ_MODULE})
    add_library(daq_static_nfq
        nfq/daq_nfq.c
        )
    target_link_libraries(daq_static_nfq PUBLIC "-lmnl")
    target_link_libraries(daq_static_nfq PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_nfq
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
if(${BUILD_TRACE_MODULE})
    add_library(daq_static_trace
        trace/daq_trace.c
        )
    target_link_libraries(daq_static_trace PRIVATE ${CODE_COVERAGE_LIBS})
    install(TARGETS daq_static_trace
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )
endif()
