#############################################################################
##  gTest
##
## Code below taken from README
#############################################################################
# Download and unpack googletest at configure time
configure_file(cmake/CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/googletest-src
                 ${CMAKE_CURRENT_BINARY_DIR}/googletest-build
                 EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
  include_directories("${gtest_SOURCE_DIR}/include")
endif()

# link_directories(${PROJECT_SOURCE_DIR}/build)

message(STATUS ">> gtest gecko-msgpack: ${PROJECT_SOURCE_DIR}/build")

list(APPEND gtests
    msgpack-msgs-gtest.cpp
    msgpack-adaptor-gtest.cpp
    msgpack-new-gtest.cpp
)

message(STATUS "MsgPack::gTests ----------------------")
foreach(src ${gtests})
    get_filename_component(name ${src} NAME_WE)
    message(STATUS " -> ${name}")
    # message(STATUS ">> ${ALL_LIBS}")
    add_executable(${name} ${src})
    target_link_libraries(${name} gtest_main ${ALL_LIBS} gecko)
    add_test(NAME ${name} COMMAND ${name})
endforeach()
