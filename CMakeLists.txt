cmake_minimum_required(VERSION 2.6)
project(V8)


##########################################################################################
# Settings

if (NOT DEFINED V8_STATIC)
    set(V8_STATIC OFF CACHE BOOL "Build V8 as a static library")
endif()


##########################################################################################
# XMake-related settings

# Set the output paths
if (NOT DEFINED XMAKE_BINARY_DIR)
    set(XMAKE_BINARY_DIR "${V8_BINARY_DIR}")
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/lib")
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
endif()

# Set the dependencies path
if (NOT DEFINED XMAKE_DEPENDENCIES_DIR)
    set(XMAKE_DEPENDENCIES_DIR "${V8_SOURCE_DIR}")
endif()


##########################################################################################
# XMake importation

if (NOT EXISTS "${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")
    message(FATAL_ERROR
"Missing dependency: XMake
V8 requires XMake to compile. It is provided as a GIT submodule of this repository.
Did you forgot to execute the following commands?
   git submodule init
   git submodule update")
endif()

include("${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")


##########################################################################################
# Global definitions

add_definitions(-DENABLE_DEBUGGER_SUPPORT)


include(FindPythonInterp)
if (PYTHON_EXECUTABLE STREQUAL "PYTHON_EXECUTABLE-NOTFOUND")
    message(FATAL_ERROR "Python is required to generate some tools needed during the compilation.
If you have it installed on your system, and CMake didn't found it, you can set the PYTHON_EXECUTABLE variable.")
endif()


##########################################################################################
# v8_base library

# List the include paths
include_directories("${V8_SOURCE_DIR}/src")

# List the source files
set(SRCS_V8_BASE "${V8_SOURCE_DIR}/src/accessors.cc"
                 "${V8_SOURCE_DIR}/src/accessors.h"
                 "${V8_SOURCE_DIR}/src/allocation.cc"
                 "${V8_SOURCE_DIR}/src/allocation.h"
                 "${V8_SOURCE_DIR}/src/api.cc"
                 "${V8_SOURCE_DIR}/src/api.h"
                 "${V8_SOURCE_DIR}/src/apiutils.h"
                 "${V8_SOURCE_DIR}/src/arguments.h"
                 "${V8_SOURCE_DIR}/src/assembler.cc"
                 "${V8_SOURCE_DIR}/src/assembler.h"
                 "${V8_SOURCE_DIR}/src/ast.cc"
                 "${V8_SOURCE_DIR}/src/ast.h"
                 "${V8_SOURCE_DIR}/src/atomicops_internals_x86_gcc.cc"
                 "${V8_SOURCE_DIR}/src/bignum.cc"
                 "${V8_SOURCE_DIR}/src/bignum.h"
                 "${V8_SOURCE_DIR}/src/bignum-dtoa.cc"
                 "${V8_SOURCE_DIR}/src/bignum-dtoa.h"
                 "${V8_SOURCE_DIR}/src/bootstrapper.cc"
                 "${V8_SOURCE_DIR}/src/bootstrapper.h"
                 "${V8_SOURCE_DIR}/src/builtins.cc"
                 "${V8_SOURCE_DIR}/src/builtins.h"
                 "${V8_SOURCE_DIR}/src/bytecodes-irregexp.h"
                 "${V8_SOURCE_DIR}/src/cached-powers.cc"
                 "${V8_SOURCE_DIR}/src/cached-powers.h"
                 "${V8_SOURCE_DIR}/src/char-predicates-inl.h"
                 "${V8_SOURCE_DIR}/src/char-predicates.h"
                 "${V8_SOURCE_DIR}/src/checks.cc"
                 "${V8_SOURCE_DIR}/src/checks.h"
                 "${V8_SOURCE_DIR}/src/circular-queue-inl.h"
                 "${V8_SOURCE_DIR}/src/circular-queue.cc"
                 "${V8_SOURCE_DIR}/src/circular-queue.h"
                 "${V8_SOURCE_DIR}/src/code-stubs.cc"
                 "${V8_SOURCE_DIR}/src/code-stubs.h"
                 "${V8_SOURCE_DIR}/src/code.h"
                 "${V8_SOURCE_DIR}/src/codegen.cc"
                 "${V8_SOURCE_DIR}/src/codegen.h"
                 "${V8_SOURCE_DIR}/src/compilation-cache.cc"
                 "${V8_SOURCE_DIR}/src/compilation-cache.h"
                 "${V8_SOURCE_DIR}/src/compiler.cc"
                 "${V8_SOURCE_DIR}/src/compiler.h"
                 "${V8_SOURCE_DIR}/src/contexts.cc"
                 "${V8_SOURCE_DIR}/src/contexts.h"
                 "${V8_SOURCE_DIR}/src/conversions-inl.h"
                 "${V8_SOURCE_DIR}/src/conversions.cc"
                 "${V8_SOURCE_DIR}/src/conversions.h"
                 "${V8_SOURCE_DIR}/src/counters.cc"
                 "${V8_SOURCE_DIR}/src/counters.h"
                 "${V8_SOURCE_DIR}/src/cpu.h"
                 "${V8_SOURCE_DIR}/src/cpu-profiler-inl.h"
                 "${V8_SOURCE_DIR}/src/cpu-profiler.cc"
                 "${V8_SOURCE_DIR}/src/cpu-profiler.h"
                 "${V8_SOURCE_DIR}/src/data-flow.cc"
                 "${V8_SOURCE_DIR}/src/data-flow.h"
                 "${V8_SOURCE_DIR}/src/dateparser.cc"
                 "${V8_SOURCE_DIR}/src/dateparser.h"
                 "${V8_SOURCE_DIR}/src/dateparser-inl.h"
                 "${V8_SOURCE_DIR}/src/debug.cc"
                 "${V8_SOURCE_DIR}/src/debug.h"
                 "${V8_SOURCE_DIR}/src/debug-agent.cc"
                 "${V8_SOURCE_DIR}/src/debug-agent.h"
                 "${V8_SOURCE_DIR}/src/deoptimizer.cc"
                 "${V8_SOURCE_DIR}/src/deoptimizer.h"
                 "${V8_SOURCE_DIR}/src/disasm.h"
                 "${V8_SOURCE_DIR}/src/disassembler.cc"
                 "${V8_SOURCE_DIR}/src/disassembler.h"
                 "${V8_SOURCE_DIR}/src/diy-fp.cc"
                 "${V8_SOURCE_DIR}/src/diy-fp.h"
                 "${V8_SOURCE_DIR}/src/double.h"
                 "${V8_SOURCE_DIR}/src/dtoa.cc"
                 "${V8_SOURCE_DIR}/src/dtoa.h"
                 "${V8_SOURCE_DIR}/src/elements.cc"
                 "${V8_SOURCE_DIR}/src/elements.h"
                 "${V8_SOURCE_DIR}/src/execution.cc"
                 "${V8_SOURCE_DIR}/src/execution.h"
                 "${V8_SOURCE_DIR}/src/factory.cc"
                 "${V8_SOURCE_DIR}/src/factory.h"
                 "${V8_SOURCE_DIR}/src/fast-dtoa.cc"
                 "${V8_SOURCE_DIR}/src/fast-dtoa.h"
                 "${V8_SOURCE_DIR}/src/flag-definitions.h"
                 "${V8_SOURCE_DIR}/src/fixed-dtoa.cc"
                 "${V8_SOURCE_DIR}/src/fixed-dtoa.h"
                 "${V8_SOURCE_DIR}/src/flags.cc"
                 "${V8_SOURCE_DIR}/src/flags.h"
                 "${V8_SOURCE_DIR}/src/frames-inl.h"
                 "${V8_SOURCE_DIR}/src/frames.cc"
                 "${V8_SOURCE_DIR}/src/frames.h"
                 "${V8_SOURCE_DIR}/src/full-codegen.cc"
                 "${V8_SOURCE_DIR}/src/full-codegen.h"
                 "${V8_SOURCE_DIR}/src/func-name-inferrer.cc"
                 "${V8_SOURCE_DIR}/src/func-name-inferrer.h"
                 "${V8_SOURCE_DIR}/src/global-handles.cc"
                 "${V8_SOURCE_DIR}/src/global-handles.h"
                 "${V8_SOURCE_DIR}/src/globals.h"
                 "${V8_SOURCE_DIR}/src/handles-inl.h"
                 "${V8_SOURCE_DIR}/src/handles.cc"
                 "${V8_SOURCE_DIR}/src/handles.h"
                 "${V8_SOURCE_DIR}/src/hashmap.cc"
                 "${V8_SOURCE_DIR}/src/hashmap.h"
                 "${V8_SOURCE_DIR}/src/heap-inl.h"
                 "${V8_SOURCE_DIR}/src/heap.cc"
                 "${V8_SOURCE_DIR}/src/heap.h"
                 "${V8_SOURCE_DIR}/src/heap-profiler.cc"
                 "${V8_SOURCE_DIR}/src/heap-profiler.h"
                 "${V8_SOURCE_DIR}/src/hydrogen.cc"
                 "${V8_SOURCE_DIR}/src/hydrogen.h"
                 "${V8_SOURCE_DIR}/src/hydrogen-instructions.cc"
                 "${V8_SOURCE_DIR}/src/hydrogen-instructions.h"
                 "${V8_SOURCE_DIR}/src/ic-inl.h"
                 "${V8_SOURCE_DIR}/src/ic.cc"
                 "${V8_SOURCE_DIR}/src/ic.h"
                 "${V8_SOURCE_DIR}/src/incremental-marking.cc"
                 "${V8_SOURCE_DIR}/src/incremental-marking.h"
                 "${V8_SOURCE_DIR}/src/inspector.cc"
                 "${V8_SOURCE_DIR}/src/inspector.h"
                 "${V8_SOURCE_DIR}/src/interpreter-irregexp.cc"
                 "${V8_SOURCE_DIR}/src/interpreter-irregexp.h"
                 "${V8_SOURCE_DIR}/src/json-parser.h"
                 "${V8_SOURCE_DIR}/src/jsregexp.cc"
                 "${V8_SOURCE_DIR}/src/jsregexp.h"
                 "${V8_SOURCE_DIR}/src/isolate.cc"
                 "${V8_SOURCE_DIR}/src/isolate.h"
                 "${V8_SOURCE_DIR}/src/list-inl.h"
                 "${V8_SOURCE_DIR}/src/list.h"
                 "${V8_SOURCE_DIR}/src/lithium.cc"
                 "${V8_SOURCE_DIR}/src/lithium.h"
                 "${V8_SOURCE_DIR}/src/lithium-allocator.cc"
                 "${V8_SOURCE_DIR}/src/lithium-allocator.h"
                 "${V8_SOURCE_DIR}/src/lithium-allocator-inl.h"
                 "${V8_SOURCE_DIR}/src/liveedit.cc"
                 "${V8_SOURCE_DIR}/src/liveedit.h"
                 "${V8_SOURCE_DIR}/src/liveobjectlist-inl.h"
                 "${V8_SOURCE_DIR}/src/liveobjectlist.cc"
                 "${V8_SOURCE_DIR}/src/liveobjectlist.h"
                 "${V8_SOURCE_DIR}/src/log-inl.h"
                 "${V8_SOURCE_DIR}/src/log-utils.cc"
                 "${V8_SOURCE_DIR}/src/log-utils.h"
                 "${V8_SOURCE_DIR}/src/log.cc"
                 "${V8_SOURCE_DIR}/src/log.h"
                 "${V8_SOURCE_DIR}/src/macro-assembler.h"
                 "${V8_SOURCE_DIR}/src/mark-compact.cc"
                 "${V8_SOURCE_DIR}/src/mark-compact.h"
                 "${V8_SOURCE_DIR}/src/messages.cc"
                 "${V8_SOURCE_DIR}/src/messages.h"
                 "${V8_SOURCE_DIR}/src/natives.h"
                 "${V8_SOURCE_DIR}/src/objects-debug.cc"
                 "${V8_SOURCE_DIR}/src/objects-printer.cc"
                 "${V8_SOURCE_DIR}/src/objects-inl.h"
                 "${V8_SOURCE_DIR}/src/objects-visiting.cc"
                 "${V8_SOURCE_DIR}/src/objects-visiting.h"
                 "${V8_SOURCE_DIR}/src/objects.cc"
                 "${V8_SOURCE_DIR}/src/objects.h"
                 "${V8_SOURCE_DIR}/src/parser.cc"
                 "${V8_SOURCE_DIR}/src/parser.h"
                 "${V8_SOURCE_DIR}/src/platform-tls-mac.h"
                 "${V8_SOURCE_DIR}/src/platform-tls-win32.h"
                 "${V8_SOURCE_DIR}/src/platform-tls.h"
                 "${V8_SOURCE_DIR}/src/platform.h"
                 "${V8_SOURCE_DIR}/src/preparse-data-format.h"
                 "${V8_SOURCE_DIR}/src/preparse-data.cc"
                 "${V8_SOURCE_DIR}/src/preparse-data.h"
                 "${V8_SOURCE_DIR}/src/preparser.cc"
                 "${V8_SOURCE_DIR}/src/preparser.h"
                 "${V8_SOURCE_DIR}/src/prettyprinter.cc"
                 "${V8_SOURCE_DIR}/src/prettyprinter.h"
                 "${V8_SOURCE_DIR}/src/property.cc"
                 "${V8_SOURCE_DIR}/src/property.h"
                 "${V8_SOURCE_DIR}/src/property-details.h"
                 "${V8_SOURCE_DIR}/src/profile-generator-inl.h"
                 "${V8_SOURCE_DIR}/src/profile-generator.cc"
                 "${V8_SOURCE_DIR}/src/profile-generator.h"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler-irregexp-inl.h"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler-irregexp.cc"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler-irregexp.h"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler-tracer.cc"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler-tracer.h"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler.cc"
                 "${V8_SOURCE_DIR}/src/regexp-macro-assembler.h"
                 "${V8_SOURCE_DIR}/src/regexp-stack.cc"
                 "${V8_SOURCE_DIR}/src/regexp-stack.h"
                 "${V8_SOURCE_DIR}/src/rewriter.cc"
                 "${V8_SOURCE_DIR}/src/rewriter.h"
                 "${V8_SOURCE_DIR}/src/runtime.cc"
                 "${V8_SOURCE_DIR}/src/runtime.h"
                 "${V8_SOURCE_DIR}/src/runtime-profiler.cc"
                 "${V8_SOURCE_DIR}/src/runtime-profiler.h"
                 "${V8_SOURCE_DIR}/src/safepoint-table.cc"
                 "${V8_SOURCE_DIR}/src/safepoint-table.h"
                 "${V8_SOURCE_DIR}/src/scanner.cc"
                 "${V8_SOURCE_DIR}/src/scanner.h"
                 "${V8_SOURCE_DIR}/src/scanner-character-streams.cc"
                 "${V8_SOURCE_DIR}/src/scanner-character-streams.h"
                 "${V8_SOURCE_DIR}/src/scopeinfo.cc"
                 "${V8_SOURCE_DIR}/src/scopeinfo.h"
                 "${V8_SOURCE_DIR}/src/scopes.cc"
                 "${V8_SOURCE_DIR}/src/scopes.h"
                 "${V8_SOURCE_DIR}/src/serialize.cc"
                 "${V8_SOURCE_DIR}/src/serialize.h"
                 "${V8_SOURCE_DIR}/src/small-pointer-list.h"
                 "${V8_SOURCE_DIR}/src/smart-array-pointer.h"
                 "${V8_SOURCE_DIR}/src/snapshot-common.cc"
                 "${V8_SOURCE_DIR}/src/snapshot.h"
                 "${V8_SOURCE_DIR}/src/spaces-inl.h"
                 "${V8_SOURCE_DIR}/src/spaces.cc"
                 "${V8_SOURCE_DIR}/src/spaces.h"
                 "${V8_SOURCE_DIR}/src/store-buffer-inl.h"
                 "${V8_SOURCE_DIR}/src/store-buffer.cc"
                 "${V8_SOURCE_DIR}/src/store-buffer.h"
                 "${V8_SOURCE_DIR}/src/string-search.cc"
                 "${V8_SOURCE_DIR}/src/string-search.h"
                 "${V8_SOURCE_DIR}/src/string-stream.cc"
                 "${V8_SOURCE_DIR}/src/string-stream.h"
                 "${V8_SOURCE_DIR}/src/strtod.cc"
                 "${V8_SOURCE_DIR}/src/strtod.h"
                 "${V8_SOURCE_DIR}/src/stub-cache.cc"
                 "${V8_SOURCE_DIR}/src/stub-cache.h"
                 "${V8_SOURCE_DIR}/src/token.cc"
                 "${V8_SOURCE_DIR}/src/token.h"
                 "${V8_SOURCE_DIR}/src/type-info.cc"
                 "${V8_SOURCE_DIR}/src/type-info.h"
                 "${V8_SOURCE_DIR}/src/unbound-queue-inl.h"
                 "${V8_SOURCE_DIR}/src/unbound-queue.h"
                 "${V8_SOURCE_DIR}/src/unicode-inl.h"
                 "${V8_SOURCE_DIR}/src/unicode.cc"
                 "${V8_SOURCE_DIR}/src/unicode.h"
                 "${V8_SOURCE_DIR}/src/utils-inl.h"
                 "${V8_SOURCE_DIR}/src/utils.cc"
                 "${V8_SOURCE_DIR}/src/utils.h"
                 "${V8_SOURCE_DIR}/src/v8-counters.cc"
                 "${V8_SOURCE_DIR}/src/v8-counters.h"
                 "${V8_SOURCE_DIR}/src/v8.cc"
                 "${V8_SOURCE_DIR}/src/v8.h"
                 "${V8_SOURCE_DIR}/src/v8checks.h"
                 "${V8_SOURCE_DIR}/src/v8conversions.cc"
                 "${V8_SOURCE_DIR}/src/v8conversions.h"
                 "${V8_SOURCE_DIR}/src/v8globals.h"
                 "${V8_SOURCE_DIR}/src/v8memory.h"
                 "${V8_SOURCE_DIR}/src/v8threads.cc"
                 "${V8_SOURCE_DIR}/src/v8threads.h"
                 "${V8_SOURCE_DIR}/src/v8utils.cc"
                 "${V8_SOURCE_DIR}/src/v8utils.h"
                 "${V8_SOURCE_DIR}/src/variables.cc"
                 "${V8_SOURCE_DIR}/src/variables.h"
                 "${V8_SOURCE_DIR}/src/version.cc"
                 "${V8_SOURCE_DIR}/src/version.h"
                 "${V8_SOURCE_DIR}/src/vm-state-inl.h"
                 "${V8_SOURCE_DIR}/src/vm-state.h"
                 "${V8_SOURCE_DIR}/src/zone-inl.h"
                 "${V8_SOURCE_DIR}/src/zone.cc"
                 "${V8_SOURCE_DIR}/src/zone.h"
                 "${V8_SOURCE_DIR}/src/extensions/externalize-string-extension.cc"
                 "${V8_SOURCE_DIR}/src/extensions/externalize-string-extension.h"
                 "${V8_SOURCE_DIR}/src/extensions/gc-extension.cc"
                 "${V8_SOURCE_DIR}/src/extensions/gc-extension.h"
)


set(SRCS_V8_BASE_IA32 "${V8_SOURCE_DIR}/src/ia32/assembler-ia32-inl.h"
                      "${V8_SOURCE_DIR}/src/ia32/assembler-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/assembler-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/builtins-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/code-stubs-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/code-stubs-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/codegen-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/codegen-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/cpu-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/debug-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/deoptimizer-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/disasm-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/frames-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/frames-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/full-codegen-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/ic-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-codegen-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-codegen-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-gap-resolver-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-gap-resolver-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/lithium-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/macro-assembler-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/macro-assembler-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/regexp-macro-assembler-ia32.cc"
                      "${V8_SOURCE_DIR}/src/ia32/regexp-macro-assembler-ia32.h"
                      "${V8_SOURCE_DIR}/src/ia32/stub-cache-ia32.cc"
)


set(SRCS_V8_BASE_X64 "${V8_SOURCE_DIR}/src/x64/assembler-x64-inl.h"
                     "${V8_SOURCE_DIR}/src/x64/assembler-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/assembler-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/builtins-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/code-stubs-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/code-stubs-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/codegen-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/codegen-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/cpu-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/debug-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/deoptimizer-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/disasm-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/frames-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/frames-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/full-codegen-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/ic-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/lithium-codegen-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/lithium-codegen-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/lithium-gap-resolver-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/lithium-gap-resolver-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/lithium-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/lithium-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/macro-assembler-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/macro-assembler-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/regexp-macro-assembler-x64.cc"
                     "${V8_SOURCE_DIR}/src/x64/regexp-macro-assembler-x64.h"
                     "${V8_SOURCE_DIR}/src/x64/stub-cache-x64.cc"
)


if (APPLE)
    set(SRCS_V8_BASE_PLATFORM "${V8_SOURCE_DIR}/src/platform-macos.cc"
                              "${V8_SOURCE_DIR}/src/platform-posix.cc"
    )
else()
    if (WIN32)
        set(SRCS_V8_BASE_PLATFORM "${V8_SOURCE_DIR}/src/platform-win32.cc"
                                  "${V8_SOURCE_DIR}/src/win32-math.cc"
        )
    else()
        set(SRCS_V8_BASE_PLATFORM "${V8_SOURCE_DIR}/src/platform-linux.cc"
                                  "${V8_SOURCE_DIR}/src/platform-posix.cc"
        )
    endif()
endif()


set(SRCS_V8_BASE_ALL ${SRCS_V8_BASE} ${SRCS_V8_BASE_IA32} ${SRCS_V8_BASE_X64} ${SRCS_V8_BASE_PLATFORM})


# Declaration of the library
xmake_create_static_library(V8_BASE v8_base ${SRCS_V8_BASE_ALL})
xmake_export_include_paths(V8_BASE "${V8_SOURCE_DIR}/src")

if (UNIX)
    xmake_export_link_flags(V8_BASE "-lpthread")
endif()

if (WIN32)
    xmake_export_link_flags(V8_BASE "Ws2_32.lib Winmm.lib")
endif()

# Disable some warnings in Visual Studio
xmake_disable_vs_warning(V8_BASE 4800 YES)
xmake_disable_vs_warning(V8_BASE 4355 YES)
xmake_disable_vs_warning(V8_BASE 4351 NO)


##########################################################################################
# js2c

set(LIBRARY_FILES "${V8_SOURCE_DIR}/src/runtime.js"
                  "${V8_SOURCE_DIR}/src/v8natives.js"
                  "${V8_SOURCE_DIR}/src/array.js"
                  "${V8_SOURCE_DIR}/src/string.js"
                  "${V8_SOURCE_DIR}/src/uri.js"
                  "${V8_SOURCE_DIR}/src/math.js"
                  "${V8_SOURCE_DIR}/src/messages.js"
                  "${V8_SOURCE_DIR}/src/apinatives.js"
                  "${V8_SOURCE_DIR}/src/debug-debugger.js"
                  "${V8_SOURCE_DIR}/src/mirror-debugger.js"
                  "${V8_SOURCE_DIR}/src/liveedit-debugger.js"
                  "${V8_SOURCE_DIR}/src/date.js"
                  "${V8_SOURCE_DIR}/src/json.js"
                  "${V8_SOURCE_DIR}/src/regexp.js"
                  "${V8_SOURCE_DIR}/src/macros.py"
)

file(MAKE_DIRECTORY "${XMAKE_BINARY_DIR}/generated/v8/")

add_custom_command(OUTPUT "${XMAKE_BINARY_DIR}/generated/v8/libraries.cc"
                   COMMAND "${PYTHON_EXECUTABLE}" "${V8_SOURCE_DIR}/tools/js2c.py" "${XMAKE_BINARY_DIR}/generated/v8/libraries.cc" "CORE" "off" ${LIBRARY_FILES}
                   WORKING_DIRECTORY "${V8_SOURCE_DIR}"
                   COMMENT "js2c..."
                   VERBATIM)


##########################################################################################
# js2c-experimental

set(LIBRARY_EXPERIMENTAL_FILES "${V8_SOURCE_DIR}/src/macros.py"
                               "${V8_SOURCE_DIR}/src/proxy.js"
                               "${V8_SOURCE_DIR}/src/collection.js"
)

add_custom_command(OUTPUT "${XMAKE_BINARY_DIR}/generated/v8/experimental-libraries.cc"
                   COMMAND "${PYTHON_EXECUTABLE}" "${V8_SOURCE_DIR}/tools/js2c.py" "${XMAKE_BINARY_DIR}/generated/v8/experimental-libraries.cc" "EXPERIMENTAL" "off" ${LIBRARY_EXPERIMENTAL_FILES}
                   WORKING_DIRECTORY "${V8_SOURCE_DIR}"
                   COMMENT "js2c-experimental..."
                   VERBATIM)


##########################################################################################
# v8_nosnapshot library

set(SRCS_V8_NOSNAPSHOT "${XMAKE_BINARY_DIR}/generated/v8/libraries.cc"
                       "${XMAKE_BINARY_DIR}/generated/v8/experimental-libraries.cc"
                       "${V8_SOURCE_DIR}/src/snapshot-empty.cc"
)

# Declaration of the library
xmake_create_static_library(V8_NOSNAPSHOT v8_nosnapshot ${SRCS_V8_NOSNAPSHOT})
xmake_export_include_paths(V8_NOSNAPSHOT "${V8_SOURCE_DIR}/src")

# Disable some warnings in Visual Studio
xmake_disable_vs_warning(V8_NOSNAPSHOT 4800 YES)
xmake_disable_vs_warning(V8_NOSNAPSHOT 4355 YES)


##########################################################################################
# mksnapshot

xmake_create_executable(MKSNAPSHOT mksnapshot "${V8_SOURCE_DIR}/src/mksnapshot.cc")
xmake_project_link(MKSNAPSHOT V8_BASE V8_NOSNAPSHOT)

if (UNIX)
    xmake_link_libraries(MKSNAPSHOT pthread)
endif()

set_target_properties(mksnapshot PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/tools/v8/")

# Disable some warnings in Visual Studio
xmake_disable_vs_warning(MKSNAPSHOT 4800 YES)
xmake_disable_vs_warning(MKSNAPSHOT 4355 YES)

add_custom_command(OUTPUT "${XMAKE_BINARY_DIR}/generated/v8/snapshot.cc"
                   COMMAND mksnapshot "--log-snapshot-positions" "--logfile" "${XMAKE_BINARY_DIR}/generated/v8/snapshot.log" "${XMAKE_BINARY_DIR}/generated/v8/snapshot.cc"
                   DEPENDS mksnapshot
                   WORKING_DIRECTORY "${V8_SOURCE_DIR}"
                   COMMENT "mksnapshot..."
                   VERBATIM)


##########################################################################################
# v8_snapshot library

set(SRCS_V8_SNAPSHOT "${XMAKE_BINARY_DIR}/generated/v8/libraries.cc"
                     "${XMAKE_BINARY_DIR}/generated/v8/experimental-libraries.cc"
                     "${XMAKE_BINARY_DIR}/generated/v8/snapshot.cc"
)

# Declaration of the library
xmake_create_static_library(V8_SNAPSHOT v8_snapshot ${SRCS_V8_SNAPSHOT})
xmake_export_include_paths(V8_SNAPSHOT "${V8_SOURCE_DIR}/src")

# Disable some warnings in Visual Studio
xmake_disable_vs_warning(V8_SNAPSHOT 4800 YES)
xmake_disable_vs_warning(V8_SNAPSHOT 4355 YES)


##########################################################################################
# v8 dynamic library

if (NOT V8_STATIC)
    set(SRCS_V8_DYN "${V8_SOURCE_DIR}/src/v8dll-main.cc"
                    ${SRCS_V8_BASE_ALL}
                    ${SRCS_V8_SNAPSHOT}
    )

    # Declaration of the library
    xmake_create_dynamic_library(V8 v8 3.8.9 3.8.0 ${SRCS_V8_DYN})
    xmake_add_to_list_property(V8 COMPILE_DEFINITIONS "BUILDING_V8_SHARED;V8_SHARED")

    if (UNIX)
        xmake_link_libraries(V8 pthread)
    endif()

    if (WIN32)
        xmake_add_to_property(V8 LINK_FLAGS "Ws2_32.lib Winmm.lib")

        # To prevent an error with Visual Studio's "parallel projects builds"
        add_dependencies(v8 v8_snapshot)
    endif()

    # Disable some warnings in Visual Studio
    xmake_disable_vs_warning(V8 4800 YES)
    xmake_disable_vs_warning(V8 4355 YES)
    xmake_disable_vs_warning(V8 4351 NO)

    xmake_export_include_paths(V8 "${V8_SOURCE_DIR}/include")
    xmake_export_compile_definitions(V8 "USING_V8_SHARED;V8_SHARED")
endif()


##########################################################################################
# js2c-d8

set(LIBRARY_D8_FILES "${V8_SOURCE_DIR}/src/macros.py"
                     "${V8_SOURCE_DIR}/src/d8.js"
)

add_custom_command(OUTPUT "${XMAKE_BINARY_DIR}/generated/v8/d8-js.cc"
                   COMMAND "${PYTHON_EXECUTABLE}" "${V8_SOURCE_DIR}/tools/js2c.py" "${XMAKE_BINARY_DIR}/generated/v8/d8-js.cc" "D8" "off" ${LIBRARY_D8_FILES}
                   WORKING_DIRECTORY "${V8_SOURCE_DIR}"
                   COMMENT "js2c-d8..."
                   VERBATIM)


##########################################################################################
# d8
#
# Note: When D8 is linked against the shared library version of V8, a lot of features
#       have to be dropped. This is known as the "D8 lite" version, meant only to test
#       the shared library version of V8. Thus, we are always linking D8 against the
#       static libraries here.

# from http://websvn.kde.org/trunk/KDE/kdeedu/cmake/modules/FindReadline.cmake
# http://websvn.kde.org/trunk/KDE/kdeedu/cmake/modules/COPYING-CMAKE-SCRIPTS
# --> BSD licensed
if (READLINE_INCLUDE_DIR AND READLINE_LIBRARY AND NCURSES_LIBRARY)
  set(READLINE_FOUND TRUE)
else()
  FIND_PATH(READLINE_INCLUDE_DIR readline/readline.h
    /usr/include/readline
  )

  FIND_LIBRARY(READLINE_LIBRARY NAMES readline)
  include(FindPackageHandleStandardArgs)
  FIND_PACKAGE_HANDLE_STANDARD_ARGS(Readline DEFAULT_MSG READLINE_INCLUDE_DIR READLINE_LIBRARY )

  MARK_AS_ADVANCED(READLINE_INCLUDE_DIR READLINE_LIBRARY)
endif()


if (READLINE_FOUND)
    include_directories("${READLINE_INCLUDE_DIR}")
    set(SRCS_D8_READLINE "${V8_SOURCE_DIR}/src/d8-readline.cc")
else()
    set(SRCS_D8_READLINE "")
endif()


if (WIN32)
    set(SRCS_D8_PLATFORM "${V8_SOURCE_DIR}/src/d8-windows.cc")
else()
    set(SRCS_D8_PLATFORM "${V8_SOURCE_DIR}/src/d8-posix.cc")
endif()


xmake_create_executable(D8 d8 "${V8_SOURCE_DIR}/src/d8.cc"
                              "${V8_SOURCE_DIR}/src/d8-debug.cc"
                              "${XMAKE_BINARY_DIR}/generated/v8/d8-js.cc"
                              "${SRCS_D8_READLINE}"
                              "${SRCS_D8_PLATFORM}"
)

xmake_project_link(D8 V8_BASE V8_SNAPSHOT)

if (READLINE_FOUND)
    xmake_link_libraries(D8 readline)
endif()
