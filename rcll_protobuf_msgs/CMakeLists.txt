cmake_minimum_required(VERSION 3.8)
project(rcll_protobuf_msgs)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

function(update_submodules)
  # Read the .gitmodules file
  file(READ "${CMAKE_SOURCE_DIR}/../.gitmodules" gitmodules_content)

  # Initialize an empty list to store submodule paths
  set(submodule_paths "")

  # Use a regex to extract the paths of the submodules
  string(REGEX MATCHALL "path = ([^\n]+)" matches "${gitmodules_content}")

  # Iterate over the matches to extract the submodule paths
  foreach(match ${matches})
    string(REGEX REPLACE "path = " "" submodule_path "${match}")
    list(APPEND submodule_paths "${CMAKE_SOURCE_DIR}/../${submodule_path}")
  endforeach()

  # Display the submodule paths for debugging purposes
  message(STATUS "Submodule paths: ${submodule_paths}")

  # Update each submodule
  foreach(submodule_path ${submodule_paths})
    if( NOT EXISTS "${submodule_path}/.git" )
      execute_process(
        COMMAND git submodule update --init --recursive ${submodule_path}
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/../
      )

      if (result)
        message(FATAL_ERROR "Error at submodule ${submodule_path}: ${error}")
      else()
        message(STATUS "Submodule ${submodule_path} updated successfully")
      endif()
    endif()
  endforeach()
endfunction()

find_package(ament_cmake REQUIRED)

update_submodules()

install(DIRECTORY rcll-protobuf-msgs DESTINATION share/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
