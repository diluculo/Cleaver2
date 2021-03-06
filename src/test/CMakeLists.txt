# gtest external download
SET(EXTERNAL_DIR "${CMAKE_BINARY_DIR}/externals")
SET(GTEST_INSTALL_DIR "${EXTERNAL_DIR}/googletest")
IF(WIN32)
  SET(GTEST_SHARED_ARG ON)
ELSE()
  SET(GTEST_SHARED_ARG OFF)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fPIC")
ENDIF()

INCLUDE(ExternalProject)

SET(googletest_GIT_TAG "origin/cleaver")

ExternalProject_Add(googletest
   PREFIX "${GTEST_INSTALL_DIR}"
   GIT_REPOSITORY "https://github.com/CIBC-Internal/googletest.git"
   GIT_TAG ${googletest_GIT_TAG}
   INSTALL_DIR "${GTEST_INSTALL_DIR}/include"
   BINARY_DIR "${GTEST_INSTALL_DIR}/lib"
   INSTALL_COMMAND ""
)

# Copy and Specify Include Directory
ExternalProject_Get_Property(googletest source_dir)
ExternalProject_Add_Step(
   googletest
   copy_deps
   COMMAND ${CMAKE_COMMAND} -E copy_directory ${source_dir}/googletest/include ${GTEST_INSTALL_DIR}/include
   COMMAND ${CMAKE_COMMAND} -E copy_directory ${source_dir}/googlemock/include ${GTEST_INSTALL_DIR}/include
   DEPENDEES install
)
INCLUDE_DIRECTORIES("${GTEST_INSTALL_DIR}/include")

# Set up library paths
SET(GTEST_LIBRARY gtest)
ADD_LIBRARY(${GTEST_LIBRARY} UNKNOWN IMPORTED)
ExternalProject_Get_Property(googletest binary_dir)
IF(NOT WIN32)
  SET(GTEST_LIBRARY_PATH_OTHER ${binary_dir}/lib/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_STATIC_LIBRARY_SUFFIX})
  set_target_properties(${GTEST_LIBRARY} PROPERTIES IMPORTED_LOCATION ${GTEST_LIBRARY_PATH_OTHER})
ENDIF()
add_dependencies(${GTEST_LIBRARY} googletest)

set(GTEST_MAIN_LIBRARY gtest_main)
ADD_LIBRARY(${GTEST_MAIN_LIBRARY} UNKNOWN IMPORTED)
IF(NOT WIN32)
  set(GTEST_MAIN_LIBRARY_PATH_OTHER ${binary_dir}/lib/${CMAKE_FIND_LIBRARY_PREFIXES}gtest_main${CMAKE_STATIC_LIBRARY_SUFFIX})
  set_target_properties(${GTEST_MAIN_LIBRARY} PROPERTIES IMPORTED_LOCATION ${GTEST_MAIN_LIBRARY_PATH_OTHER})
ENDIF()
add_dependencies(${GTEST_MAIN_LIBRARY} googletest)

set(GMOCK_LIBRARY gmock)
add_library(${GMOCK_LIBRARY} UNKNOWN IMPORTED)
IF(NOT WIN32)
  set(GMOCK_LIBRARY_PATH_OTHER ${binary_dir}/lib/${CMAKE_FIND_LIBRARY_PREFIXES}gmock${CMAKE_STATIC_LIBRARY_SUFFIX})
  set_target_properties(${GMOCK_LIBRARY} PROPERTIES IMPORTED_LOCATION ${GMOCK_LIBRARY_PATH_OTHER})
ENDIF()
add_dependencies(${GMOCK_LIBRARY} googletest)

set(GMOCK_MAIN_LIBRARY gmock_main)
add_library(${GMOCK_MAIN_LIBRARY} UNKNOWN IMPORTED)
IF(NOT WIN32)
  set(GMOCK_MAIN_LIBRARY_PATH_OTHER ${binary_dir}/lib/${CMAKE_FIND_LIBRARY_PREFIXES}gmock_main${CMAKE_STATIC_LIBRARY_SUFFIX})
  set_target_properties(${GMOCK_MAIN_LIBRARY} PROPERTIES IMPORTED_LOCATION ${GMOCK_MAIN_LIBRARY_PATH_OTHER})
ENDIF()
add_dependencies(${GMOCK_MAIN_LIBRARY} ${GTEST_LIBRARY})


# Definitions for Tests
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/test/bin)
#test macro
function(NEWTEST name)
  ADD_EXECUTABLE(${name} ${name}.cpp ${ARGN})
  IF(NOT WIN32)
    TARGET_LINK_LIBRARIES(${name} ${GTEST_LIBRARY} ${GTEST_MAIN_LIBRARY} ${GMOCK_LIBRARY} ${GMOCK_MAIN_LIBRARY} cleaver -lpthread)
  ELSE ()
    TARGET_LINK_LIBRARIES(${name}
       ${binary_dir}/lib/$<CONFIG>/${CMAKE_FIND_LIBRARY_PREFIXES}gtest$<$<CONFIG:Debug>:d>${CMAKE_STATIC_LIBRARY_SUFFIX}
       ${binary_dir}/lib/$<CONFIG>/${CMAKE_FIND_LIBRARY_PREFIXES}gtest_main$<$<CONFIG:Debug>:d>${CMAKE_STATIC_LIBRARY_SUFFIX}
       ${binary_dir}/lib/$<CONFIG>/${CMAKE_FIND_LIBRARY_PREFIXES}gmock$<$<CONFIG:Debug>:d>${CMAKE_STATIC_LIBRARY_SUFFIX}
       ${binary_dir}/lib/$<CONFIG>/${CMAKE_FIND_LIBRARY_PREFIXES}gmock_main$<$<CONFIG:Debug>:d>${CMAKE_STATIC_LIBRARY_SUFFIX}
        cleaver )
  ENDIF()
  ADD_TEST(${name} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${name})
  ADD_DEPENDENCIES(${name} gtest gtest_main cleaver)
ENDFUNCTION()

ADD_SUBDIRECTORY(cleaver)
ADD_SUBDIRECTORY(cli)
