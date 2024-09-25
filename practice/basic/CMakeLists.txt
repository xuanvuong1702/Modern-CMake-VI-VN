cmake_minimum_required(VERSION 3.15...3.30)

project(practice_basic LANGUAGES CXX)


add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)

target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc app/calc.cpp)
target_link_libraries(calc PUBLIC calclib)