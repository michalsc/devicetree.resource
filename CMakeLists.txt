cmake_minimum_required(VERSION 3.30.0)

project(devicetree VERSION 1.5.0)

include(cmake/verstring.cmake)
include(cmake/sfdc.cmake)

get_verstring(VERSTRING)
sfdc(sfd/devicetree_lib.sfd)

add_executable(devicetree.resource
    src/main.c
    src/init.c
    src/closekey.c
    src/openkey.c
    src/findproperty.c
    src/getpropname.c
    src/getpropvalue.c
    src/getproplen.c
    src/getchild.c
    src/getproperty.c
    src/getparent.c
    src/addram.c
    src/getkeyname.c
)

target_compile_options(devicetree.resource PRIVATE -Os -m68040 -msmall-code -fomit-frame-pointer)
target_compile_definitions(devicetree.resource PRIVATE VERSION_STRING="${VERSTRING}")
target_include_directories(devicetree.resource PRIVATE include)
target_link_options(devicetree.resource PRIVATE -ffreestanding -nostdlib -nostartfiles -s -Wl,-e_rom_start -Wl,-T${CMAKE_CURRENT_SOURCE_DIR}/ldscript.lds)
target_link_libraries(devicetree.resource devicetree)

set_target_properties(devicetree.resource PROPERTIES LINK_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/ldscript.lds)
