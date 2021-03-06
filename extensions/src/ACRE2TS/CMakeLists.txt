cmake_minimum_required (VERSION 3.0)

set(ACRE_NAME "ACRE2TS")

acre_set_build_output()
acre_set_linker_options()

file(GLOB_RECURSE SOURCES *.h *.hpp *.c *.cpp inc/*)

include_directories(inc)

add_library( ${ACRE_NAME} MODULE ${SOURCES} ${GLOBAL_SOURCES})
target_link_libraries(${ACRE_NAME} ACRE2Core ACRE2Shared x3daudio)
set_target_properties(${ACRE_NAME} PROPERTIES FOLDER ACRE2)


# Copy and rename
if(USE_64BIT_BUILD)
    set(FINAL_DLL_NAME acre2_win64.dll)
else()
    set(FINAL_DLL_NAME acre2_win32.dll)
endif()

add_custom_command(TARGET ${ACRE_NAME} POST_BUILD
    # Copy DLL to plugins
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${ACRE_NAME}> ${PROJECT_SOURCE_DIR}/../plugin/${FINAL_DLL_NAME}
    # Copy PDB to symbols
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE_DIR:${ACRE_NAME}>/${ACRE_NAME}.pdb ${PROJECT_SOURCE_DIR}/../symbols/${ACRE_ARCH}/${ACRE_NAME}.pdb
)
