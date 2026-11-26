dnl Competition-pruned FC6-II configuration.
dnl
dnl This fork is STM32-only. Android, desktop UI, wireless tools and non-STM32
dnl targets have been removed; configure only what the firmware build needs.

AC_PREREQ([2.57])
AC_INIT([altos], [1.9.22])
AC_CONFIG_SRCDIR([src/kernel/ao.h])
AM_INIT_AUTOMAKE([foreign])
AM_MAINTAINER_MODE
AC_CONFIG_HEADERS([config.h])

WARN_CFLAGS="-Wall -Wpointer-arith -Wstrict-prototypes \
	-Wmissing-prototypes -Wmissing-declarations \
	-Wnested-externs -fno-strict-aliasing"
AC_SUBST(WARN_CFLAGS)

AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_LN_S
PKG_PROG_PKG_CONFIG

dnl ---------------------------------------------------------------------------
dnl ARM toolchain for STM32F1 (Cortex-M3) targets.
dnl ---------------------------------------------------------------------------

AC_ARG_WITH([arm-cc],
	[AS_HELP_STRING([--with-arm-cc], [Name of ARM C compiler])],
	[], [with_arm_cc=auto])

if test "x$with_arm_cc" = "xno"; then
	HAVE_ARM_CC=no
else
	if test "x$with_arm_cc" = "xauto"; then
		with_arm_cc=arm-none-eabi-gcc
	fi
	AC_CHECK_PROG([HAVE_ARM_CC], [$with_arm_cc], yes, no)
fi

if test "x$HAVE_ARM_CC" = "xno"; then
	AC_MSG_WARN([ARM compiler not found; STM32 firmware will not be built])
else
	ARM_CC=$with_arm_cc
	save_CC="$CC"
	save_CFLAGS="$CFLAGS"
	save_LIBS="$LIBS"
	CC="$ARM_CC"
	CFLAGS="-mthumb -mcpu=cortex-m3"
	LIBS="--specs=picolibc.specs"
	AC_LANG_PUSH([C])
	AC_MSG_CHECKING([if $ARM_CC supports cortex-m3])
	AC_LINK_IFELSE([AC_LANG_PROGRAM([])],
		[HAVE_ARM_M3_CC=yes], [HAVE_ARM_M3_CC=no])
	AC_MSG_RESULT([$HAVE_ARM_M3_CC])
	AC_LANG_POP([C])
	LIBS="$save_LIBS"
	CFLAGS="$save_CFLAGS"
	CC="$save_CC"
fi
AC_SUBST([HAVE_ARM_M3_CC])
AC_SUBST([ARM_CC])

dnl Kept for src/Makedefs compatibility; no Cortex-M0 targets remain.
HAVE_ARM_M0_CC=no
AVR_CC=
AVR_OBJCOPY=
HAVE_AVR_CC=no
AC_SUBST([HAVE_ARM_M0_CC])
AC_SUBST([AVR_CC])
AC_SUBST([AVR_OBJCOPY])
AC_SUBST([HAVE_AVR_CC])

AC_CHECK_PROG([HAVE_NICKLE], [nickle], yes, no)
if test "x$HAVE_NICKLE" = "xno"; then
	AC_MSG_ERROR([nickle is required to generate firmware headers])
fi

dnl Host helpers in ao-tools/lib still use libusb and libelf.
PKG_CHECK_MODULES([LIBUSB], [libusb-1.0])
AC_CHECK_HEADERS([libelf.h libelf/libelf.h], [break])

AC_OUTPUT([
	Makefile
	src/Makedefs
	ao-tools/Makefile
	ao-tools/lib/Makefile
	ao-tools/ao-elftohex/Makefile
	ao-tools/ao-makebin/Makefile
])

echo ""
echo "  Package: ${PACKAGE_NAME} ${PACKAGE_VERSION}"
echo ""
echo "  Configuration"
echo "    ARM compiler................: ${ARM_CC}"
echo "    STM32F1 / Cortex-M3 support.: ${HAVE_ARM_M3_CC}"
echo ""
