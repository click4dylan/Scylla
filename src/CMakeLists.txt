find_path(DISTORM_INCLUDE_DIR NAMES distorm.h REQUIRED)
find_library(DISTORM_LIBRARY_RELEASE NAMES distorm PATHS "${_VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET}/lib" NO_DEFAULT_PATH REQUIRED)
find_library(DISTORM_LIBRARY_DEBUG   NAMES distorm PATHS "${_VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET}/debug/lib" NO_DEFAULT_PATH REQUIRED)

add_library(distorm::distorm STATIC IMPORTED)
set_target_properties(distorm::distorm PROPERTIES
    IMPORTED_LOCATION                 "${DISTORM_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_RELEASE         "${DISTORM_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_RELWITHDEBINFO  "${DISTORM_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_MINSIZEREL      "${DISTORM_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_DEBUG           "${DISTORM_LIBRARY_DEBUG}"
    INTERFACE_INCLUDE_DIRECTORIES     "${DISTORM_INCLUDE_DIR}"
)


find_path(TINYXML_INCLUDE_DIR NAMES tinyxml.h REQUIRED)
find_library(TINYXML_LIBRARY_RELEASE NAMES tinyxml PATHS "${_VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET}/lib" NO_DEFAULT_PATH REQUIRED)
find_library(TINYXML_LIBRARY_DEBUG   NAMES tinyxml PATHS "${_VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET}/debug/lib" NO_DEFAULT_PATH REQUIRED)

add_library(tinyxml::tinyxml STATIC IMPORTED)
set_target_properties(tinyxml::tinyxml PROPERTIES
    IMPORTED_LOCATION                "${TINYXML_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_RELEASE        "${TINYXML_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_RELWITHDEBINFO "${TINYXML_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_MINSIZEREL     "${TINYXML_LIBRARY_RELEASE}"
    IMPORTED_LOCATION_DEBUG          "${TINYXML_LIBRARY_DEBUG}"
    INTERFACE_INCLUDE_DIRECTORIES    "${TINYXML_INCLUDE_DIR}"
)


find_path(WTL_INCLUDE_DIR NAMES atlres.h REQUIRED)

add_library(WTL::WTL INTERFACE IMPORTED)
set_target_properties(WTL::WTL PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${WTL_INCLUDE_DIR}"
)

set(CMAKE_RC_FLAGS "${CMAKE_RC_FLAGS} /I \"${WTL_INCLUDE_DIR}\"")

set(SCYLLA_HEADER_FILES
    "AboutGui.h"
    "AppInfo.h"
    "DeviceNameResolver.h "
    "DumpMemoryGui.h"
    "ImportRebuilder.h"
    "OptionsGui.h"
    "PluginLoader.h"
    "SystemInformation.h"
    "ApiReader.h"
    "DisassemblerGui.h"
    "DumpSectionGui.h"
    "ImportsHandling.h"
    "PeParser.h"
    "ProcessAccessHelp.h"
    "Thunks.h"
    "Architecture.h"
    "DllInjection.h"
    "FunctionExport.h"
    "Logger.h"
    "PeRebuild.h"
    "ProcessLister.h"
    "TreeImportExport.h"
    "Configuration.h"
    "DllInjectionPlugin.h"
    "IATReferenceScan.h"
    "MainGui.h"
    "PickApiGui.h"
    "Scylla.h"
    "hexedit.h"
    "ConfigurationHolder.h"
    "DonateGui.h"
    "IATSearch.h"
    "NativeWinApi.h"
    "PickDllGui.h"
    "StringConversion.h"
    "multitree.h"
)
set(SCYLLA_SRC_FILES
    "AboutGui.cpp"
    "ApiReader.cpp"
    "Architecture.cpp"
    "Configuration.cpp"
    "ConfigurationHolder.cpp"
    "DeviceNameResolver.cpp"
    "DisassemblerGui.cpp"
    "DllInjection.cpp"
    "DllInjectionPlugin.cpp"
    "DonateGui.cpp"
    "DumpMemoryGui.cpp"
    "DumpSectionGui.cpp"
    "FunctionExport.cpp"
    "IATReferenceScan.cpp"
    "IATSearch.cpp"
    "ImportRebuilder.cpp"
    "ImportsHandling.cpp"
    "Logger.cpp"
    "MainGui.cpp"
    "NativeWinApi.cpp"
    "OptionsGui.cpp"
    "PeParser.cpp"
    "PeRebuild.cpp"
    "PickApiGui.cpp"
    "PickDllGui.cpp"
    "PluginLoader.cpp"
    "ProcessAccessHelp.cpp"
    "ProcessLister.cpp"
    "Scylla.cpp"
    "StringConversion.cpp"
    "SystemInformation.cpp"
    "TreeImportExport.cpp"
)
set(SCYLLA_RSRC_FILES
    "resource.h"
    "MainGui.rc"
)

# ScyllaLib (static)
add_library(ScyllaLib STATIC
    ${SCYLLA_HEADER_FILES}
    ${SCYLLA_SRC_FILES}
)

target_include_directories(ScyllaLib PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
)

target_link_libraries(ScyllaLib PUBLIC
    distorm::distorm
    tinyxml::tinyxml
    WTL::WTL
)

target_compile_definitions(ScyllaLib PRIVATE
    APPNAME_S="${CMAKE_PROJECT_NAME}"
    APPVERSION_S="v${CMAKE_PROJECT_VERSION}"
)

# Scylla.dll
add_library(Scylla MODULE
    ${SCYLLA_RSRC_FILES}
    "main.cpp"
    "scylla_export_functions.def"
)

target_link_libraries(Scylla PRIVATE ScyllaLib)

set_target_properties(Scylla PROPERTIES
    OUTPUT_NAME "Scylla"
)

#ScyllaExe
add_executable(ScyllaExe WIN32
    ${SCYLLA_RSRC_FILES}
    "main.cpp"
)

target_link_libraries(ScyllaExe PRIVATE ScyllaLib)

set_target_properties(ScyllaExe PROPERTIES
    OUTPUT_NAME "Scylla"
)