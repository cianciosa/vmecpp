cmake_minimum_required(VERSION 3.18)
project(vmecpp C CXX)

#-------------------------------------------------------------------------------
#  Build Options
#-------------------------------------------------------------------------------
option (USE_PCH "Enable the use of precompiled headers" ON)

#-------------------------------------------------------------------------------
#  Setup build types
#-------------------------------------------------------------------------------
if (NOT CMAKE_BUILD_TYPE)
    set (CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
endif ()
set_property (CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
              Debug
              Release
              MinSizeRel
              RelWithDebInfo
)

#-------------------------------------------------------------------------------
#  Dependencies.
#-------------------------------------------------------------------------------
find_package (HDF5 REQUIRED COMPONENTS C CXX)
find_package (netCDF REQUIRED)
find_package (Eigen3 REQUIRED)
find_package (nlohmann_json REQUIRED)
find_package (LAPACK REQUIRED)
find_package (OpenMP)

# use ccache if available
find_program (CCACHE_COMMAND NAMES ccache ccache-swig)
if (EXISTS ${CCACHE_COMMAND})
  message (STATUS "Found ccache: ${CCACHE_COMMAND}")
  set (CMAKE_CXX_COMPILER_LAUNCHER ${CCACHE_COMMAND})
else ()
  message (STATUS "Could NOT find ccache")
endif ()

#-------------------------------------------------------------------------------
#  Setup targets
#-------------------------------------------------------------------------------
#  Its unfortunate that we need to set this here globally because of abseil-cpp.
#  Cmake build targets should be self constained.
set (CMAKE_CXX_STANDARD 20)
add_subdirectory (abseil-cpp)
unset (CMAKE_CXX_STANDARD)
add_subdirectory (indata2json)

add_library (vmecpp_core STATIC)

target_compile_features (vmecpp_core

                         PUBLIC

                         cxx_std_20
)

target_compile_options (vmecpp_core

                        PUBLIC

                        -fPIC
                        -Wall
                        -Wextra

                        $<$<CONFIG:Release>:-fno-math-errno>
)

target_include_directories (vmecpp_core

                            PRIVATE

                            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/abscab-cpp>
)

target_precompile_headers (vmecpp_core

                           PRIVATE

                           $<$<BOOL:${USE_PCH}>:$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/abscab-cpp/abscab/abscab.hh>>
)

target_sources (vmecpp_core

                PRIVATE

                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/abscab-cpp/abscab/abscab.cc>
)

target_link_libraries (vmecpp_core

                       PRIVATE

                       netCDF::netcdf
                       hdf5::hdf5_cpp
                       LAPACK::LAPACK
                       $<$<BOOL:${OpenMP_CXX_FOUND}>:OpenMP::OpenMP_CXX>
                       nlohmann_json::nlohmann_json
                       absl::log
                       absl::absl_check
                       absl::status
                       absl::algorithm
                       absl::base
                       absl::synchronization
                       absl::str_format
                       absl::string_view
                       absl::status
                       absl::statusor

                       PUBLIC

                       absl::strings
                       Eigen3::Eigen
)

add_executable (vmec_standalone)

target_link_libraries (vmec_standalone

                       PRIVATE

                       vmecpp_core
)
if (${USE_PCH})
    target_precompile_headers (vmec_standalone REUSE_FROM vmecpp_core)
endif ()

# Now add the pybind11 module for VMEC++.
add_subdirectory(pybind11)
pybind11_add_module(_vmecpp)
target_link_libraries(_vmecpp PRIVATE vmecpp_core)
if (${USE_PCH})
    target_precompile_headers (_vmecpp REUSE_FROM vmecpp_core)
endif ()

add_subdirectory (src)

install(TARGETS _vmecpp LIBRARY DESTINATION vmecpp/cpp/.)
install(TARGETS indata2json DESTINATION vmecpp/cpp/third_party/indata2json/)
