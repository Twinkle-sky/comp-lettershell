#
# Letter_Shell — lightweight embedded serial console
# =============================================================================
# This file wraps the third-party library as a STATIC library target so it
# can be consumed cleanly by the application layer without modifying the
# upstream source tree.
#
# Upstream: https://github.com/Twinkle-sky/comp-lettershell
#

add_library(lettershell STATIC)

# ---- Core source files ---------------------------------------------------
target_sources(lettershell PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src/shell.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/shell_ext.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/shell_cmd_list.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/shell_companion.c
)

# ---- Public headers (any consumer can #include <shell.h>) ----------------
target_include_directories(lettershell PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# ---- Bare-metal (no RTOS) configuration ----------------------------------
# These propagate to every target that links against lettershell (PUBLIC),
# overriding the defaults in shell_cfg.h before they take effect.
#
# NOTE: SHELL_GET_TICK is intentionally NOT set here — it defaults to 0
# (tick features disabled).  To enable double-tab help and lock timeout,
# define SHELL_GET_TICK to your HAL tick function and ensure the
# corresponding header is reachable from both the lettershell library
# and application code.
target_compile_definitions(lettershell PUBLIC
    SHELL_TASK_WHILE=0          # do NOT loop inside shellTask — caller drives the loop
    SHELL_USING_CMD_EXPORT=0    # use command-table mode (no custom linker section needed)
)
