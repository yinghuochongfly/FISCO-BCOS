#------------------------------------------------------------------------------
# Top-level CMake file for ut of bcos-framework
# ------------------------------------------------------------------------------
# Copyright (C) 2021 FISCO BCOS.
# SPDX-License-Identifier: Apache-2.0
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ------------------------------------------------------------------------------
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libvm/WasmPath.h.in ${CMAKE_CURRENT_SOURCE_DIR}/libvm/WasmPath.h @ONLY)

file(GLOB_RECURSE SOURCES "*.cpp" "*.h")
# cmake settings
set(TEST_BINARY_NAME test-bcos-executor)

add_executable(${TEST_BINARY_NAME} ${SOURCES})
target_include_directories(${TEST_BINARY_NAME} PRIVATE ./ ../../src ../../include ${CMAKE_BINARY_DIR}/bcos-framework)

find_package(Boost CONFIG QUIET REQUIRED unit_test_framework)
find_package(TBB CONFIG QUIET REQUIRED)
find_package(wedpr-crypto CONFIG REQUIRED)

target_link_libraries(${TEST_BINARY_NAME} ${EXECUTOR_TARGET} ${CRYPTO_TARGET} ${PBPROTOCOL_TARGET} Boost::serialization)
target_compile_options(${TEST_BINARY_NAME} PRIVATE -Wno-error -fno-var-tracking -Wno-unused-parameter)
add_test(NAME test-executor WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} COMMAND ${TEST_BINARY_NAME})

# for code coverage
if (COVERAGE)
    include(Coverage)
    config_coverage("coverage" "'/usr*' 'boost/**' 'deps/**'")
endif ()
