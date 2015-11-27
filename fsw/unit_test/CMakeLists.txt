include(CTest)
find_program( MEMORYCHECK_COMMAND valgrind )
find_package(Threads)
set( MEMORYCHECK_COMMAND_OPTIONS "--trace-children=yes --leak-check=full" )

# Set include directories
include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CFE_INC_DIRS}
	${OSAL_INC_DIRS}
	${UTF_INCS}
	${APP_INC_DIRS})

add_definitions(-DCFE_LINUX)

set(CMAKE_C_FLAGS "-g -O0 -Wformat=0 -Wno-pointer-to-int-cast -Wno-int-to-pointer-cast -fprofile-arcs -ftest-coverage -m32")

# Add the executables
add_executable(MD-UT 
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test1.c
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test2.c
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test3.c
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test4.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MD-UT -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

# We need to both run the test and compare the as-run with the expected output
# with a diff command. 
add_test(NAME md-ut
    COMMAND ${CMAKE_COMMAND}
    	-DTEST_PROG=$<TARGET_FILE:MD-UT>
    	-DEXPECTED_OUTPUT=${UNIT_TEST_INPUT_DIR}/md_utf_test.out
    	-DACTUAL_OUTPUT=${CMAKE_CURRENT_BINARY_DIR}/md_utf_test.out
    -P ${CMAKE_CURRENT_SOURCE_DIR}/runtest.cmake)

# Copy input files to build directory
configure_file(${UNIT_TEST_INPUT_DIR}/md_unit_test1.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${UNIT_TEST_INPUT_DIR}/md_unit_test2.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${UNIT_TEST_INPUT_DIR}/md_unit_test3.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${UNIT_TEST_INPUT_DIR}/md_unit_test4.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)

# Build tables for the tests
add_subdirectory(ram ram)
add_subdirectory(ramnosig ramnosig)