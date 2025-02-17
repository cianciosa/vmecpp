target_sources (_vmecpp

                PRIVATE

                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/pybind_vmec.cc>
                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/vmec_indata_pywrapper.cc>
)
