cmake_minimum_required (VERSION 2.8.12)
project (dirent LANGUAGES C CXX)

# User options
option(DIRENT_BUILD_TESTS "Build bundled tests" OFF)

# Initialize C and C++ compilers
enable_language (C CXX)

# Compile in debug mode by default
if (NOT CMAKE_BUILD_TYPE)
    set (CMAKE_BUILD_TYPE Debug
        CACHE STRING
        "Type of build: None Debug Release RelWithDebInfo MinSizeRel."
        FORCE
    )
endif (NOT CMAKE_BUILD_TYPE)

add_library(dirent INTERFACE)
target_include_directories(dirent INTERFACE include)

# Build example programs
if(DIRENT_BUILD_TESTS)
    add_executable (find examples/find.c)
    add_executable (ls examples/ls.c)
    add_executable (locate examples/locate.c)
    add_executable (updatedb examples/updatedb.c)
    add_executable (scandir examples/scandir.c)
    add_executable (cat examples/cat.c)

    # Build test programs
    include (CTest)
    add_custom_target (check COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure -C ${CMAKE_CFG_INTDIR})
    function (add_test_executable TEST_NAME)
        add_executable (${TEST_NAME} EXCLUDE_FROM_ALL ${ARGN})
        add_test (NAME ${TEST_NAME} WORKING_DIRECTORY ${CMAKE_SOURCE_DIR} COMMAND $<TARGET_FILE:${TEST_NAME}>)
        add_dependencies (check ${TEST_NAME})
    endfunction (add_test_executable)

    add_test_executable (t-compile tests/t-compile.c)
    add_test_executable (t-dirent tests/t-dirent.c)
    add_test_executable (t-scandir tests/t-scandir.c)
    add_test_executable (t-unicode tests/t-unicode.c)
    add_test_executable (t-cplusplus tests/t-cplusplus.cpp)
    add_test_executable (t-strverscmp tests/t-strverscmp.c)
endif(DIRENT_BUILD_TESTS)
