include(CTest)
find_program( MEMORYCHECK_COMMAND valgrind )
find_package(Threads)
set( MEMORYCHECK_COMMAND_OPTIONS "--trace-children=yes --leak-check=full" )

# Set include directories
include_directories(
	../mission_inc
	../platform_inc
	$ENV{CFE_DIR}/fsw/mission_inc
	$ENV{CFE_DIR}/fsw/platform_inc/cpu1
	${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src
	${CMAKE_CURRENT_SOURCE_DIR}
	${CFE_INC_DIRS}
	${OSAL_INC_DIRS}
	${UTF_INCS}
	${APP_INC_DIRS})

add_definitions(-DCFE_LINUX)

set(CMAKE_C_FLAGS "-g -O0 -Wformat=0 -Wno-pointer-to-int-cast -Wno-int-to-pointer-cast -fprofile-arcs -ftest-coverage -m32")

# Add the executables
add_executable(MD-UT1
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test1.c
		${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src/cfs_utils.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MD-UT1 -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

add_executable(MD-UT2 
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test2.c
		${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src/cfs_utils.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MD-UT2 -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

add_executable(MD-UT3 
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test3.c
		${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src/cfs_utils.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MD-UT3 -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

add_executable(MD-UT4 
		${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test4.c
		${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src/cfs_utils.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MD-UT4 -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

# We need to both run the test and compare the as-run with the expected output
# with a diff command. 
add_test(NAME md-ut1 COMMAND $<TARGET_FILE:MD-UT1>)
    
add_test(NAME md-ut2 COMMAND $<TARGET_FILE:MD-UT2>)
    
add_test(NAME md-ut3 COMMAND $<TARGET_FILE:MD-UT3>)
    
add_test(NAME md-ut4 COMMAND $<TARGET_FILE:MD-UT4>)

# Copy input files to build directory
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test1.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test2.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test3.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/md_unit_test4.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)

# Build tables for the tests
add_subdirectory(ram ram)
add_subdirectory(ramnosig ramnosig)
