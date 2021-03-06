set(LIBS core gtest_main)
if(APPLE)
	list(APPEND LIBS ${FOUNDATION_LIBRARY} ${CORESERV_LIBRARY})
endif()
if(ANDROID)
	set(LIBS ${LIBS} android log)
endif()
file(MAKE_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/Tests)
macro(add_dolphin_test target srcs)
	# Since this is a Core dependency, it can't be linked as a library and has
	# to be linked as an object file. Otherwise CMake inserts the library after
	# core, but before other core dependencies like videocommon which also use
	# Host_ functions.
	set(srcs2 ${srcs} ${CMAKE_SOURCE_DIR}/Source/UnitTests/TestUtils/StubHost.cpp)
	add_executable(Test_${target} EXCLUDE_FROM_ALL ${srcs2})
	set_target_properties(Test_${target} PROPERTIES
		OUTPUT_NAME Tests/${target}
		FOLDER Tests
	)
	target_link_libraries(Test_${target} ${LIBS})
	add_dependencies(unittests Test_${target})
	add_test(NAME ${target} COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/Tests/${target})
endmacro()

add_subdirectory(TestUtils)

add_subdirectory(Common)
add_subdirectory(Core)
add_subdirectory(VideoCommon)
