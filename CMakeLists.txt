cmake_minimum_required(VERSION 3.22)

project(2DTissueDeps VERSION 1.0)

# Set C++ Standard
set(CMAKE_CXX_STANDARD 20)

# Paths for Roadrunner and LLVM
set(ROADRUNNER_INSTALL_PREFIX "${CMAKE_CURRENT_SOURCE_DIR}/roadrunner/install-release")
set(LLVM_INSTALL_PREFIX "${CMAKE_CURRENT_SOURCE_DIR}/libroadrunner-deps/third_party/llvm-13.x/install-release")

# Set CMake Prefix Path
set(CMAKE_PREFIX_PATH
        "${ROADRUNNER_INSTALL_PREFIX}/lib/cmake/Poco" # This is where poco installs its config files
        "${ROADRUNNER_INSTALL_PREFIX}/lib/cmake"      # All other standard packages install config files here
)
list(APPEND CMAKE_MODULE_PATH "${ROADRUNNER_INSTALL_PREFIX}/lib/cmake")

# Integrate submodules
add_subdirectory(libroadrunner-deps)
add_subdirectory(roadrunner)

# Create an interface library target. This doesn't produce a physical library but handles dependencies.
add_library(2DTissueDepsInterface INTERFACE)

# Link the required libraries to the interface target and propagate include directories
target_link_libraries(2DTissueDepsInterface INTERFACE roadrunner-static::roadrunner-static)
target_include_directories(2DTissueDepsInterface INTERFACE "${ROADRUNNER_INSTALL_PREFIX}/include")

# Export the interface target for other CMake projects to use
install(TARGETS 2DTissueDepsInterface EXPORT 2DTissueDepsTargets)
install(EXPORT 2DTissueDepsTargets FILE 2DTissueDepsConfig.cmake DESTINATION lib/cmake/2DTissueDeps)
