#/***************************************************************************
# *
# *  @license
# *  Copyright (C) Codeplay Software Limited
# *  Licensed under the Apache License, Version 2.0 (the "License");
# *  you may not use this file except in compliance with the License.
# *  You may obtain a copy of the License at
# *
# *      http://www.apache.org/licenses/LICENSE-2.0
# *
# *  For your convenience, a copy of the License has been included in this
# *  repository.
# *
# *  Unless required by applicable law or agreed to in writing, software
# *  distributed under the License is distributed on an "AS IS" BASIS,
# *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# *  See the License for the specific language governing permissions and
# *  limitations under the License.
# *
# *  SYCL-BLAS: BLAS implementation using SYCL
# *
# *  @filename CMakeLists.txt
# *
# **************************************************************************/
add_definitions(-D${TARGET})

set(SYCLBLAS_EXPRTEST ${CMAKE_CURRENT_SOURCE_DIR})

# compiling tests
set(SYCL_EXPRTEST_SRCS
  ${SYCLBLAS_EXPRTEST}/blas1_scal_asum_test.cpp
  ${SYCLBLAS_EXPRTEST}/blas1_axpy_copy_test.cpp
  ${SYCLBLAS_EXPRTEST}/collapse_nested_tuple.cpp
  ${SYCLBLAS_EXPRTEST}/extension_reduction_partial_rows_test.cpp
)

foreach(blas_test ${SYCL_EXPRTEST_SRCS})
  get_filename_component(test_exec ${blas_test} NAME_WE)
  add_executable(${test_exec} main.cpp ${blas_test})
  target_link_libraries(${test_exec} PRIVATE gtest_main blas::blas sycl_blas_common sycl_blas)
  target_include_directories(${test_exec} PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/.." ${CBLAS_INCLUDE} ${SYCLBLAS_SRC})
  add_sycl_to_target(
    TARGET ${test_exec}
    SOURCES ${blas_test}
  )
  if(TEST_DEVICE)
    add_test(NAME ${test_exec} COMMAND ${CMAKE_CURRENT_BINARY_DIR}/${test_exec} --device ${TEST_DEVICE})
  else()
    add_test(NAME ${test_exec} COMMAND ${CMAKE_CURRENT_BINARY_DIR}/${test_exec})
  endif()
  message(STATUS "Created google test ${test_exec}")
  install(TARGETS ${test_exec}
    RUNTIME
      DESTINATION ${CMAKE_INSTALL_BINDIR}
      COMPONENT tests
  )
endforeach()
