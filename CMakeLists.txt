cmake_minimum_required(VERSION 3.16)
project(cylog)

set(SRCS        
        src/cylog.cpp
        src/cylog_generic.cpp
)

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    add_executable(cylog ${SRCS})
    target_sources(cylog PRIVATE main.cpp)    
    add_compile_definitions(USE_SYSTEM_LINUX)
    message("CYLOG_SYSTEM is linux")
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Generic")
    add_library(cylog STATIC ${SRCS})
    add_compile_definitions(USE_SYSTEM_GENERIC)
    message("CYLOG_SYSTEM is generic")
else()
    message(ERROR "No system matched")
endif()

target_include_directories(cylog
                PRIVATE         ./
                INTERFACE       inc
)
