dnl
dnl File:        configure.in
dnl Revision:    $Id: configure.ac,v 1.4 2010/06/11 12:04:29 j-nijhof Exp $
dnl Created:     2010/06/09
dnl Author:      Jeroen Nijhof <jeroen@jeroennijhof.nl>
dnl              Benoit Donneaux <benoit.donneaux@gmail.com>
dnl
dnl Process this file with autoconf to produce a configure script
dnl You need autoconf 2.59 or better!
dnl
dnl ---------------------------------------------------------------------------

AC_PREREQ(2.59)
AC_COPYRIGHT([
See the included file: COPYING for copyright information.
])
AC_INIT(pam_tacplus, 1.4.0, [jeroen@jeroennijhof.nl,pawel.krawczyk@hush.com])

AC_CONFIG_AUX_DIR(config)
AM_INIT_AUTOMAKE([foreign])
AC_CONFIG_SRCDIR([pam_tacplus.c])
AC_CONFIG_HEADER([config.h])
AC_CONFIG_MACRO_DIR([config])

dnl --------------------------------------------------------------------
dnl Checks for programs.
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
LT_INIT([disable-static])
AM_PROG_CC_C_O

dnl --------------------------------------------------------------------
dnl Checks for libraries, but don't use -lpam for everything
AC_CHECK_LIB(pam, pam_start, HAVE_LIBPAM=1)

case "$host" in
	sparc-* | sparc64-*)
		LIBS="$LIBS -lresolv";;
esac

dnl --------------------------------------------------------------------
dnl Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([arpa/inet.h fcntl.h netdb.h netinet/in.h stdlib.h string.h strings.h sys/socket.h sys/time.h syslog.h unistd.h])
AC_CHECK_HEADER(security/pam_appl.h, [], [AC_MSG_ERROR([PAM libraries missing. Install with "yum install pam-devel" or "apt-get install libpam-dev".])] )

dnl --------------------------------------------------------------------
dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_HEADER_TIME

AC_ENABLE_SHARED(yes)
AC_ENABLE_STATIC(no)
AC_DISABLE_STATIC

AC_ARG_ENABLE(am-ldcflags, AS_HELP_STRING([--disable-am-ldcflags], [do not add various 'AM_CFLAGS/AM_LDFLAGS']))
if test "x$enable_am_ldcflags" != "xno"; then
   for flag in -fstack-protector-all -Wl,-z,relro -Wl,-z,now -fPIE -pie -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=2; do
	my_save_cflags="$CFLAGS"
	CFLAGS=$flag
	AC_MSG_CHECKING([whether CC supports $flag])
	AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])],
	    [AC_MSG_RESULT([yes])]
	    [AM_CFLAGS="$AM_CFLAGS $flag"]
	    [AM_LDFLAGS="$AM_LDFLAGS $flag"],
	    [AC_MSG_RESULT([no])]
	)
	CFLAGS="$my_save_cflags"
    done
    AC_SUBST([AM_CFLAGS])
    AC_SUBST([AM_LDFLAGS])
fi

dnl --------------------------------------------------------------------
dnl Checks for library functions.
AC_FUNC_REALLOC
AC_FUNC_SELECT_ARGTYPES
AC_TYPE_SIGNAL
AC_CHECK_FUNCS([bzero gethostbyname gettimeofday inet_ntoa select socket logwtmp])

dnl --------------------------------------------------------------------
dnl Switch for pam module dir
AC_ARG_ENABLE([pamdir], [AS_HELP_STRING([--enable-pamdir],
              [Location to install the pam module ($libdir/security)])],
              [pamdir=$enableval], [pamdir=$libdir/security])
AC_SUBST(pamdir)

AC_ARG_ENABLE(doc, AS_HELP_STRING([--disable-doc], [do not build docs]))
AM_CONDITIONAL(DOC, test "x$enable_doc" != "xno")

AC_ARG_ENABLE(openssl,[  --disable-openssl  disables OpenSSL random number, etc],
   [
      if test "x$enableval" = "xyes" ; then
            AX_CHECK_OPENSSL([
                    AC_CHECK_LIB(crypto, MD5_Init)
                    AC_CHECK_LIB(crypto, RAND_pseudo_bytes)
                    AC_CHECK_HEADERS([ openssl/md5.h openssl/rand.h])]
                  ,[AC_MSG_ERROR([No openssl found.])])
      fi
    ],[
     AX_CHECK_OPENSSL(,[AC_MSG_ERROR([No openssl found. using builtin crypto])])
])


dnl --------------------------------------------------------------------
dnl Generate made files
AC_CONFIG_FILES([Makefile
		 libtac.pc
                 pam_tacplus.spec])
AC_OUTPUT
