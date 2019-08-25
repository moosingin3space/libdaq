project(libdaq)
cmake_minimum_required(VERSION 3.4)

set(ALL_BUNDLED_MODULES
    pcap
    dump
    bpf
    afpacket
    netmap
    nfq
    trace
    fst
    divert
    )

# Configure options
option(HAS_BUNDLED_MODULES "Build the bundled DAQ modules" ON)
option(HAS_CODE_COVERAGE "Build with code coverage enabled" OFF)

set(BUNDLED_MODULES "${ALL_BUNDLED_MODULES}" CACHE STRING "Selects the bundled DAQ modules")

# DAQ version
set(DAQ_MAJOR_VERSION 3)
set(DAQ_MINOR_VERSION 0)
set(DAQ_PATCH_VERSION 0)
set(DAQ_VERSION_STR
    "${DAQ_MAJOR_VERSION}.${DAQ_MINOR_VERSION}.${DAQ_PATCH_VERSION}")

# Special compiler flags
if("${CMAKE_C_COMPILER_ID}" STREQUAL "GNU")
    add_compile_options(
        -Wall
        -Wmissing-declarations
        -Wpointer-arith
        -Wcast-align
        -Wcast-qual
        -Wformat
        -Wformat-nonliteral
        -Wformat-security
        -Wundef
        -Wwrite-strings
        -Wextra
        -Wsign-compare
        -Wno-unused-parameter
        -fno-strict-aliasing
        -fdiagnostics-show-option
        -Wstrict-prototypes
        -Wmissing-prototypes
        -Wold-style-definition
        -Wnested-externs
        )
endif()

find_package(PkgConfig REQUIRED)
include(CheckIncludeFiles)
include(CheckSymbolExists)
include(CheckLibraryExists)
include(GNUInstallDirs)

# set up code coverage
set(CODE_COVERAGE_LIBS)
if(${HAS_CODE_COVERAGE})
    add_compile_options(
        -fprofile-arcs
        -ftest-coverage
        )
    set(CODE_COVERAGE_LIBS "-lgcov")
endif()
# check for cmocka
pkg_check_modules(CMOCKA cmocka)

if(${HAS_BUNDLED_MODULES})
    message("want to build ${BUNDLED_MODULES}")
    set(BUILD_DUMP_MODULE FALSE)
    set(BUILD_PCAP_MODULE FALSE)
    set(BUILD_BPF_MODULE FALSE)
    set(BUILD_AFPACKET_MODULE FALSE)
    set(BUILD_NETMAP_MODULE FALSE)
    set(BUILD_TRACE_MODULE FALSE)
    set(BUILD_FST_MODULE FALSE)
    set(BUILD_DIVERT_MODULE FALSE)

    # pcap
    if("pcap" IN_LIST BUNDLED_MODULES)
        pkg_check_modules(LIBPCAP libpcap)
        if(${LIBPCAP_FOUND})
            set(BUILD_PCAP_MODULE TRUE)
        else()
            message(FATAL_ERROR "pcap module requested but libpcap not found")
        endif()
    endif()
    if("dump" IN_LIST BUNDLED_MODULES)
        pkg_check_modules(LIBPCAP libpcap)
        if(${LIBPCAP_FOUND})
            set(BUILD_DUMP_MODULE TRUE)
        else()
            message(FATAL_ERROR "dump module requested but libpcap not found")
        endif()
    endif()
    if("bpf" IN_LIST BUNDLED_MODULES)
        pkg_check_modules(LIBPCAP libpcap)
        if(${LIBPCAP_FOUND})
            set(BUILD_BPF_MODULE TRUE)
        else()
            message(FATAL_ERROR "bpf module requested but libpcap not found")
        endif()
    endif()

    # AF_PACKET
    if("afpacket" IN_LIST BUNDLED_MODULES)
        if("${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")
            # check for Linux 3.14, conditionally enable AF_PACKET
            check_include_files("linux/if_ether.h;linux/if_packet.h" AFPKT_HEADERS_PRESENT)
            check_symbol_exists("PACKET_FANOUT_QM" linux/if_packet.h FANOUT_QM_SUPPORTED)
            check_symbol_exists("PACKET_QDISC_BYPASS" linux/if_packet.h QDISC_BYPASS_SUPPORTED)
            check_symbol_exists("TP_STATUS_VLAN_TPID_VALID" linux/if_packet.h VLAN_SUPPORTED)
            if(${AFPKT_HEADERS_PRESENT} AND ${FANOUT_QM_SUPPORTED}
                    AND ${QDISC_BYPASS_SUPPORTED} AND ${VLAN_SUPPORTED})
                set(BUILD_AFPACKET_MODULE TRUE)
            else()
                message(FATAL_ERROR "afpacket module requested but not building on Linux 3.14+")
            endif()
        else()
            message(FATAL_ERROR "afpacket module requested but not building on Linux 3.14+")
        endif()
    endif()

    # netmap
    if("netmap" IN_LIST BUNDLED_MODULES)
        check_include_files("net/netmap.h;net/netmap_user.h" NM_HEADERS_PRESENT)
        check_symbol_exists("NETMAP_API" "net/if.h;net/netmap.h" NM_API_PRESENT)
        if(${NM_HEADERS_PRESENT} AND ${NM_API_PRESENT})
            set(BUILD_NETMAP_MODULE TRUE)
        else()
            message(FATAL_ERROR "netmap module requested but netmap userspace library not found")
        endif()
    endif()

    # nfq
    if("nfq" IN_LIST BUNDLED_MODULES)
        check_include_files("linux/netfilter.h;linux/netfilter/nfnetlink_queue.h;libmnl/libmnl.h"
            NFQ_HEADERS_PRESENT)
        check_library_exists(mnl mnl_socket_open "" NFQ_LIBRARY_PRESENT)
        if(${NFQ_HEADERS_PRESENT} AND ${NFQ_LIBRARY_PRESENT})
            set(BUILD_NFQ_MODULE TRUE)
        else()
            message(FATAL_ERROR "nfq module requested but nfq library not found")
        endif()
    endif()

    # trace
    if("trace" IN_LIST BUNDLED_MODULES)
        set(BUILD_TRACE_MODULE TRUE)
    endif()

    # fst
    if("fst" IN_LIST BUNDLED_MODULES)
        set(BUILD_FST_MODULE TRUE)
        set(CMAKE_CXX_STANDARD 11)
    endif()

    # divert
    if("divert" IN_LIST BUNDLED_MODULES)
        check_symbol_exists(IPPROTO_DIVERT netinet/in.h DIVERT_SUPPORTED)
        if(${DIVERT_SUPPORTED})
            set(BUILD_DIVERT_MODULE TRUE)
        else()
            message(FATAL_ERROR "divert module requested but divert library not found")
        endif()
    endif()
endif()

add_subdirectory(api)
add_subdirectory(modules)
add_subdirectory(example)
if(${CMOCKA_FOUND})
    add_subdirectory(test)
endif()

configure_file(libdaq.pc.in libdaq.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libdaq.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
    )
