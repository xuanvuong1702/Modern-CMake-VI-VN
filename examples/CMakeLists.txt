cmake_minimum_required(VERSION 3.15...3.30)

project(ModernCMakeExamples)
set(MODERN_CMAKE_BUILD_TESTING ON)

include(CTest)

add_subdirectory(simple-project)
add_subdirectory(extended-project)
add_subdirectory(fetch)

add_subdirectory(root-usefile)
add_subdirectory(root-simple)
add_subdirectory(root-dict)
