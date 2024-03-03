cmake_minimum_required(VERSION 3.15...3.26)

if(SCIKIT_BUILD)
    project(${SKBUILD_PROJECT_NAME} LANGUAGES CXX)
else()
    project(rectangle LANGUAGES CXX)
endif()

if(SCIKIT_BUILD)
    find_package(
        Python
        COMPONENTS Interpreter Development.Module
        REQUIRED)

    find_program(CYTHON "cython")

    add_custom_command(
        OUTPUT rect.cpp
        DEPENDS rect.pyx
        VERBATIM
        COMMAND "${CYTHON}" "${CMAKE_CURRENT_SOURCE_DIR}/rect.pyx" --output-file
        "${CMAKE_CURRENT_BINARY_DIR}/rect.cpp" --cplus)

    configure_file("${CMAKE_CURRENT_SOURCE_DIR}/Rectangle.cpp" "${CMAKE_CURRENT_BINARY_DIR}/Rectangle.cpp" COPYONLY)
    configure_file("${CMAKE_CURRENT_SOURCE_DIR}/Rectangle.h" "${CMAKE_CURRENT_BINARY_DIR}/Rectangle.h" COPYONLY)

    python_add_library(rect MODULE "${CMAKE_CURRENT_BINARY_DIR}/rect.cpp"
        WITH_SOABI)

    install(TARGETS rect DESTINATION .)
else()
    add_library(Rectangle Rectangle.cpp)
endif()
