cmake_minimum_required(VERSION 3.5)

include_directories(${LIBSPDM_DIR}/unit_test/fuzzing/test_secured_message/test_spdm_decode_secured_message
                    ${LIBSPDM_DIR}/include
                    ${LIBSPDM_DIR}/include/library
                    ${LIBSPDM_DIR}/unit_test/include
                    ${LIBSPDM_DIR}/unit_test/fuzzing/spdm_unit_fuzzing_common
                    ${LIBSPDM_DIR}/os_stub/include
)

if(TOOLCHAIN STREQUAL "KLEE")
    include_directories($ENV{KLEE_SRC_PATH}/include)
endif()

set(src_test_spdm_decode_secured_message
    spdm_decode_secured_message.c
    ${PROJECT_SOURCE_DIR}/unit_test/fuzzing/spdm_unit_fuzzing_common/common.c
    ${PROJECT_SOURCE_DIR}/unit_test/fuzzing/spdm_unit_fuzzing_common/toolchain_harness.c
    ${PROJECT_SOURCE_DIR}/unit_test/fuzzing/spdm_unit_fuzzing_common/algo.c
)

set(test_spdm_decode_secured_message_LIBRARY
    memlib
    debuglib
    spdm_transport_mctp_lib
    spdm_common_lib
    ${CRYPTO_LIB_PATHS}
    rnglib
    cryptlib_${CRYPTO}
    malloclib
    spdm_crypt_lib
    spdm_secured_message_lib
    spdm_transport_test_lib
    spdm_device_secret_lib_null
)

if((TOOLCHAIN STREQUAL "KLEE") OR (TOOLCHAIN STREQUAL "CBMC"))
    add_executable(test_spdm_decode_secured_message
                   ${src_test_spdm_decode_secured_message}
                   $<TARGET_OBJECTS:memlib>
                   $<TARGET_OBJECTS:debuglib>
                   $<TARGET_OBJECTS:spdm_transport_mctp_lib>
                   $<TARGET_OBJECTS:spdm_common_lib>
                   $<TARGET_OBJECTS:${CRYPTO_LIB_PATHS}>
                   $<TARGET_OBJECTS:rnglib>
                   $<TARGET_OBJECTS:cryptlib_${CRYPTO}>
                   $<TARGET_OBJECTS:malloclib>
                   $<TARGET_OBJECTS:spdm_crypt_lib>
                   $<TARGET_OBJECTS:spdm_secured_message_lib>
                   $<TARGET_OBJECTS:spdm_transport_test_lib>
                   $<TARGET_OBJECTS:spdm_device_secret_lib_null>
    )
else()
    add_executable(test_spdm_decode_secured_message ${src_test_spdm_decode_secured_message})
    target_link_libraries(test_spdm_decode_secured_message ${test_spdm_decode_secured_message_LIBRARY})
endif()
