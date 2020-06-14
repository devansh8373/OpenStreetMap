cmake_minimum_required(VERSION 3.11.3)

# Set the C++ standard we will use
set(CMAKE_CXX_STANDARD 17)

# Add the path of the cmake files to the CMAKE_MODULE_PATH
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

project(OSM_A_star_search)

# Set library output path to /lib
set(MAINFOLDER $(PROJECT_SOURCE_DIR))
set(LIBRARY_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/lib")

# Locate project prerequisites
find_package(Cairo)
find_package(GraphicsMagick)

# Set IO2D flags
set(IO2D_WITHOUT_SAMPLES 1)
set(IO2D_WITHOUT_TESTS 1)

# Add the pugixml and GoogleTest library subdirectories
set(IO2D_WITHOUT_SAMPLES 1)
set(IO2D_WITHOUT_TESTS 1)
add_subdirectory(thirdparty/pugixml)
add_subdirectory(thirdparty/googletest)
add_subdirectory(thirdparty/P0267_RefImpl)
add_library(io2d::io2d ALIAS io2d) 

file(GLOB project_SRCS src/*.cpp src/*.h)

# Add project executable
#add_executable(OSM_A_star_search src/main.cpp src/model.cpp src/render.cpp src/route_model.cpp src/route_planner.cpp)
add_executable(OSM_A_star_search ${project_SRCS})

target_link_libraries(OSM_A_star_search
    PRIVATE io2d::io2d
    PUBLIC pugixml
)

# Add testing executable
add_executable(test test/utest_rp_a_star_search.cpp)
target_link_libraries(test gtest_main route_planner pugixml)
add_test(NAME test COMMAND test)
unset(TESTING CACHE)
if( ${CMAKE_SYSTEM_NAME} MATCHES "Linux" )
    target_link_libraries(OSM_A_star_search PUBLIC pthread)
endif()

if(MSVC)
	target_compile_options(OSM_A_star_search PUBLIC /D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING /wd4459)
endif()

# Create a library for unit tests
add_library(route_planner OBJECT src/route_planner.cpp src/model.cpp src/route_model.cpp)
target_include_directories(route_planner PRIVATE thirdparty/pugixml/src)


