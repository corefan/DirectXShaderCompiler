# Copyright (C) Microsoft Corporation. All rights reserved.
# Licensed under the MIT license. See COPYRIGHT in the project root for full license information.
# Generate ETW instrumentation.

# Create the header in a temporary file and only update when necessary,
# to avoid invalidating targets that depend on it.
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/dxc/Tracing/tmpdxcetw.h
  COMMAND mc -r ${CMAKE_CURRENT_BINARY_DIR} -h ${CMAKE_CURRENT_BINARY_DIR} -p DxcEtw_ -um -z tmpdxcetw ${CMAKE_CURRENT_SOURCE_DIR}/dxcetw.man
  DEPENDS {CMAKE_CURRENT_SOURCE_DIR}/dxcetw.man
  COMMENT "Building instrumentation manifest ..."
)
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/dxc/Tracing/dxcetw.h
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_CURRENT_BINARY_DIR}/tmpdxcetw.h
        ${CMAKE_CURRENT_BINARY_DIR}/dxcetw.h
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_CURRENT_BINARY_DIR}/tmpdxcetw.rc
        ${CMAKE_CURRENT_BINARY_DIR}/dxcetw.rc
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_CURRENT_BINARY_DIR}/tmpdxcetwTEMP.bin
        ${CMAKE_CURRENT_BINARY_DIR}/dxcetwTEMP.bin
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_CURRENT_BINARY_DIR}/tmpdxcetw_MSG00001.bin
        ${CMAKE_CURRENT_BINARY_DIR}/MSG00001.bin
  DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/dxc/Tracing/tmpdxcetw.h
  COMMENT "Updating instrumentation manifest ..."
)

set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/dxcetw.h PROPERTIES GENERATED 1)
set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/dxcetw.rc PROPERTIES GENERATED 1)
set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/dxcetwTEMP.bin PROPERTIES GENERATED 1)
set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/MSG00001.bin PROPERTIES GENERATED 1)

add_custom_target(DxcEtw
  DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/dxc/Tracing/dxcetw.h
  SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/dxcetw.man
)

# Not quite tablegen, but close enough.
set_target_properties(DxcEtw PROPERTIES FOLDER "Tablegenning")
