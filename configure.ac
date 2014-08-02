
AC_PREREQ([2.68])
AC_INIT([urlsnarf], [1.0], [damon@petta.org])
AC_CONFIG_SRCDIR([src/base64.c])
AM_INIT_AUTOMAKE
AC_CONFIG_HEADERS([config.h])

dnl Initialize prefix.
if test "$prefix" = "NONE"; then
   prefix="/usr/local"
fi

dnl Checks for programs.
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_RANLIB

AC_PREFIX_DEFAULT(/usr)

if test "$prefix" = "NONE"; then
   prefix=$ac_default_prefix
   echo $prefix XXX
fi

AC_CHECK_LIB([net], [main])
AC_CHECK_LIB([pcap], [main])
AC_CHECK_LIB([nids], [main])
AC_CHECK_LIB([nsl], [main])
AC_CHECK_LIB([resolv], [main])
AC_CHECK_LIB([rpcsvc], [main])
AC_SEARCH_LIBS([json_object_get], [json-c json], [HAVE_JSON=yes], [], [])


# Checks for header files.
AC_CHECK_HEADERS([arpa/inet.h netinet/in.h stdlib.h string.h sys/ioctl.h sys/param.h sys/socket.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T

# Checks for library functions.
AC_FUNC_MALLOC
AC_CHECK_FUNCS([isascii memset regcomp strchr strerror strncasecmp strtol])


dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_STRUCT_TM
dnl XXX - Solaris sux.
AC_CHECK_TYPE(u_int32_t, uint32_t)
AC_CHECK_TYPE(u_int64_t, uint64_t)
dnl XXX - Linux sux.
AC_CHECK_TYPE(in_addr_t, u_int32_t)
CFLAGS="$CFLAGS -D_BSD_SOURCE"

dnl Checks for library functions.
AC_PROG_GCC_TRADITIONAL
AC_FUNC_MEMCMP
AC_TYPE_SIGNAL
AC_FUNC_STRFTIME
AC_CHECK_FUNCS(gethostname socket strdup strstr)
AC_CHECK_LIB(rpcsvc, xdr_fhstatus)
dnl XXX - Solaris sux.
AC_CHECK_LIB(socket, socket)
AC_CHECK_LIB(nsl, gethostbyname)
dnl XXX - feh, everything except OpenBSD sux.
AC_CHECK_LIB(resolv, dn_expand)
AC_CHECK_LIB(resolv, __dn_expand)
AC_REPLACE_FUNCS(dirname strlcpy strlcat strsep)
needmd5=no
AC_CHECK_FUNCS(MD5Update, , [needmd5=yes])
if test $needmd5 = yes; then
   LIBOBJS="$LIBOBJS md5.o"
fi
neederr=no
AC_CHECK_FUNCS(warnx, , [neederr=yes])
if test $neederr = yes; then
   LIBOBJS="$LIBOBJS err.o"
fi
needethers=no
AC_CHECK_FUNCS(ether_ntoa, , [needethers=yes])
if test $needethers = yes; then
   LIBOBJS="$LIBOBJS ethers.o"
fi

dnl Checks for BSD tunnel device
dnl if test "x$ac_cv_header_net_if_tun_h" = "xyes"; then
dnl   TCPHIJACK="tcphijack"
dnl fi
AC_SUBST(TCPHIJACK)

dnl Checks for X11
if test "x$no_x" = "x"; then
   WEBSPY="webspy"; AC_SUBST(WEBSPY)
fi


dnl Checks for libnet
AC_MSG_CHECKING(for libnet)
AC_ARG_WITH(libnet,
[  --with-libnet=DIR       use libnet in DIR],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
     AC_MSG_RESULT($withval)
     if test -f $withval/include/libnet.h -a -f $withval/lib/libnet.a; then
        owd=`pwd`
        if cd $withval; then withval=`pwd`; cd $owd; fi
	if test -f $withval/bin/libnet-config; then
	   CFLAGS="$CFLAGS `$withval/bin/libnet-config --defines`"
	elif test -f $withval/libnet-config; then
	   CFLAGS="$CFLAGS `$withval/libnet-config --defines`"
	else
	   CFLAGS="$CFLAGS `libnet-config --defines`"
	fi
	LNETINC="-I$withval/include"
	LNETLIB="-L$withval/lib -lnet"
     else
        AC_ERROR(libnet.h or libnet.a not found in $withval)
     fi
     ;;
  esac ],
[ if test -f ${prefix}/include/libnet.h; then
     CFLAGS="$CFLAGS `${prefix}/bin/libnet-config --defines`"
     LNETINC="-I${prefix}/include"
     LNETLIB="-L${prefix}/lib -lnet"
  elif test -f /usr/include/libnet.h; then
     CFLAGS="$CFLAGS `libnet-config --defines`"
     LNETLIB="-lnet"
  else
     AC_MSG_RESULT(no)
     AC_ERROR(libnet not found)
  fi
  AC_MSG_RESULT(yes) ]
)
AC_SUBST(LNETINC)
AC_SUBST(LNETLIB)

dnl Checks for libnids
AC_MSG_CHECKING(for libnids)
AC_ARG_WITH(libnids,
[  --with-libnids=DIR      use libnids in DIR],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
     AC_MSG_RESULT($withval)
     if test -f $withval/src/nids.h -a -f $withval/src/libnids.a; then
        owd=`pwd`
        if cd $withval; then withval=`pwd`; cd $owd; fi
	NIDSINC="-I$withval/src"
	NIDSLIB="-L$withval/src -lnids"
     elif test -f $withval/include/nids.h -a -f $withval/lib/libnids.a; then
	owd=`pwd`
	if cd $withval; then withval=`pwd`; cd $owd; fi
	NIDSINC="-I$withval/include"
	NIDSLIB="-L$withval/lib -lnids"
     else
        AC_ERROR(nids.h or libnids.a not found in $withval)
     fi
     ;;
  esac ],
[ if test -f ${prefix}/include/nids.h; then
     NIDSINC="-I${prefix}/include"
     NIDSLIB="-L${prefix}/lib -lnids"
  elif test -f /usr/include/nids.h; then
     NIDSLIB="-lnids"
  else
     AC_MSG_RESULT(no)
     AC_ERROR(libnids not found)
  fi
  AC_MSG_RESULT(yes) ]
)
save_cppflags="$CPPFLAGS"
CPPFLAGS="$NIDSINC"
AC_MSG_CHECKING(whether libnids version is good)
AC_EGREP_HEADER(pcap_filter, nids.h, AC_MSG_RESULT(yes),
   [ AC_MSG_RESULT(no); AC_ERROR(libnids version 1.13 or greater required) ])
CPPFLAGS="$save_cppflags"
AC_SUBST(NIDSINC)
AC_SUBST(NIDSLIB)



AC_CONFIG_FILES([Makefile
                 src/missing/Makefile
                 src/Makefile])
AC_OUTPUT
