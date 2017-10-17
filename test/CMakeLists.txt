set(UNITTEST Unittest)
file(GLOB_RECURSE SOURCES "*.cpp")
add_executable(${UNITTEST} ${SOURCES})

if(UNIX)
    target_link_libraries(${UNITTEST}
        ${PROJECT_NAME} gtest_main gtest pthread
    )
elseif(MSVC)
    target_link_libraries(${UNITTEST}
        ${PROJECT_NAME}
    )
    if(CMAKE_BUILD_TYPE MATCHES Debug)
        target_link_libraries(${UNITTEST}
            ${VCPKG_ROOT}/debug/lib/manual-link/gtest.lib
            ${VCPKG_ROOT}/debug/lib/manual-link/gtest_main.lib
        )
    else()
        target_link_libraries(${UNITTEST}
            ${VCPKG_ROOT}/lib/manual-link/gtest.lib
            ${VCPKG_ROOT}/lib/manual-link/gtest_main.lib
        )
    endif()

    foreach(dll ${DLLS})
        add_custom_command(TARGET ${UNITTEST}
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
            ${dll}
            $<TARGET_FILE_DIR:${UNITTEST}>
        )
    endforeach(dll)
endif()