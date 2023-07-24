add_subdirectory(XSBench)
add_subdirectory(Pathfinder)
add_subdirectory(miniAMR)
add_subdirectory(miniGMG)
# Skip RSBench on Apple ARM64 because of missing symbol link error.
if (NOT (APPLE AND ARCH STREQUAL "AArch64"))
  add_subdirectory(RSBench)
endif()
add_subdirectory(SimpleMOC)
