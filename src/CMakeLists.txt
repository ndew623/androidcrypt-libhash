# Create the library
add_library(hash STATIC hash.cpp hmac.cpp sha1.cpp sha256.cpp sha384.cpp
                        sha512.cpp)
add_library(Terra::libhash ALIAS hash)

# Specify the internal and public include directories
target_include_directories(hash
    PRIVATE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>)

# Specify the C++ standard to observe
set_target_properties(hash
    PROPERTIES
        CXX_STANDARD 20
        CXX_STANDARD_REQUIRED ON
        CXX_EXTENSIONS OFF)

# Use the following compile options; always optimize for speed
target_compile_options(hash
    PRIVATE
        $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>: -Wpedantic -Wextra -Wall>
        $<$<CXX_COMPILER_ID:MSVC>: >)

# Link against library dependencies
target_link_libraries(hash PRIVATE Terra::secutil Terra::bitutil)

# Install target and associated include files
if(libhash_INSTALL)
    include(GNUInstallDirs)
    install(TARGETS hash EXPORT libhashTargets ARCHIVE)
    install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ TYPE INCLUDE)
    install(EXPORT libhashTargets
            FILE libhashConfig.cmake
            NAMESPACE Terra::
            DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/libhash)
endif()
