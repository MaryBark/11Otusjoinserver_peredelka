#cmake_minimum_required(VERSION 3.5)

#project(11otusjoin_server LANGUAGES CXX)

#set(CMAKE_CXX_STANDARD 11)
#set(CMAKE_CXX_STANDARD_REQUIRED ON)

#add_executable(11otusjoin_server main.cpp)

cmake_minimum_required(VERSION 3.2)

if (DEFINED ENV{TRAVIS_BUILD_NUMBER})
   project(11otusjoin_server VERSION 0.0 $ENV{TRAVIS_BUILD_NUMBER})
else()
   project(11otusjoin_server VERSION 0.0.1)
endif()

find_package(GTest REQUIRED)
find_package(Boost COMPONENTS system REQUIRED)

set(INCLUDE_DIR ${PROJECT_SOURCE_DIR})
file(GLOB HEADER_FILES *.h *.cpp)
add_executable(11otusjoin_server ${HEADER_FILES} main.cpp)

set_target_properties(11otusjoin_server PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
        INCLUDE_DIRECTORIES "${Boost_INCLUDE_DIR};${INCLUDE_DIR}"
)

enable_testing()
add_executable(11otusjoin_server_tests ${HEADER_FILES} test_main.cpp)

set_target_properties(11otusjoin_server_tests PROPERTIES INCLUDE_DIRECTORIES ${GTEST_INCLUDE_DIRS} )

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
        set_target_properties(11otusjoin_server PROPERTIES COMPILE_OPTIONS -W3)
        target_link_libraries(11otusjoin_server ${Boost_LIBRARIES} )
        target_link_libraries(11otusjoin_server_tests ${GTEST_LIBRARIES} ${GTEST_MAIN_LIBRARIES})
else()
        set_target_properties(11otusjoin_server PROPERTIES COMPILE_OPTIONS -Wpedantic -Wall -Wextra)
        target_link_libraries(11otusjoin_server pthread ${Boost_LIBRARIES} )
        target_link_libraries(11otusjoin_server_tests pthread ${GTEST_LIBRARIES} ${GTEST_MAIN_LIBRARIES})
endif()


add_test(test_symmetric_difference	11otusjoin_server_tests)
add_test(test_intersection			11otusjoin_server_tests)
add_test(test_output				11otusjoin_server_tests)
add_test(test_commands				11otusjoin_server_tests)
add_test(test_command_errors		11otusjoin_server_tests)

install(TARGETS 11otusjoin_server RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT Alttaira@yandex.ru)

include (CPack)
