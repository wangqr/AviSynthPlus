# We need CMake 3.8 at least, because we require
# CMAKE_CXX_STANDARD to be set to C++17.
CMAKE_MINIMUM_REQUIRED( VERSION 3.8.2 )

set(PluginName "TimeStretch")
set(ProjectName "Plugin${PluginName}")

# Create library
project(${ProjectName})
list (APPEND SourceFiles
    "TimeStretch.cpp"
)
add_library(${ProjectName} SHARED ${SourceFiles})
set_target_properties(${ProjectName} PROPERTIES "OUTPUT_NAME" ${PluginName})
if (MINGW)
  set_target_properties(${ProjectName} PROPERTIES PREFIX "")
  set_target_properties(${ProjectName} PROPERTIES IMPORT_PREFIX "")
endif()

# Library dependencies 
add_subdirectory("SoundTouch")
target_link_libraries(${ProjectName} "SoundTouch")

# Include directories
target_include_directories(${ProjectName} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR} ${AvsCore_SOURCE_DIR})

if (MSVC_IDE)    
  # Copy output to a common folder for easy deployment
  add_custom_command(
    TARGET ${ProjectName}
    POST_BUILD
    COMMAND xcopy /Y \"$(TargetPath)\" \"${CMAKE_BINARY_DIR}/Output/plugins\"
  )
endif()

INSTALL(TARGETS "${ProjectName}"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/avisynth")
