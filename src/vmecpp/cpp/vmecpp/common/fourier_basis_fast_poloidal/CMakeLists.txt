target_precompile_headers (vmecpp_core

                           PRIVATE

                           $<$<BOOL:${USE_PCH}>:$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/fourier_basis_fast_poloidal.h>>
)

target_sources (vmecpp_core

                PRIVATE

                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/fourier_basis_fast_poloidal.cc>
)
