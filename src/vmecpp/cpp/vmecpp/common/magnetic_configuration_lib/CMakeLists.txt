target_precompile_headers (vmecpp_core

                           PRIVATE

                           $<$<BOOL:${USE_PCH}>:$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/magnetic_configuration_lib.h>>
)

target_sources (vmecpp_core

                PRIVATE

                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/magnetic_configuration_lib.cc>
)
