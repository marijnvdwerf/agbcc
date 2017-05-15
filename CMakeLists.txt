cmake_minimum_required(VERSION 3.6)
project(agbcc)

set(CMAKE_CXX_STANDARD 11)

set(GCC_DIR "${CMAKE_SOURCE_DIR}/gcc")

set(RTL_SRCS
        ${GCC_DIR}/rtl.c
        ${GCC_DIR}/bitmap.c
        ${GCC_DIR}/obstack.c)

set(RTLANAL_SRCS
        ${GCC_DIR}/rtlanal.c)

set(PRINT_SRCS
        ${GCC_DIR}/print-rtl.c)

set(SRCS
        ${GCC_DIR}/toplev.c
        ${GCC_DIR}/version.c
        ${GCC_DIR}/tree.c
        ${GCC_DIR}/print-tree.c
        ${GCC_DIR}/stor-layout.c
        ${GCC_DIR}/fold-const.c
        ${GCC_DIR}/function.c
        ${GCC_DIR}/stmt.c
        ${GCC_DIR}/except.c
        ${GCC_DIR}/expr.c
        ${GCC_DIR}/calls.c
        ${GCC_DIR}/expmed.c
        ${GCC_DIR}/explow.c
        ${GCC_DIR}/optabs.c
        ${GCC_DIR}/varasm.c
        ${GCC_DIR}/emit-rtl.c
        ${CMAKE_CURRENT_BINARY_DIR}/genrtl.c
        ${GCC_DIR}/real.c
        ${GCC_DIR}/regmove.c
        ${GCC_DIR}/dwarf2out.c
        ${GCC_DIR}/alias.c
        ${GCC_DIR}/integrate.c
        ${GCC_DIR}/jump.c
        ${GCC_DIR}/cse.c
        ${GCC_DIR}/loop.c
        ${GCC_DIR}/unroll.c
        ${GCC_DIR}/flow.c
        ${GCC_DIR}/stupid.c
        ${GCC_DIR}/combine.c
        ${GCC_DIR}/varray.c
        ${GCC_DIR}/regclass.c
        ${GCC_DIR}/local-alloc.c
        ${GCC_DIR}/global.c
        ${GCC_DIR}/reload.c
        ${GCC_DIR}/reload1.c
        ${GCC_DIR}/caller-save.c
        ${GCC_DIR}/gcse.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-peep.c
        ${GCC_DIR}/final.c
        ${GCC_DIR}/recog.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-opinit.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-recog.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-extract.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-output.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-emit.c
        ${GCC_DIR}/lcm.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-attrtab.c
        ${GCC_DIR}/thumb.c
        ${GCC_DIR}/getpwd.c
        ${GCC_DIR}/convert.c
        ${GCC_DIR}/dyn-string.c
        ${GCC_DIR}/splay-tree.c
        ${GCC_DIR}/graph.c
        ${GCC_DIR}/sbitmap.c
        ${GCC_DIR}/resource.c
        ${GCC_DIR}/c-parse.c
        ${GCC_DIR}/c-lex.c
        ${GCC_DIR}/c-decl.c
        ${GCC_DIR}/c-typeck.c
        ${GCC_DIR}/c-convert.c
        ${GCC_DIR}/c-aux-info.c
        ${GCC_DIR}/c-common.c
        ${GCC_DIR}/c-iterate.c)

set(GENERATED
        ${CMAKE_CURRENT_BINARY_DIR}/genrtl.c
        ${CMAKE_CURRENT_BINARY_DIR}/genrtl.h
        ${CMAKE_CURRENT_BINARY_DIR}/insn-peep.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-opinit.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-recog.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-extract.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-output.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-emit.c
        ${CMAKE_CURRENT_BINARY_DIR}/insn-attrtab.c
        ${CMAKE_CURRENT_BINARY_DIR}/tree-check.h
        ${CMAKE_CURRENT_BINARY_DIR}/insn-config.h
        ${CMAKE_CURRENT_BINARY_DIR}/insn-flags.h
        ${CMAKE_CURRENT_BINARY_DIR}/insn-codes.h
        ${CMAKE_CURRENT_BINARY_DIR}/insn-attr.h
        )

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -std=gnu11 -m32")

set(MD_FILE ${GCC_DIR}/thumb.md)

# Include generated files
include_directories(${CMAKE_CURRENT_BINARY_DIR} ${GCC_DIR})


macro(generate_md_file target output)
    add_custom_command(
            OUTPUT ${output}
            COMMAND $<TARGET_FILE:${target}> ${MD_FILE} > ${CMAKE_BINARY_DIR}/${output}
            DEPENDS ${target} ${MD_FILE}
    )
endmacro()


add_executable(gengenrtl ${GCC_DIR}/gengenrtl.c)
add_custom_command(
        OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/genrtl.h ${CMAKE_CURRENT_BINARY_DIR}/genrtl.c
        COMMAND $<TARGET_FILE:gengenrtl> ${CMAKE_CURRENT_BINARY_DIR}/genrtl.h ${CMAKE_BINARY_DIR}/genrtl.c
        DEPENDS gengenrtl
)
add_custom_target(genrtl_h DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/genrtl.h)


add_executable(genemit ${GCC_DIR}/genemit.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genemit genrtl_h)
generate_md_file(genemit insn-emit.c)


add_executable(gencheck ${GCC_DIR}/gencheck.c)
add_custom_command(
        OUTPUT tree-check.h
        COMMAND $<TARGET_FILE:gencheck> > ${CMAKE_CURRENT_BINARY_DIR}/tree-check.h
        DEPENDS gencheck
)


add_executable(genpeep ${GCC_DIR}/genpeep.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genpeep genrtl_h)
generate_md_file(genpeep insn-peep.c)


add_executable(genopinit ${GCC_DIR}/genopinit.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genopinit genrtl_h)
generate_md_file(genopinit insn-opinit.c)


add_executable(genrecog ${GCC_DIR}/genrecog.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genrecog genrtl_h)
generate_md_file(genrecog insn-recog.c)


add_executable(genextract ${GCC_DIR}/genextract.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genextract genrtl_h insn-config_h)
generate_md_file(genextract insn-extract.c)


add_executable(genoutput ${GCC_DIR}/genoutput.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genoutput genrtl_h)
generate_md_file(genoutput insn-output.c)


add_executable(genattrtab ${GCC_DIR}/genattrtab.c ${RTL_SRCS} ${PRINT_SRCS} ${RTLANAL_SRCS})
add_dependencies(genattrtab genrtl_h insn-config_h)
generate_md_file(genattrtab insn-attrtab.c)


add_executable(genconfig ${GCC_DIR}/genconfig.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genconfig genrtl_h)
generate_md_file(genconfig insn-config.h)
add_custom_target(insn-config_h DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/insn-config.h)


add_executable(genflags ${GCC_DIR}/genflags.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genflags genrtl_h)
generate_md_file(genflags insn-flags.h)


add_executable(gencodes ${GCC_DIR}/gencodes.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(gencodes genrtl_h)
generate_md_file(gencodes insn-codes.h)


add_executable(genattr ${GCC_DIR}/genattr.c ${RTL_SRCS} ${PRINT_SRCS})
add_dependencies(genattr genrtl_h)
generate_md_file(genattr insn-attr.h)


add_custom_target(generate_files
        DEPENDS ${GENERATED}
)


add_executable(agbcc_old ${SRCS} ${RTL_SRCS} ${PRINT_SRCS} ${RTLANAL_SRCS})
target_compile_definitions(agbcc_old PRIVATE -DOLD_COMPILER)
add_dependencies(agbcc_old generate_files)


add_executable(agbcc ${SRCS} ${RTL_SRCS} ${PRINT_SRCS} ${RTLANAL_SRCS})
add_dependencies(agbcc generate_files)
