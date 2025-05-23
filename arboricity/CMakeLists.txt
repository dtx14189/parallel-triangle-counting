project(arboricity)
set(arboricity_VERSION_MAJOR 0)
set(arboricity_VERSION_MINOR 1)
set(PROJECT_NAME arboricity)

cmake_minimum_required(VERSION 2.8.12)

add_compile_options(-std=c++14)

add_definitions(-march=native)

add_library(arboricity arboricity.h spanning_forest.cpp solve.cpp undirected_graph.cpp maxflow-v3.04.src/graph.cpp maxflow-v3.04.src/maxflow.cpp )
set_target_properties(arboricity PROPERTIES LINKER_LANGUAGE CXX)

add_executable(test_arboricity test_arboricity.cpp)
target_link_libraries(test_arboricity arboricity)

add_executable(find_arboricity find_arboricity.cpp)
target_link_libraries(find_arboricity PRIVATE arboricity)

find_package(OpenMP REQUIRED)
target_link_libraries(find_arboricity PRIVATE OpenMP::OpenMP_CXX)