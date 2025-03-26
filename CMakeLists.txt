cmake_minimum_required (VERSION 3.5)
project(ANTLR_Starter
  LANGUAGES CXX
)
include(FetchContent)

# useful diagnostic/debug options
# set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "Build Type" FORCE)
# set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS  "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
# set(CMAKE_VERBOSE_MAKEFILE ON)

set(ANTLR_VER 4.13.2)
set(ANTLR_JAR_FILE antlr-${ANTLR_VER}-complete.jar)
set(ANTLR_DOWNLOAD_URL "https://www.antlr.org/download/${ANTLR_JAR_FILE}")

FetchContent_Declare(
  antlr_jar
  DOWNLOAD_NO_EXTRACT TRUE
  URL  ${ANTLR_DOWNLOAD_URL}
)
FetchContent_MakeAvailable(antlr_jar)
set(ANTLR_JAR_DIR ${FETCHCONTENT_BASE_DIR}/antlr_jar-src)

FetchContent_Declare(
  antlr_repo
  GIT_REPOSITORY https://github.com/antlr/antlr4.git
  DOWNLOAD_EXTRACT_TIMESTAMP true
  GIT_SHALLOW 1
  GIT_TAG  ${ANTLR_VER}
)
FetchContent_MakeAvailable(antlr_repo)
set(ANTLR_REPO_DIR ${FETCHCONTENT_BASE_DIR}/antlr_repo-src)

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
      -jar ${ANTLR_JAR_DIR}/${ANTLR_JAR_FILE}
      -Dlanguage=Cpp
      -no-listener
      -no-visitor
      -o ${source_gen_dir}
      ${source_src_dir}/${source_filename}.g4
  )
  set(output ${source_gen_dir}/${source_filename}.cpp)
endfunction()

SET(WITH_LIBCXX OFF CACHE BOOL "")
add_subdirectory(${ANTLR_REPO_DIR}/runtime/Cpp/ EXCLUDE_FROM_ALL)
include_directories(${ANTLR_REPO_DIR}/runtime/Cpp/)
add_subdirectory(src)
