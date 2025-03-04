cmake_minimum_required(VERSION 3.10)
include_guard(GLOBAL)
include(${CMAKE_CURRENT_LIST_DIR}/../../modules/module.cmake)
fhb_include_module(common)
fhb_include_module(gpio)
fhb_include_module(kernelsdk)
fhb_include_module(interrupt)
project(steppermotor)

enable_language( C CXX ASM )
set(TARGET steppermotor)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(LINKER_SCRIPT ${CMAKE_CURRENT_LIST_DIR}/linker.ld)
include(${PROJECT_ROOT}/cmake/linker.cmake)

add_executable(${TARGET}.elf 
    "${CMAKE_CURRENT_LIST_DIR}/main.c"
    "${CMAKE_CURRENT_LIST_DIR}/start.S"
) 
target_link_libraries(${TARGET}.elf PUBLIC 
    common
    gpio
    kernelsdk
    interrupt
)

add_custom_command(TARGET ${TARGET}.elf POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}.elf" -O binary "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}.bin"
    COMMENT "Invoking: objcopy")

add_custom_command(TARGET ${TARGET}.elf POST_BUILD
    COMMAND ${CMAKE_OBJDUMP} -D "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}.elf"  > "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}.list"
    COMMENT "Invoking: objdump")


