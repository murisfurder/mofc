# $Id: configure.ac,v 1.4 2005/06/07 08:39:37 mihajlov Exp $

AC_INIT(MOF Compiler, 0.7.1, sblim-devel@lists.sourceforge.net,mofc)
AC_CONFIG_SRCDIR([hash.c])
AC_CONFIG_HEADER([config.h])

AM_INIT_AUTOMAKE

AC_CANONICAL_HOST
case $host_os in
     openedition*) CPPFLAGS=-W"c,SSCOMM" 
		CPPFLAGS="-D_OPEN_SOURCE=3 -D_ENHANCED_ASCII_EXT=0xFFFFFFFF $CPPFLAGS"
		CFLAGS=-W"c,ASCII,dll,expo,langlvl(extended),float"
		LDFLAGS=-W"l,XPLINK,dll,EDIT=NO"
		;;
     **)	CFLAGS=-Wall
		CFLAGS="-g $CFLAGS"
		;;
esac

AC_ARG_ENABLE(relax-mofsyntax,
	      [AC_HELP_STRING([--enable-relax-mofsyntax],
		              [be less strict when parsing MOFs.])])
AC_ARG_ENABLE(testscanner,
	      [AC_HELP_STRING([--enable-testscanner],
		              [produce a standalone scanner for testing.])])
AC_ARG_VAR(SFCB_DIR,
      [set SFCB_DIR to the directory containing the SFCB sources, 
       if configure fails to find it.])

AC_ARG_WITH(sfcb,
	    [AC_HELP_STRING([--with-sfcb],
		              [use sfcb backend. In that case this package must
			       be in a sub-directory of the sfcb package.])])


if test x"$SFCB_DIR" != x && test "$with_sfcb" != yes ; then
   AC_WARN([SFCB_DIR has been specified ... assuming --with_sfcb])
   with_sfcb=yes
fi

unset MOFCNAME

if test "$with_sfcb" == yes; then
   if test x"$SFCB_DIR" = x; then
      SFCB_DIR=..
   fi
   MOFCNAME=sfcbmof
   BACKEND_STRING=sfcb
   BACKEND_OBJ='backend_sfcb.$(OBJEXT)'
   BACKEND_LIBS="-L $SFCB_DIR/.libs -lsfcBrokerCore"
   BACKEND_INCLUDES="-I $SFCB_DIR"
   AC_DEFINE(BACKEND_INFO,"SFCB Backend",[Backend Identification String])
else
   BACKEND_STRING=default
   BACKEND_OBJ='backend_print.$(OBJEXT)'
   BACKEND_LIBS=
   BACKEND_INCLUDES=
   AC_DEFINE(BACKEND_INFO,"Default Backend",[Backend Identification String])
fi

if test "$CFLAGS" != ""; then
  user_cflags_set=yes
else
  user_cflags_set=no
fi


# Checks for programs.
AC_PROG_CC
AC_PROG_YACC
AC_PROG_LEX

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([stddef.h stdlib.h string.h unistd.h])
if test "$with_sfcb" = yes; then
   save_CPPFLAGS=$CPPFLAGS
   CPPFLAGS="-I $SFCB_DIR $CPPFLAGS"
   AC_CHECK_HEADER(objectImpl.h,,
	[AC_MSG_ERROR([sorry, could not find SFCB include files. Hint: set SFCB_DIR environment variable.])])
   CPPFLAGS=$save_CPPFLAGS
fi;

# Checks for libraries
if test "$with_sfcb" = yes && test x"$sfcb_recursed" = x; then
   save_LDFLAGS=$LDFLAGS
   LDFLAGS="-L $SFCB_DIR/.libs $LDFLAGS"
   AC_CHECK_LIB(sfcBrokerCore,main,,
       [AC_MSG_ERROR([sorry, could not find SFCB libraries. Hint: set SFCB_DIR environment variable.])])
   LDFLAGS=$save_LDFLAGS
fi;

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_REALLOC
AC_CHECK_FUNCS([strcasecmp strchr strdup strrchr strstr])

# Defines
if test "$enable_relax_mofsyntax" = yes; then
  AC_DEFINE(RELAXED_MOF,,[Relaxed MOF Parsing])
fi

AM_CONDITIONAL(TESTSCANNER,[test "$enable_testscanner" == yes]) 
AM_CONDITIONAL(ALTERNATE_MOFCNAME,[test x"$MOFCNAME" != x]) 

AC_SUBST(MOFCNAME)
AC_SUBST(BACKEND_OBJ)
AC_SUBST(BACKEND_LIBS)
AC_SUBST(BACKEND_INCLUDES)

# Output control
echo =================================================================
echo mofc configuration complete
echo configured for $BACKEND_STRING backend
if test "$with_sfcb" == yes
then
echo SFCB_DIR=$SFCB_DIR
fi
echo =================================================================


AC_CONFIG_FILES([Makefile])
AC_OUTPUT