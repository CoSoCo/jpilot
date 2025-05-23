dnl ############################################################################
dnl Process this file with autoconf to produce a configure script.
dnl ############################################################################

dnl boilerplate to initialize autoconf(AC) and automake(AM)
AC_PREREQ([2.71])
AC_INIT([jpilot],[2.0.2])
AM_INIT_AUTOMAKE([1.8])

dnl use a config.h file rather than string of -DXXX args to compiler
dnl run autoheader before autoconf to create config.h.in from configure.in
AM_CONFIG_HEADER(config.h)
dnl enable maintainer mode in Makefiles (make dist, distclean)
AM_MAINTAINER_MODE

dnl ############################################################################
dnl Check for programs needed during configure or build
dnl ############################################################################
AC_CANONICAL_HOST # determine build host
AC_PROG_CC
dnl AC_SEARCH_LIBS([strerror],[cposix]) only needed for Interacive Systems UNIX which Sun
dnl is discontinuing support on as of 7/23/2006.
dnl This obsolete macro should probably be removed.
AC_SEARCH_LIBS([strerror],[cposix])
dnl Need intltool for i18n support
AC_PROG_INTLTOOL()
AC_PROG_INSTALL
AC_PATH_PROG([SED], [sed], [ AC_MSG_ERROR([sed is required for configure script]) ])
AC_PATH_PROG([GREP], [grep], [ AC_MSG_ERROR([grep is required for configure script]) ])
AC_PATH_PROG([CUT], [cut], [ AC_MSG_ERROR([cut is required for configure script]) ])

dnl ******************************
dnl Libtool setup
dnl ******************************
dnl enable shared library building, turn off static libraries
AC_DISABLE_STATIC
AC_LIBTOOL_DLOPEN
AC_PROG_LIBTOOL
AC_SUBST(LIBTOOL_DEPS)

dnl ******************************
dnl Gettext setup
dnl ******************************
dnl Set of available languages
ALL_LINGUAS="ca cs da de es fr it ja ko nl nb pt_BR ru rw sv tr uk vi zh_CN zh_TW"

dnl Name of language files
GETTEXT_PACKAGE=jpilot
AC_SUBST(GETTEXT_PACKAGE)
dnl 9/29/2006: Work around a bug in current versions of gettext/intltool
dnl            which leave this macro undefined.
DATADIRNAME=share
AC_SUBST(DATADIRNAME)

dnl Setup gettext using version available on host
dnl The intl/ directory is not exported with the distribution
AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION([0.16.1])

if test "x$MSGFMT" = "xno"; then
   if test "x$GMSGFMT" = "xno"; then
      AC_MSG_RESULT(No gettext found)
      AC_MSG_ERROR(Either install gettext or use 'configure --disable-nls')
   fi
fi

dnl gettext macros for context-sensitive messages depend on locale.h
AC_CHECK_HEADER(locale.h,,
                AC_MSG_ERROR(gettext requires locale.h.  Install locale.h or disable language support with 'configure --disable-nls'))

dnl ############################################################################
dnl Process Enable/Disable configure arguments
dnl ############################################################################

AC_ARG_ENABLE(plugins,
   AS_HELP_STRING([--disable-plugins],[Do not compile plugin support]),
   enable_plugins=$enableval,
   enable_plugins=yes)

plugin_support=no

if test "x$enable_plugins" = "xyes"; then
   AC_CHECK_FUNCS(dlopen)
   if test "x$ac_cv_func_dlopen" = "xyes"; then
      have_dlopen=yes
   else
      for lib in dl; do
         AC_CHECK_LIB($lib, dlopen, [LIBS="$LIBS -ldl";
          have_dlopen=yes; break])
      done
   fi

   if test "x$have_dlopen" = "xyes"; then
      AC_DEFINE(ENABLE_PLUGINS, 1, [ plugin support ])
      plugin_support="yes"
##      AC_SUBST(plugin_support) ## %RW: not apparently used anywhere
   else
      AC_MSG_RESULT(Could not find dlopen - plugin support disabled)
   fi
else
   AC_MSG_RESULT(Plugin support disabled by configure options)
fi

dnl ************************************************************

AC_ARG_ENABLE(private,
   AS_HELP_STRING([--disable-private],[Do not use private records feature (password not needed to see private records)]),
   enable_private=$enableval,
   enable_private=yes)

if test "$enable_private" = "yes"; then
   AC_DEFINE(ENABLE_PRIVATE, 1, [ Private record support ])
else
   AC_MSG_RESULT(Private record support disabled by configure options)
fi

dnl ************************************************************

AC_ARG_ENABLE(datebk,
   AS_HELP_STRING([--disable-datebk],[Disable Datebk support]),
   enable_datebk=$enableval,
   enable_datebk=yes)

if test "$enable_datebk" = "yes"; then
   AC_DEFINE(ENABLE_DATEBK, 1, [ DateBk support ])
else
   AC_MSG_RESULT(Datebk support disabled by configure options)
fi

dnl ************************************************************

AC_ARG_ENABLE(manana,
   AS_HELP_STRING([--disable-manana],[Disable Manana support]),
   enable_manana=$enableval,
   enable_manana=yes)

if test "$enable_manana" = "yes"; then
   AC_DEFINE(ENABLE_MANANA, 1, [ Manana support ])
else
   AC_MSG_RESULT(Manana support disabled by configure options)
fi

dnl ************************************************************

AC_ARG_ENABLE(prometheon,
   AS_HELP_STRING([--enable-prometheon],[For use with Prometheon: http://www.prometheon.net]),
   enable_prometheon=$enableval,
   enable_prometheon=no)

if test "$enable_prometheon" = "yes"; then
   AC_DEFINE(ENABLE_PROMETHEON, 1, [ Prometheon support ])
   AC_DEFINE_UNQUOTED(PROGNAME, "copilot", [ program name ])
   AC_MSG_RESULT(Prometheon support enabled)
else
   AC_DEFINE_UNQUOTED(PROGNAME, "jpilot", [ program name ])
fi

AC_SUBST(PROGNAME)

dnl ************************************************************

AC_ARG_ENABLE(alarm-shell-danger,
   AS_HELP_STRING([--enable-alarm-shell-danger],[Allow alarm descriptions and notes to be used in alarm shell commands]),
   enable_alarm_shell_danger=$enableval,
   enable_alarm_shell_danger=no;
   )

if test "$enable_alarm_shell_danger" = "yes"; then
   AC_DEFINE(ENABLE_ALARM_SHELL_DANGER, 1, [ Datebook description and note allowed  in shell commands ])
fi

dnl ************************************************************

AC_ARG_ENABLE(stock_buttons,
   AS_HELP_STRING([--disable-stock-buttons],[Disable stock buttons (icons on GUI buttons)]),
   enable_stock_buttons=$enableval,
   enable_stock_buttons=yes)

if test "x$enable_stock_buttons" = "xyes"; then
    AC_DEFINE(ENABLE_STOCK_BUTTONS, 1, [ Use GTK2 stock buttons ])
    AC_MSG_RESULT(stock buttons enabled by configure options)
else
    AC_MSG_RESULT(stock buttons disabled by configure options)
fi

dnl ############################################################################
dnl Check for libraries needed
dnl ############################################################################
dnl ******************************
dnl * GTK3 libraries
dnl ******************************
AM_PATH_GTK_3_0(3.24.12, ,
  AC_MSG_ERROR([*** GTK >= 3.24.12 not found ***]))

dnl ******************************
dnl * pilot-link libs
dnl ******************************
pilot_prefix=""
AC_ARG_WITH(pilot_prefix,
            AS_HELP_STRING([--with-pilot-prefix=PFX],[Prefix to top level of pilot-link files (e.g., = /usr/local if the pilot-link includes are in /usr/local/include and libs are in /usr/local/lib)]))

if test "x$with_pilot_prefix" != "x"; then
   pilot_prefix=$with_pilot_prefix
fi

dnl Make sure that the pilot-link stuff actually exists

AC_MSG_CHECKING(for pilot-link header files)
pilotinclude=${FORCE_PILOT_INCLUDES:-no}
if test "$pilotinclude" = "no" ; then
for pilot_incl in $pilot_prefix/include /usr/include /usr/local/include \
                  /usr/extra/pilot/include /usr/include/libpisock; do
   if test -r "$pilot_incl/pi-version.h" ; then
      pilotinclude=yes
      PILOT_FLAGS="$PILOT_FLAGS -I$pilot_incl"
      break
   fi
done
fi

if test "$pilotinclude" = "no" ; then
   AC_MSG_RESULT(no)
   AC_MSG_ERROR(Could not find the pilot-link header files)
else
   AC_MSG_RESULT(found at $pilot_incl)
fi

dnl pilot-link headers are installed.  Now check for libraries

AC_MSG_CHECKING(for pilot library files)
pilotlibs=${FORCE_PILOT_LIBS:-no}
PILOT_LIBS="-lpisock"

if test "$pilotlibs" = "no" ; then
for pilot_libs in $pilot_prefix/lib /usr/lib /usr/local/lib/ /usr/local/lib64 \
   /usr/extra/pilot/lib $pilot_prefix/lib64 /usr/lib64 ; do
   if test -r $pilot_libs/libpisock.so >/dev/null 2>&1 ; then
      pilotlibs=yes
      PILOT_LIBS="-L$pilot_libs $PILOT_LIBS"
      break
   fi
   if test -r "$pilot_libs/libpisock.a" ; then
      pilotlibs=yes
      PILOT_LIBS="-L$pilot_libs $PILOT_LIBS"
      break
   fi
   if test -r "$pilot_libs/libpisock.sl" ; then
      pilotlibs=yes
      PILOT_LIBS="-L$pilot_libs $PILOT_LIBS"
      break
   fi
   if test -r "$pilot_libs/libpisock.dylib" ; then
      pilotlibs=yes
      PILOT_LIBS="-L$pilot_libs $PILOT_LIBS"
      break
   fi
done
fi

if test "$pilotlibs" = "no" ; then
   AC_MSG_RESULT(no)
   AC_MSG_ERROR(Could not find the pilot-link libraries)
else
   AC_MSG_RESULT(found at $pilot_libs)
fi

dnl Solaris needs the socket library
AC_CHECK_FUNC(gethostent, , AC_CHECK_LIB(nsl, gethostent))
AC_CHECK_FUNC(setsockopt, , AC_CHECK_LIB(socket, setsockopt))

dnl Substitute macros now that LIB & FLAG detection is done
AC_SUBST(PILOT_LIBS)
AC_SUBST(PILOT_FLAGS)

dnl ************************************************************

AC_ARG_ENABLE(pl-test,
   AS_HELP_STRING([--disable-pl-test],[Do not try to compile a test pilot-link program]),
   enable_pl_test=$enableval,
   enable_pl_test=yes)

dnl run some simple tests to verify pilot-link environment

if test "x$enable_pl_test" = "xyes"; then

   pilotcompile=no

   dnl Check to make sure pilot-link library can be linked

   AC_MSG_CHECKING(to see if I can compile a pilot link program)
   save_CFLAGS="$CFLAGS"
   CFLAGS="$CFLAGS $PILOT_FLAGS"
   save_LIBS="$LIBS"
   LIBS="$LIBS $PILOT_LIBS"

   AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include "pi-version.h"
#include "pi-socket.h"]], [[pi_close (0); ]])],[pilotcompile=yes],[])

   if test "$pilotcompile" = "no" ; then
      AC_MSG_RESULT(no)
      AC_MSG_ERROR(Could not compile a test pilot-link program)
   else
      AC_MSG_RESULT(ok)
   fi

   dnl Try to run a pilot-link program (tests dynamic linking)

   AC_MSG_CHECKING(if I can run a pilot-link program)
   AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <pi-version.h>
int main()
{
   return 0;
}
]])],[AC_MSG_RESULT(ok)],[AC_MSG_ERROR(
* Can not run a pilot-link test program
* Make sure libpisock can be found by ld
* Check /etc/ld.so.conf and run ldconfig
* This test can be disabled by the --disable-pl-test option)
   ],[ error ])

   dnl restore variables after pilot-link testing
   CFLAGS="$save_CFLAGS"
   LIBS="$save_LIBS"

fi

dnl * Check pilot-link pi-version.h

AC_MSG_CHECKING(pilot-link version)

save_CFLAGS="$CFLAGS"
CFLAGS="$CFLAGS $PILOT_FLAGS"
save_LIBS="$LIBS"
LIBS="$LIBS $PILOT_LIBS"

AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[
#include <stdlib.h>
#include <pi-version.h>
]], [[
   exit(0);
]])],[],[AC_MSG_ERROR(pilot-link header pi-version.h not found)
])

dnl * Pilot-link version variations, USB support, 12.0, etc.

pl_version_check_done=no;

pl_version=`$GREP "define PILOT_LINK_VERSION" "$pilot_incl/pi-version.h" | \
$CUT -d " " -f 3`
pl_major=`$GREP "define PILOT_LINK_MAJOR" "$pilot_incl/pi-version.h" | \
$CUT -d " " -f 3`
pl_minor=`$GREP "define PILOT_LINK_MINOR" "$pilot_incl/pi-version.h" | \
$CUT -d " " -f 3`
pl_patch=`$GREP "define PILOT_LINK_PATCH" "$pilot_incl/pi-version.h" | \
$CUT -d " " -f 3 | $SED -e 's/"//g'`
AC_MSG_RESULT([pi-version indicates $pl_version.$pl_major.$pl_minor])

dnl *** check for pilot-link 0.12.5 and up

if test $pl_version -eq 0 ; then
   if test $pl_major -ge 12 ; then
      if test $pl_minor -ge 0 ; then
         pl_version_check_done=yes;
         AC_MSG_RESULT([pilot-link has USB])
         AC_MSG_RESULT([pilot-link has card support (>12.0)])
         AC_MSG_RESULT([pilot-link has Calendar support (>12.5)])
      fi
   fi
fi
if test $pl_version_check_done != yes; then
   AC_MSG_ERROR([pilot-link version >= 0.12.5 is required])
fi

dnl restore variables after pilot-link testing
CFLAGS="$save_CFLAGS"
LIBS="$save_LIBS"

dnl ******************************
dnl * End pilot-links tests
dnl ******************************

dnl Check for gnu crypt lib necessary for Keyring
crypto_lib="none"
AM_PATH_LIBGCRYPT
AC_CHECK_LIB([gcrypt], [gcry_md_hash_buffer], have_libgcrypt=1, have_libgcrypt=0)
AC_ARG_WITH(openssl,
   AS_HELP_STRING([--with-openssl],[Use OpenSSL instead of GNU libgcrypt]),
   have_libgcrypt="0")
if test "$have_libgcrypt" = "0"; then
dnl Check for OpenSSL libs necessary for Keyring
   AC_CHECK_LIB([crypto], [SSLeay_version], have_libcrypto=1, have_libcrypto=0)
else
	AC_DEFINE(HAVE_LIBGCRYPT, 1, [ Use GNU libgcrypt instead of OpenSSL ])
	crypto_lib="libgcrypt"
fi

dnl Check for sqlite3 lib, necessary for jpsqlite
AC_CHECK_LIB([sqlite3], [sqlite3_libversion], have_libsqlite3=1, have_libsqlite3=0)
#AC_CHECK_LIB([sqlite3], [sqlite3_libversion], AC_MSG_RESULT(Found sqlite3 library),
#		AC_MSG_ERROR(Could not find the sqlite3 library))

dnl ############################################################################
dnl Check for headers needed
dnl ############################################################################
dnl 10/2/06: only headers used for true code portability right now are locale.h, langinfo.h
AC_CHECK_HEADERS([fcntl.h langinfo.h locale.h stdlib.h string.h sys/socket.h sys/time.h sys/wait.h unistd.h utime.h])

# Headers required for plugins
AC_CHECK_HEADERS(netinet/in.h, have_netinet=1, have_netinet=0)
if test "$plugin_support" = "yes" -a "$have_netinet" = "0"; then
	plugin_support = "no"
fi

# jpilot-dial required headers
AC_CHECK_HEADERS(termio.h, have_termio=1, have_termio=0)

dnl Headers needed to build keyring
AC_CHECK_HEADERS(openssl/md5.h, have_openssl_md5=1, have_openssl_md5=0)
AC_CHECK_HEADERS(openssl/des.h, have_openssl_des=1, have_openssl_des=0)

dnl Headers needed to build jpsqlite
AC_CHECK_HEADERS(sqlite3.h, have_sqlite3_h=1, have_sqlite3_h=0)
#AC_CHECK_HEADERS(sqlite3.h, , AC_MSG_ERROR(Could not find sqlite3.h))

dnl ############################################################################
dnl Check for typedefs and structures needed
dnl ############################################################################
dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_INLINE
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_TYPE_UID_T
m4_warn([obsolete],
[Update your code to rely only on HAVE_SYS_TIME_H,
then remove this warning and the obsolete code below it.
All current systems provide time.h; it need not be checked for.
Not all systems provide sys/time.h, but those that do, all allow
you to include it and time.h simultaneously.])dnl
AC_CHECK_HEADERS_ONCE([sys/time.h])
# Obsolete code to be removed.
if test $ac_cv_header_sys_time_h = yes; then
  AC_DEFINE([TIME_WITH_SYS_TIME],[1],[Define to 1 if you can safely include both <sys/time.h>
	     and <time.h>.  This macro is obsolete.])
fi
# End of obsolete code.

dnl 10/2/06: This is obsolescent on most systems now as the tm struct is in time.h
AC_STRUCT_TM

dnl ******************************
dnl Check whether nl_langinfo can provide start of week to GTK
dnl ******************************

# _NL_TIME_FIRST_WEEKDAY is an enum and not a define
AC_MSG_CHECKING([for _NL_TIME_FIRST_WEEKDAY])
AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <langinfo.h>]], [[
char c;
c = *((unsigned char *)  nl_langinfo(_NL_TIME_FIRST_WEEKDAY));
]])],[nl_ok=yes],[nl_ok=no])
AC_MSG_RESULT($nl_ok)

if test "$nl_ok" = "yes"; then
  AC_DEFINE([HAVE__NL_TIME_FIRST_WEEKDAY], [1],
       [Define if _NL_TIME_FIRST_WEEKDAY is available])
fi

dnl ############################################################################
dnl Check for individual functions needed
dnl ############################################################################
dnl Checks for library functions.
m4_warn([obsolete],
[your code may safely assume C89 semantics that RETSIGTYPE is void.
Remove this warning and the `AC_CACHE_CHECK' when you adjust the code.])dnl
AC_CACHE_CHECK([return type of signal handlers],[ac_cv_type_signal],[AC_COMPILE_IFELSE(
[AC_LANG_PROGRAM([#include <sys/types.h>
#include <signal.h>
],
		 [return *(signal (0, 0)) (0) == 1;])],
		   [ac_cv_type_signal=int],
		   [ac_cv_type_signal=void])])
AC_DEFINE_UNQUOTED([RETSIGTYPE],[$ac_cv_type_signal],[Define as the return type of signal handlers
		    (`int' or `void').])

AC_FUNC_STRCOLL
#AC_FUNC_MALLOC
#AC_FUNC_REALLOC

AC_CHECK_FUNCS(setenv)

AC_ARG_WITH(with_flock,
   AS_HELP_STRING([--with-flock],[Substitute flock instead of fnctl for file locking (for NFS)]),
   with_flock=yes)

if test "x$with_flock" = "xyes"; then
   AC_CHECK_HEADERS(sys/file.h,
     [AC_DEFINE(USE_FLOCK, 1, [ Using flock instead of fnctl ])
      AC_MSG_RESULT(Using flock instead of fnctl)],
     [AC_MSG_FAILURE([--with-flock was given, but sys/file.h was not found])])
fi

dnl ############################################################################
dnl Figure which programs can be built on this particular host with the
dnl libraries available.
dnl ############################################################################

dnl * Work out which plugins to make

make_keyring=no
if test "$have_libgcrypt" = "1"; then
   make_keyring=yes
fi
if test "$have_libcrypto" = "1"; then
   if test "$have_openssl_md5" = "1" -a "$have_openssl_des" = "1"; then
      make_keyring=yes
	  OPENSSL_LIBS=-lcrypto
	  crypto_lib="OpenSSL"
   fi
fi
AC_SUBST(OPENSSL_LIBS)
if test "$make_keyring" != "yes"; then
   AC_MSG_WARN([OpenSSL library not found, Keyring will not be built])
fi

keyring_plugin=no;
if test "x$make_keyring" = "xyes" -a "x$plugin_support" = "xyes"; then
   keyring_plugin=yes;
fi


make_sqlite=no
if test "$have_libsqlite3" = "1" -a "$have_sqlite3_h" = "1"; then
	make_sqlite=yes
else
	AC_MSG_WARN([Sqlite library not found, jpsqlite will not be built])
fi
sqlite_plugin=no;
if test "x$make_sqlite" = "xyes" -a "x$plugin_support" = "xyes"; then
	sqlite_plugin=yes;
fi

AM_CONDITIONAL(MAKE_KEYRING, test "x$keyring_plugin" = "xyes")
AM_CONDITIONAL(MAKE_EXPENSE, test "x$plugin_support" = "xyes")
AM_CONDITIONAL(MAKE_JPSQLITE, test "x$sqlite_plugin" = "xyes")
AM_CONDITIONAL(MAKE_SYNCTIME, test "x$plugin_support" = "xyes")

dnl * Work out whether dialer can be built

dnl check operating system for jpilot-dialer
dialer=no
if test "$have_termio" = "1"; then
   case "${build_os}" in
      linux-gnu)     dialer=yes  ;;

      *) AC_MSG_WARN(
		   [Operating system ${build_os} not supported by jpilot-dialer]) ;
			dialer=no ;;

   esac
fi

AM_CONDITIONAL(JPILOT_DIALER, test "x$dialer" = "xyes")

dnl ############################################################################
dnl Miscellaneous definitions
dnl ############################################################################

dnl Define BASE_DIR in which is passed on to users in Jpilot help window
if test "x$prefix" = "xNONE"; then
   AC_DEFINE_UNQUOTED(BASE_DIR, "$ac_default_prefix", [ BASE_DIR ])
else
   AC_DEFINE_UNQUOTED(BASE_DIR, "$prefix", [ BASE_DIR ])
fi

dnl For Irix Systems
abilib=$ABILIB
if test "x$abilib" = "x"; then
   AC_DEFINE_UNQUOTED(ABILIB, "lib", [ ABILIB ])
else
   AC_DEFINE_UNQUOTED(ABILIB, "$abilib", [ ABILIB ])
fi
AC_SUBST(ABILIB)

dnl ############################################################################
dnl Setup complete.  Create the specified files
dnl ############################################################################
AC_CONFIG_FILES([Makefile
                 Expense/Makefile
                 jpsqlite/Makefile
                 SyncTime/Makefile
                 KeyRing/Makefile
                 dialer/Makefile
                 m4/Makefile
                 po/Makefile.in
                 icons/Makefile
                 docs/Makefile
                 empty/Makefile
                 jpilot.spec
                 SlackBuild ])

AC_OUTPUT

dnl Make SlackBuild executable
chmod +x SlackBuild

dnl ******************************
dnl Configuration messages
dnl ******************************
AC_MSG_RESULT()
AC_MSG_RESULT(This package is configured for the following features:)
AC_MSG_RESULT(------------------------------------------------------)
AC_MSG_RESULT(Compiling with plugin support..........   $enable_plugins)
AC_MSG_RESULT(Compiling with private record support..   $enable_private)
AC_MSG_RESULT(Compiling with Datebk support..........   $enable_datebk)
AC_MSG_RESULT(Compiling with Manana support..........   $enable_manana)
AC_MSG_RESULT(Compiling with Prometheon support......   $enable_prometheon)
AC_MSG_RESULT(Compiling Expense plugin...............   $plugin_support)
AC_MSG_RESULT(Compiling jpsqlite plugin..............   $sqlite_plugin)
AC_MSG_RESULT(Compiling SyncTime plugin..............   $plugin_support)
AC_MSG_RESULT(Compiling KeyRing plugin...............   $keyring_plugin)
AC_MSG_RESULT(Compiling dialer add-on................   $dialer)
AC_MSG_RESULT(Cryptographic library..................   $crypto_lib)
AC_MSG_RESULT(GTK-3 support..........................   yes)
AC_MSG_RESULT(Stock buttons (icons on buttons in GUI)   $enable_stock_buttons)
AC_MSG_RESULT(NLS support (foreign languages)........   $USE_NLS)
AC_MSG_RESULT(Compiler Options.......................   $CFLAGS)
AC_MSG_RESULT(Prefix directory.......................   $prefix)
AC_MSG_RESULT(pilot-link headers.....................   $pilot_incl)
AC_MSG_RESULT(USB support enabled....................   yes)
AC_MSG_RESULT(pilot-link version found...............   $pl_version.$pl_major.$pl_minor$pl_patch)
AC_MSG_RESULT()
AC_MSG_RESULT(Now type make to compile)
AC_MSG_RESULT()

