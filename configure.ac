# $Id: $

AC_INIT([hfp4linux], [0.1.0], [samr7@cs.washington.edu])
AC_CONFIG_HEADER([config.h])
AC_CONFIG_MACRO_DIR([m4])
AM_MAINTAINER_MODE([enable])

DX_HTML_FEATURE(ON)
DX_CHM_FEATURE(OFF)
DX_CHI_FEATURE(OFF)
DX_MAN_FEATURE(OFF)
DX_RTF_FEATURE(OFF)
DX_XML_FEATURE(OFF)
DX_PDF_FEATURE(OFF)
DX_PS_FEATURE(OFF)
DX_INIT_DOXYGEN([libhfp], [Doxyfile], [doc])

AM_INIT_AUTOMAKE

AC_PROG_CC
AC_PROG_CXX
AC_PROG_CXX_C_O
AC_PROG_LIBTOOL

dnl Checks for header files.
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS([fcntl.h limits.h stdint.h stdlib.h string.h sys/ioctl.h sys/socket.h unistd.h])

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_C_INLINE
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T

dnl Checks for library functions.
AC_FUNC_ALLOCA
AC_FUNC_FORK
AC_PROG_GCC_TRADITIONAL
AC_FUNC_MALLOC
AC_FUNC_VPRINTF
AC_CHECK_FUNCS([memmove memset socket strdup strncasecmp strtol])

dnl Append /usr/local/lib/pkgconfig to PKG_CONFIG_PATH
dnl Manually installed packages like libnghost will tend to go there
PKG_CONFIG_PATH="${PKG_CONFIG_PATH}${PKG_CONFIG_PATH:+:}/usr/local/lib/pkgconfig"

PKG_PROG_PKG_CONFIG([0.9.0])

dnl *******************
dnl Default debug level
dnl For now, verbose debugging is on by default
dnl Set to "yes" for more extreme measures, "partial" for simple
dnl measures, and "no" to turn off all measures.

default_want_debug=yes
dnl default_want_debug=maybe

dnl *******************

AC_ARG_ENABLE(debug,
	AC_HELP_STRING([--disable-debug], [Configure debugging measures]),
	want_debug=$enableval, want_debug=$default_want_debug)

dnl Add -Wall to the CFLAGS unconditionally
CFLAGS="$CFLAGS -Wall" # -Werror -fstack-protector-all -Wstack-protector -Woverloaded-virtual -Wextra -Wshadow"
CXXFLAGS="$CXXFLAGS -Wall" # -Werror -fstack-protector-all -Wstack-protector -Woverloaded-virtual -Wextra -Wshadow"

dnl If debugging is enabled, and the user didn't supply CFLAGS or CXXFLAGS,
dnl remove the autoconf default -O2.  This is kind of a pain in the @$$
if test $want_debug \!= "no"; then
	if test x${ac_test_CFLAGS} != xset; then
		CFLAGS=`echo $CFLAGS | sed -e 's,-O2,,g'`
	fi
	if test x${ac_test_CXXFLAGS} != xset; then
		CXXFLAGS=`echo $CXXFLAGS | sed -e 's,-O2,,g'`
	fi
else
	dnl Add -DNDEBUG to CFLAGS/CXXFLAGS in this case
	if test x${ac_test_CFLAGS} != xset; then
		CFLAGS="$CFLAGS -DNDEBUG"
	fi
	if test x${ac_test_CXXFLAGS} != xset; then
		CXXFLAGS="$CXXFLAGS -DNDEBUG"
	fi
fi

ACX_PTHREAD(use_pthread=yes, use_pthread=no)

AC_ARG_ENABLE(alsa,
	AC_HELP_STRING([--enable-alsa], [build ALSA backend]),
	want_alsa=$enableval, want_alsa=maybe)

AC_ARG_ENABLE(qt,
	AC_HELP_STRING([--enable-qt], [build Qt demonstration app]),
	want_qt=$enableval, want_qt=maybe)

AC_ARG_ENABLE(oss,
	AC_HELP_STRING([--enable-oss], [build OSS backend]),
	want_oss=$enableval, want_oss=yes)

AC_ARG_ENABLE(audiofile,
	AC_HELP_STRING([--enable-audiofile], [build audiofile backend]),
	want_audiofile=$enableval, want_audiofile=maybe)

AC_ARG_ENABLE(speexdsp,
	AC_HELP_STRING([--enable-speexdsp], [build the speexdsp AEC filter]),
	want_speexdsp=$enableval, want_speexdsp=maybe)

AC_ARG_ENABLE(dbus,
	AC_HELP_STRING([--enable-dbus], [build D-Bus components -- hfpd]),
	want_dbus=$enableval, want_dbus=maybe)

AC_ARG_ENABLE(dbus-service,
	AC_HELP_STRING([--disable-dbus-service], [Install D-Bus service file]),
	do_dbus_service=$enableval, do_dbus_service=yes)

AC_ARG_ENABLE(nghost,
	AC_HELP_STRING([--enable-nghost], [build the nhandsfree nGhost plugin]),
	want_nghost=$enableval, want_nghost=maybe)

dnl We can't build without libbluetooth
PKG_CHECK_MODULES([BLUEZ], [bluez >= 2.00])

dnl libasound is optional
use_alsa=no
if test $want_alsa != "no"; then
	PKG_CHECK_MODULES([ALSA], [alsa >= 0.9.0],
			[use_alsa="yes"], [use_alsa="no"])
	if test $use_alsa = "no" -a $want_alsa = "yes"; then
	        AC_MSG_ERROR([[
***
*** ALSA support has been requested, but was not found.
*** Check pkg_config_dir if this is in error.
*** Aborting.
***
]])
	fi
fi

dnl libaudiofile is optional
use_audiofile=no
if test $want_audiofile != "no"; then
	PKG_CHECK_MODULES([AUDIOFILE], [audiofile],
			[use_audiofile="yes"], [use_audiofile="no"])

	if test $use_audiofile = "no" -a $want_audiofile = "yes"; then
	        AC_MSG_ERROR([[
***
*** libaudiofile support has been requested, but was not found.
*** Check pkg_config_dir if this is in error.
*** Aborting.
***
]])
	fi
fi

dnl libspeexdsp is optional
use_speexdsp=no
if test $want_speexdsp != "no"; then
	PKG_CHECK_MODULES([SPEEXDSP], [speexdsp >= 1.2beta2],
			[use_speexdsp="yes"], [use_speexdsp="no"])

	if test $use_speexdsp = "no" -a $want_speexdsp = "yes"; then
	        AC_MSG_ERROR([[
***
*** libspeexdsp support has been requested, but was not found.
*** Check pkg_config_dir if this is in error.
*** Aborting.
***
]])
	fi
fi

dnl dbus is optional
use_dbus=no
if test $want_dbus != "no"; then
	PKG_CHECK_MODULES([DBUS], [dbus-1],
			[use_dbus="yes"], [use_dbus="no"])

	if test $use_dbus = "no" -a $want_dbus = "yes"; then
	        AC_MSG_ERROR([[
***
*** D-Bus support has been requested, but was not found.
*** Check pkg_config_dir if this is in error.
*** Aborting.
***
]])
	fi
fi

dnl Find the D-Bus service path from pkgconfig
dnl This may be a different directory than we would expect
dnl based on our chosen prefix!
if test $use_dbus = "yes"; then
	if test $do_dbus_service = "yes"; then
		AC_ARG_VAR([DBUS_SERVICE_PATH],
	           [Path for D-Bus .service files, overriding pkg-config])
		_PKG_CONFIG([DBUS_SERVICE_PATH],
			    [variable=session_bus_services_dir],
			    [dbus-1])
		DBUS_SERVICE_PATH=$pkg_cv_DBUS_SERVICE_PATH
		AC_SUBST(DBUS_SERVICE_PATH)
	fi

	DBUS_VERSION=`$PKG_CONFIG --modversion dbus-1`
	DBUS_VERSION_MAJOR=`echo $DBUS_VERSION | cut -d. -f 1`
	DBUS_VERSION_MINOR=`echo $DBUS_VERSION | cut -d. -f 2`
	DBUS_VERSION_MICRO=`echo $DBUS_VERSION | cut -d. -f 3`

	AC_DEFINE_UNQUOTED(DBUS_VERSION_MAJOR, $DBUS_VERSION_MAJOR,
			   [D-BUS major version.])
	AC_DEFINE_UNQUOTED(DBUS_VERSION_MINOR, $DBUS_VERSION_MINOR,
			   [D-BUS minor version.])
	AC_DEFINE_UNQUOTED(DBUS_VERSION_MICRO, $DBUS_VERSION_MICRO,
			   [D-BUS micro version.])
fi


use_nghost=no
if test $want_nghost != "no"; then
	PKG_CHECK_MODULES([NGHOSTCORE], [nghostcore],
			[use_nghost="yes"], [use_nghost="no"])
	if test $use_nghost = "no" -a $want_nghost = "yes"; then
	        AC_MSG_ERROR([[
***
*** libnghost support has been requested, but was not found.
*** Check pkg_config_dir if this is in error.
*** Aborting.
***
]])
	fi
fi

if test $use_nghost = "yes"; then
	PKG_CHECK_MODULES([NGHOSTDAO], [nghostdao])
	PKG_CHECK_MODULES([NGHOSTGUI2], [nghostgui2])
	PKG_CHECK_MODULES([NGHOSTLOG], [nghostlog])
	PKG_CHECK_MODULES([NGHOSTNET], [nghostnet])
	PKG_CHECK_MODULES([NGHOSTPLUGINS], [nghostplugins])
	use_nghost=yes
	AC_SUBST([pkglibdir], [${libdir}/nghost])
fi


have_qt=maybe
if test $want_qt != no; then
	AC_PATH_X
	if test "$no_x" = yes; then
		have_qt=no
 	fi
fi
if test $have_qt = "maybe"; then
	PKG_CHECK_MODULES([QT], [QtCore QtGui Qt3Support], [have_qt="yes"])
	have_moc=no
	AC_PATH_PROGS([MOC],[moc-qt4 moc4 moc])
	if test "x$MOC" != "x"; then
		have_moc=yes
	fi
	have_uic=no
	AC_PATH_PROGS([UIC], [uic-qt4 uic4 uic])
	if test "x$UIC" != "x"; then
		have_uic=yes
	fi
fi
if test $have_qt = "no" -a $want_qt = "maybe"; then
	want_qt=no
fi
if test $want_qt = "yes"; then
	if test $have_qt = "no"; then
	        AC_MSG_ERROR([[
***
*** Qt ($qt_minversion) $ac_qt_notfound not found. Please check your
*** installation!  For more details about this problem, look at the
*** end of config.log.$missing_qt_mt
***]])
	fi
	if test $have_moc = "no"; then
	        AC_MSG_ERROR([[
***
*** No Qt meta object compiler (moc) found!
*** Please check whether you installed Qt correctly.
*** You need to have a running moc binary.
*** configure tried to run $ac_cv_path_moc and the test didn't
*** succeed. If configure shouldn't have tried this one, set
*** the environment variable MOC to the right one before running
*** configure.
***]])
	fi
	if test $have_uic = "no"; then
	        AC_MSG_ERROR([[
***
*** No Qt4 user interface compiler (uic) found!
*** Please check whether you installed Qt correctly.
*** You need to have a running uic binary.
*** configure tried to run $ac_cv_path_uic and the test didn't
*** succeed. If configure shouldn't have tried this one, set
*** the environment variable UIC to the right one before running
*** configure.
***]])
	fi
fi


dnl BlueZ libraries are unconditional

libhfp_CXXFLAGS="$libhfp_CXXFLAGS $BLUEZ_CFLAGS"
libhfp_LIBS="$libhfp_LIBS $BLUEZ_LIBS"
AC_SUBST(libhfp_CXXFLAGS)
AC_SUBST(libhfp_LIBS)


dnl Other options are set here and have their cflags/libs added
dnl to libhfp_CXXFLAGS and libhfp_LIBS.

if test $want_debug = "yes"; then
	AC_DEFINE([USE_VERBOSE_DEBUG], [], [Enable extreme debug measures])
fi

if test $use_pthread = "yes"; then
	AC_DEFINE([USE_PTHREADS], [], [Enable thread support])
	libhfp_CXXFLAGS="$libhfp_CXXFLAGS $PTHREAD_CFLAGS"
	libhfp_LIBS="$libhfp_LIBS $PTHREAD_LIBS"
fi

if test $use_alsa = "yes"; then
	AC_DEFINE([USE_ALSA_SOUNDIO], [], [Enable support for libasound])
	libhfp_CXXFLAGS="$libhfp_CXXFLAGS $ALSA_CFLAGS"
	libhfp_LIBS="$libhfp_LIBS $ALSA_LIBS"
else
	if test $want_alsa != "no"; then
		echo "***"
		echo "***"
		echo "*** WARNING: Support for ALSA is being omitted"
		echo "*** WARNING: This probably isn't what you want"
		echo "*** WARNING: Install libasound development package"
		echo "***"
		echo "***"
		echo -n -e "\a"
		sleep 1
		echo -n -e "\a"
		sleep 1
		echo -n -e "\a"
	fi
fi
if test $want_oss = "yes"; then
	AC_DEFINE([USE_OSS_SOUNDIO], [], [Enable support for OSS])
fi
if test $use_audiofile = "yes"; then
	AC_DEFINE([USE_AUDIOFILE], [], [Enable support for libaudiofile])
	libhfp_CXXFLAGS="$libhfp_CXXFLAGS $AUDIOFILE_CFLAGS"
	libhfp_LIBS="$libhfp_LIBS $AUDIOFILE_LIBS"
fi
if test $use_speexdsp = "yes"; then
	AC_DEFINE([USE_SPEEXDSP], [], [Enable support for libspeexdsp])
	libhfp_CXXFLAGS="$libhfp_CXXFLAGS $SPEEXDSP_CFLAGS"
	libhfp_LIBS="$libhfp_LIBS $SPEEXDSP_LIBS"
else
	if test $want_speexdsp != "no"; then
		echo "***"
		echo "***"
		echo "*** WARNING: Support for libspeexdsp is being omitted"
		echo "*** WARNING: This probably isn't what you want"
		echo "*** WARNING: Install libspeexdsp development package"
		echo "***"
		echo "***"
		echo -n -e "\a"
		sleep 1
		echo -n -e "\a"
		sleep 1
		echo -n -e "\a"
	fi
fi

if test $use_nghost = "yes"; then
	libnghost_CFLAGS="$NGHOSTCORE_CFLAGS $NGHOSTDAO_CFLAGS $NGHOSTGUI2_CFLAGS $NGHOSTLOG_CFLAGS $NGHOSTNET_CFLAGS $NGHOSTPLUGINS_CFLAGS"
	libnghost_LIBS="$NGHOSTCORE_LIBS $NGHOSTDAO_LIBS $NGHOSTGUI2_LIBS $NGHOSTLOG_LIBS $NGHOSTNET_LIBS $NGHOSTPLUGINS_LIBS"
fi
AC_SUBST(libnghost_CFLAGS)
AC_SUBST(libnghost_LIBS)
AM_CONDITIONAL(BUILD_DBUS, [test $use_dbus = "yes"])
AM_CONDITIONAL(BUILD_DBUS_SERVICE, [test $use_dbus = "yes" -a $do_dbus_service = "yes"])
AM_CONDITIONAL(BUILD_QT, [test $have_qt = "yes"])
AM_CONDITIONAL(BUILD_NGHOST, [test $use_nghost = "yes"])

AC_CONFIG_FILES([Makefile libhfp/Makefile include/Makefile include/libhfp/Makefile qt/Makefile nhandsfree/Makefile hfpd/Makefile test/Makefile data/Makefile])
AC_OUTPUT
