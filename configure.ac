AC_INIT([a6], [0.1], [sceneviper@hotmail.com], [a6], [https://github.com/Alpacius/a6])
AC_CONFIG_SRCDIR([src])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE([foreign subdir-objects])
AM_PROG_AR
AM_PROG_AS

LT_INIT

AC_ARG_ENABLE([arch-specified-impl], AS_HELP_STRING([--enable-arch-specified-impl], [enable architecture specified implementation (available for: x86-64)]))
AM_CONDITIONAL([ENABLE_ARCH_SPEC_IMPL], [test -n "${enable_arch_specified_impl}"])

AC_CANONICAL_HOST
AS_CASE([$host_cpu],
        [x86_64],
        [
            AM_CONDITIONAL([ENABLE_X86_64_CNTX], [test -n "${enable_arch_specified_impl}"])
        ])

AC_PROG_CC
AC_CHECK_LIB(pthread, pthread_create)

AC_OUTPUT([Makefile])
