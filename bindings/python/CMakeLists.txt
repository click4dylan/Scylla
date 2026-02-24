set(PYBIND11_FINDPYTHON ON)
find_package(pybind11 CONFIG REQUIRED)

pybind11_add_module(pyscylla "module.cpp")

target_link_libraries(pyscylla PRIVATE ScyllaLib)

set_target_properties(pyscylla PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bindings/python/$<CONFIG>"
)