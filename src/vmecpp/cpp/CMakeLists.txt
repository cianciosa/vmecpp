target_include_directories (vmecpp_core

                            PUBLIC

                            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

add_subdirectory(util)
add_subdirectory(vmecpp)
