target_precompile_headers (vmecpp_core

                           PRIVATE

                           $<$<BOOL:${USE_PCH}>:$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/external_magnetic_field.h>>
)

target_sources (vmecpp_core

                PRIVATE

                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/external_magnetic_field.cc>
)
