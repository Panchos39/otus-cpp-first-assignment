if(WITH_BOOST_TEST)
    
    find_package(Boost COMPONENTS unit_test_framework REQUIRED)
    add_executable(test_version test_version.cpp)

    set_target_properties(test_version PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
    )

    set_target_properties(test_version PROPERTIES
        #COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
        RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/tests
        RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/tests
        INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
        
    )
    
    target_link_libraries(test_version
        CONAN_PKG::boost
        helloworld
        
    )
    
endif()

if(WITH_GOOGLE_TEST)
    find_package(GTest REQUIRED)
    add_executable(test_version_gtest test_version_gtest.cpp)
    
    set_target_properties(test_version_gtest PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
    )

    set_target_properties(test_version_gtest PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/tests
        RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/tests
        INCLUDE_DIRECTORIES ${GTEST_INCLUDE_DIRS}

    )

    # исполняемый файл тестов
    target_link_libraries(test_version_gtest 
        helloworld
        pthread
        CONAN_PKG::gtest)
endif()

if (MSVC)
    if(WITH_BOOST_TEST)
        target_compile_options(test_version PRIVATE
            /W4
        )
    endif()
    
    if(WITH_GOOGLE_TEST)
        target_compile_options(test_version_gtest PRIVATE
            /W4
        )
    endif() 
else ()
    if(WITH_BOOST_TEST)
        target_compile_options(test_version PRIVATE
            -Wall -Wextra -pedantic -Werror
        )
    endif()

    if(WITH_GOOGLE_TEST)
        target_compile_options(test_version_gtest PRIVATE
            -Wall -Wextra -pedantic -Werror
        )
    endif()
endif()

include_directories(${CMAKE_SOURCE_DIR}/include)

if(WITH_BOOST_TEST)
    add_test(test_version ${CMAKE_BINARY_DIR}/tests/test_version)
endif()

if(WITH_GOOGLE_TEST)
    add_test(test_version_gtest ${CMAKE_BINARY_DIR}/tests/test_version_gtest )
endif()
