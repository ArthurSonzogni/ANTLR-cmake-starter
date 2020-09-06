cmake_minimum_required (VERSION 3.0)
project(ANTLR_Starter
  LANGUAGES CXX
)
set(CMAKE_CXX_STANDARD 11)

# ┌─────────────┐
# │ ANTLR       │
# └─────────────┘

# Function
#   ANTLR(INPUT <input> (DEPENDENCIES <dependencies>))
#
# Description:
#   Take an ANTLR file and produce a CMake rule to generate the corresponding
#   C++ files.
#
# Notes:
#   The ANTLR file path must be relative to ${CMAKE_CURRENT_SOURCE_DIR}
#
# Example:
#   ANTLR(INPUT MyLexer.g4)
#   ANTLR(INPUT MyParser.g4 DEPENDENCIES MyLexer.cpp)
function(ANTLR)
  cmake_parse_arguments(ARGUMENTS "DEPENDENCIES" "INPUT" "" ${ARGN} )
  set(source ${ARGUMENTS_INPUT})
  set(dependencies ${ARGUMENTS_UNPARSED_ARGUMENTS})
  get_filename_component(source_filename ${CMAKE_CURRENT_SOURCE_DIR}/${source} NAME_WE)
  get_filename_component(source_src_dir  ${CMAKE_CURRENT_SOURCE_DIR}/${source} DIRECTORY)
  get_filename_component(source_gen_dir  ${CMAKE_CURRENT_BINARY_DIR}/${source} DIRECTORY)
  add_custom_command(
    DEPENDS
      ${source_src_dir}/${source_filename}.g4
      ${dependencies}
    OUTPUT
      ${source_gen_dir}/${source_filename}.h
      ${source_gen_dir}/${source_filename}.cpp
      ${source_gen_dir}/${source_filename}.interp
      ${source_gen_dir}/${source_filename}.tokens
    COMMAND
      java
    ARGS
      -jar ${CMAKE_SOURCE_DIR}/tools/antlr/antlr.jar
      -Dlanguage=Cpp
      -no-listener
      -no-visitor
      -o ${source_gen_dir}
      ${source_src_dir}/${source_filename}.g4
  )
  set(output ${source_gen_dir}/${source_filename}.cpp)
endfunction()

SET(WITH_LIBCXX OFF CACHE BOOL "")
add_subdirectory(${CMAKE_SOURCE_DIR}/tools/antlr/antlr4/runtime/Cpp/ EXCLUDE_FROM_ALL)
include_directories(${CMAKE_SOURCE_DIR}/tools/antlr/antlr4/runtime/Cpp/runtime/src)
add_subdirectory(src)
