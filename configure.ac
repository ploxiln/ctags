#	Copyright (c) 2009, Darren Hiebert
#
#	This source code is released for free distribution under the terms
#	of the GNU General Public License version 2 or (at your option) any
#	later version.

#	Process this file with autoconf to produce a configure script.

AC_PREREQ([2.59])
AC_INIT([universal-ctags],[5.99.4])

if ! test -e "${srcdir}/config.h.in"; then
   echo "---"
   echo "--- ${srcdir}/config.h.in WAS NOT FOUND." 1>&2
   echo "--- YOU MIGHT HAVE RUN autoconf ONLY" 1>&2
   echo "--- BUT YOU MAY HAVE TO RUN autogen.sh." 1>&2
   echo "---"
   exit 1
fi

# Report system info
# ------------------
program_name=[`grep 'PROGRAM_NAME  *"' $srcdir/main/ctags.h | sed -e 's/.*"\([^"]*\)".*/\1/'`]
program_version=[`grep 'PROGRAM_VERSION  *"' $srcdir/main/ctags.h | sed -e 's/.*"\([^"]*\)".*/\1/'`]
echo "$program_name, version $program_version"
uname -mrsv 2>/dev/null

AM_INIT_AUTOMAKE([foreign subdir-objects tar-ustar])
AM_SILENT_RULES([yes])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])

m4_ifndef([PKG_CHECK_EXISTS], [m4_fatal([must install pkg-config 0.18 or later before running ./autogen.sh])])

AC_CANONICAL_BUILD
AC_CANONICAL_HOST

AH_TEMPLATE([PACKAGE], [Package name.])
AH_TEMPLATE([VERSION], [Package version.])
AH_TEMPLATE([clock_t],
	[Define to the appropriate type if <time.h> does not define this.])
AH_TEMPLATE([fpos_t],
	[Define to long if <stdio.h> does not define this.])
AH_TEMPLATE([L_tmpnam],
	[Define to the appropriate size for tmpnam() if <stdio.h> does not define
	this.])
AH_TEMPLATE([HAVE_STAT_ST_INO],
	[Define this macro if the field "st_ino" exists in struct stat in
	<sys/stat.h>.])
AH_TEMPLATE([HAVE_STATEMENT_EXPRESSION_EXT],
	[Define this macro if compiler supports statement expression non-standard
	C feature.])
AH_TEMPLATE([remove],
	[Define remove to unlink if you have unlink(), but not remove().])
AH_TEMPLATE([INT_MAX],
	[Define as the maximum integer on your system if not defined <limits.h>.])
AH_TEMPLATE([CUSTOM_CONFIGURATION_FILE],
	[You can define this label to be a string containing the name of a
	site-specific configuration file containing site-wide default options. The
	files /etc/ctags.conf and /usr/local/etc/ctags.conf are already checked,
	so only define one here if you need a file somewhere else.])
AH_TEMPLATE([ETAGS],
	[Define the string to check (in executable name) for etags mode])
AH_TEMPLATE([MACROS_USE_PATTERNS],
	[Define this label if you want macro tags (defined lables) to use patterns
	in the EX command by default (original ctags behavior is to use line
	numbers).])
AH_VERBATIM([DEFAULT_FILE_FORMAT], [
/* Define this as desired.
 * 1:  Original ctags format
 * 2:  Extended ctags format with extension flags in EX-style comment.
 */
#define DEFAULT_FILE_FORMAT	2
])
AH_TEMPLATE([CASE_INSENSITIVE_FILENAMES],
	[Define this label if your system uses case-insensitive file names])
AH_VERBATIM([EXTERNAL_SORT], [
/* Define this label to use the system sort utility (which is probably more
*  efficient) over the internal sorting algorithm.
*/
#ifndef INTERNAL_SORT
# undef EXTERNAL_SORT
#endif
])
AH_TEMPLATE([HAVE_ICONV],
	[Define this value if support multibyte character encoding.])
AH_TEMPLATE([ICONV_USE_LIB_PREFIX],
	[Define this value if the platform uses "lib" as prefix for iconv functions.])
AH_TEMPLATE([TMPDIR],
	[If you wish to change the directory in which temporary files are stored,
	define this label to the directory desired.])
AH_TEMPLATE([CHECK_REGCOMP],
	[Define this label if you wish to check the regcomp() function at run time
	for correct behavior. This function is currently broken on Cygwin.])
AH_TEMPLATE([NON_CONST_PUTENV_PROTOTYPE],
	[Define this is you have a prototype for putenv() in <stdlib.h>, but
	doesn't declare its argument as "const char *".])
AH_TEMPLATE([MSDOS_STYLE_PATH],
	[Define to 1 if your system uses MS-DOS style path.])
AH_TEMPLATE([ENABLE_GCOV],
	[Define to 1 if gcov is instrumented.])


# Define convenience macros
# -------------------------
# CHECK_HEADER_DEFINE(LABEL, HEADER [,ACTION-IF-FOUND [, ACTION-IF-NOT-FOUND ] ])
AC_DEFUN([CHECK_HEADER_DEFINE], [
	AC_MSG_CHECKING([if $1 is defined in $2])
	AC_EGREP_CPP([$2:$1],
[
#include <$2>
#ifdef $1
const char *result_yes = "$2:$1";
#endif
], [
	AC_MSG_RESULT(yes)
	[$3]
], [
	AC_MSG_RESULT(no)
	[$4]
]) ])

# CHECK_PROTO(FUNCTION, HEADER-FILE)
AC_DEFUN([CHECK_PROTO], [
	AC_EGREP_HEADER([[^A-Za-z0-9_]$1([ 	]+[A-Za-z0-9_]*)?[	 ]*\(],
	[$2],
	[],
	[
		AC_MSG_NOTICE([adding prototype for $1])
		AH_TEMPLATE(AS_TR_CPP([NEED_PROTO_$1]),
			    [If you receive error or warning messages indicating that you are missing a prototype for, or a type mismatch using, the following function, define this label and remake.])
		AC_DEFINE(AS_TR_CPP([NEED_PROTO_$1]))
	])
])

# PRETTY_ARG_VAR(VARIABLE, DESCRIPTION, DEFAULT)
# ----------------------------------------------
# Call AC_ARG_VAR with DEFAULT value.
AC_DEFUN([PRETTY_ARG_VAR], [
	AC_ARG_VAR([$1], [$2 [$3]])

	if test "${ac_env_$1_set}" = "set"; then
		$1="${ac_env_$1_value}"
	else
		$1="$3"
	fi
])

# Checks for configuration options
# --------------------------------

AC_ARG_WITH([sparse-cgcc],
	[AS_HELP_STRING([--with-sparse-cgcc],
		[use Sparse 'compiler wrapper' cgcc as C compiler [no]])])

AC_ARG_ENABLE([coverage-gcov],
	[AS_HELP_STRING([--enable-coverage-gcov],
		[enable 'gcov' coverage testing tool [no]])])

AC_ARG_ENABLE(readcmd,
	[AS_HELP_STRING([--disable-readcmd],
		[do not include readtags command during install])],
	[], [enable_readcmd=yes])

# AC_ARG_WITH(perl-regex,
# 	[AS_HELP_STRING([--with-perl-regex],
# 	    [use Perl pcre interface, if available])])

AC_ARG_ENABLE(etags,
	[AS_HELP_STRING([--enable-etags],
		[enable the installation of links for etags])])

AC_ARG_ENABLE(extended-format,
	[AS_HELP_STRING([--disable-extended-format],
		[disable extension flags; use original ctags file format only])],
	AC_DEFINE(DEFAULT_FILE_FORMAT, 1), AC_DEFINE(DEFAULT_FILE_FORMAT, 2))

AC_ARG_ENABLE(external-sort,
	[AS_HELP_STRING([--disable-external-sort],
		[use internal sort algorithm instead of sort program])])

AC_ARG_ENABLE(iconv,
	[AS_HELP_STRING([--disable-iconv],
		[disable multibyte character encoding support])])

AC_ARG_ENABLE(custom-config,
	[AS_HELP_STRING([--enable-custom-config=FILE],
		[enable custom config file for site-wide defaults])])

AC_ARG_ENABLE(macro-patterns,
	[AS_HELP_STRING([--enable-macro-patterns],
		[use patterns as default method to locate macros instead of line numbers])])

AC_ARG_ENABLE(tmpdir,
	[AS_HELP_STRING([--enable-tmpdir=DIR],
		[default directory for temporary files [ARG=/tmp]])],
	tmpdir_specified=yes)

AC_ARG_ENABLE([debugging],
	[AS_HELP_STRING([--enable-debugging],
		[enable debugging features])])

AC_ARG_ENABLE([static],
	[AS_HELP_STRING([--enable-static],
		[enable static linking (mainly for MinGW)])])

AC_ARG_PROGRAM

# Process configuration options
# -----------------------------

AM_CONDITIONAL(INSTALL_ETAGS, [test "x$enable_etags" = "xyes"])
AM_CONDITIONAL(USE_READCMD, [test "x$enable_readcmd" = "xyes"])

dnl AC_MSG_NOTICE(Change with $program_transform_name)
CTAGS_NAME_EXECUTABLE=`echo ctags | sed "$program_transform_name"`
AC_SUBST(CTAGS_NAME_EXECUTABLE)
ETAGS_NAME_EXECUTABLE=`echo etags | sed "$program_transform_name"`
AC_SUBST(ETAGS_NAME_EXECUTABLE)

AC_DEFINE_UNQUOTED(ETAGS, "$ETAGS_NAME_EXECUTABLE")

if test "$enable_custom_config" = no -o "$enable_custom_config" = yes ; then
	AC_MSG_NOTICE(no name supplied for custom configuration file)
elif test -n "$enable_custom_config" ; then
	AC_DEFINE_UNQUOTED(CUSTOM_CONFIGURATION_FILE, "$enable_custom_config")
	AC_MSG_NOTICE($enable_custom_config will be used as custom configuration file)
fi

if test "$enable_macro_patterns" = yes ; then
	AC_DEFINE(MACROS_USE_PATTERNS)
	AC_MSG_NOTICE(tag file will use patterns for macros by default)
fi

AM_CONDITIONAL(ENABLE_DEBUGGING, [test "x$enable_debugging" = "xyes"])


# Checks for programs
# -------------------

AC_PROG_CC
AC_PROG_CC_C99
AC_PROG_RANLIB
AC_C_BIGENDIAN

# https://www.gnu.org/software/autoconf-archive/ax_prog_cc_for_build.html
AX_PROG_CC_FOR_BUILD

# We need to compile and run a program on the build machine.
AC_MSG_CHECKING(for cc for build)
if test "$cross_compiling" = "yes"; then
  CC_FOR_BUILD="${CC_FOR_BUILD-cc}"
else
  CC_FOR_BUILD="${CC_FOR_BUILD-$CC}"
  CFLAGS_FOR_BUILD="${CFLAGS_FOR_BUILD-$CFLAGS}"
  CPPFLAGS_FOR_BUILD="${CPPFLAGS_FOR_BUILD-$CPPFLAGS}"
  LDFLAGS_FOR_BUILD="${LDFLAGS_FOR_BUILD-$LDFLAGS}"
fi
AC_MSG_RESULT($CC_FOR_BUILD)
AC_ARG_VAR(CC_FOR_BUILD,[build system C compiler])
AC_ARG_VAR(CFLAGS_FOR_BUILD,[CFLAGS for build system C compiler])
AC_ARG_VAR(CPPFLAGS_FOR_BUILD,[CPPFLAGS for build system C compiler])
AC_ARG_VAR(LDFLAGS_FOR_BUILD,[LDFLAGS for build system C compiler])

if test "${with_sparse_cgcc}" = "yes"; then
	REAL_CC="${CC}"
	CC="cgcc"
	AC_SUBST([REAL_CC])

	PRETTY_ARG_VAR([CGCC_CFLAGS], [cgcc specific flags],
		       [-Wsparse-all])
else
	CGCC_CFLAGS=""
fi

if test "${enable_coverage_gcov}" = "yes"; then
	COVERAGE_CFLAGS="--coverage"
	COVERAGE_LDFLAGS="--coverage"
	AC_DEFINE(ENABLE_GCOV)
fi
AC_SUBST([COVERAGE_CFLAGS])
AC_SUBST([COVERAGE_LDFLAGS])

AC_PROG_LN_S
AC_CHECK_PROG(STRIP, strip, strip, :)
AC_SYS_LARGEFILE

AC_CHECK_PROGS([perl_found], [perl], [yes], [no])
AM_CONDITIONAL([RUN_OPTLIB2C], [test "${perl_found}" = "yes"])

AC_PATH_PROGS(RST2MAN, [rst2man rst2man.py rst2man3 rst2man3.py], [no])
AM_CONDITIONAL([HAVE_RST2MAN], [test "x$RST2MAN" != "xno"])

RST2MAN_OPTIONS=
if ! test "x$RST2MAN" = "xno"; then
   RST2MAN_SUPPORTING_SYNTAX_HIGHLIGHT_OPTION=no
   AC_MSG_CHECKING(whether rst2mn has --syntax-highlight option)
   if $RST2MAN --help | grep -q -e --syntax-highlight; then
      RST2MAN_SUPPORTING_SYNTAX_HIGHLIGHT_OPTION=yes
      dnl See man/Makefile about the option
      RST2MAN_OPTIONS=--syntax-highlight=none
   fi
   AC_MSG_RESULT($RST2MAN_SUPPORTING_SYNTAX_HIGHLIGHT_OPTION)
fi
AC_SUBST(RST2MAN_OPTIONS)

# Checks for operating environment
# --------------------------------
in_git_repo=no
AC_MSG_CHECKING(building in a git repository)
if test -f "${srcdir}/.git/HEAD" || test -f "${srcdir}/.git"; then
	in_git_repo=yes
fi
AC_MSG_RESULT(${in_git_repo})

AC_CHECK_PROGS([GIT], [git])
if ! test "${GIT:+set}" = "set"; then
	in_git_repo=no
fi
AM_CONDITIONAL([BUILD_IN_GIT_REPO], [test "x${in_git_repo}" = "xyes"])

have_timeout=no
AC_CHECK_PROGS([TESTING_TIMEOUT], [timeout])
if test "${TESTING_TIMEOUT:+set}" = "set"; then
	have_timeout=yes
fi
AM_CONDITIONAL([HAVE_TIMEOUT], [test "x${have_timeout}" = "xyes"])

AC_CHECK_PROGS([PYTHON],
			   [python3 python3.8 python3.7 python3.6 python3.5 python], [])


# Check for temporary directory
AC_MSG_CHECKING(directory to use for temporary files)
if test -n "$enable_tmpdir"; then
	tmpdir="$enable_tmpdir"
elif test -n "$TMPDIR"; then
	tmpdir="$TMPDIR"
elif test -n "$TMP"; then
	tmpdir="$TMP"
elif test -n "$TEMP"; then
	tmpdir="$TEMP"
elif test -d "c:/"; then
	tmpdir="c:/"
else
	tmpdir="/tmp"
fi
if test -d $tmpdir ; then
	AC_MSG_RESULT($tmpdir)
	AC_DEFINE_UNQUOTED(TMPDIR, "$tmpdir")
else
	AC_MSG_ERROR($tmpdir does not exist)
fi

# Test for case-insensitive filenames
AC_MSG_CHECKING(for case-insensitive filenames)
touch conftest.cif
if test -f CONFTEST.CIF; then
	AC_MSG_RESULT(yes)
	AC_DEFINE(CASE_INSENSITIVE_FILENAMES)
else
	AC_MSG_RESULT(no)
fi
rm -f conftest.cif

AC_MSG_CHECKING(selected sort method)
if test no = "$enable_external_sort"; then
	AC_MSG_RESULT(simple internal algorithm)
else
	AC_MSG_RESULT(external sort utility)
	enable_external_sort=no
    AC_CHECK_PROG(sort_found, sort, yes, no)
	if test "$sort_found" = yes ; then
		AC_MSG_CHECKING(if sort accepts our command line)
		touch ${tmpdir}/sort.test
		sort -u -f -o ${tmpdir}/sort.test ${tmpdir}/sort.test 1>/dev/null 2>&1
		if test $? -ne 0 ; then
			AC_MSG_RESULT(no)
		else
			AC_MSG_RESULT(yes)
			AC_DEFINE(EXTERNAL_SORT)
			enable_external_sort=yes
		fi
		rm -f ${tmpdir}/sort.test
    fi
fi
if test "$enable_external_sort" != yes ; then
	AC_MSG_NOTICE(using internal sort algorithm as fallback)
fi


# Checks for header files
# -----------------------

AC_CHECK_HEADERS([direct.h dirent.h fcntl.h io.h stat.h types.h unistd.h])
AC_CHECK_HEADERS([sys/dir.h sys/stat.h sys/types.h sys/wait.h])

# Checks for header file macros
# -----------------------------

CHECK_HEADER_DEFINE(L_tmpnam, [stdio.h],, AC_DEFINE(L_tmpnam, 20))


# Checks for typedefs and types
# -----------------------------

AC_TYPE_OFF_T

AC_CHECK_HEADERS([stdbool.h])

# Checks for compiler characteristics
# -----------------------------------

AC_C_CONST
AC_OBJEXT
AC_EXEEXT
AC_C_TYPEOF

if test "${enable_static}" = "yes"; then
	LDFLAGS="$LDFLAGS -static"
fi

# Check for host type
case "$host" in
  i?86-*-mingw* | x86_64-*-mingw*)
	host_mingw=yes
	AC_DEFINE(MSDOS_STYLE_PATH)
	;;
esac
AM_CONDITIONAL([HOST_MINGW], [test "x${host_mingw}" = "xyes"])

AC_MSG_CHECKING(if compiler supports statement expressions)
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([],[
int main(int argc, char **argv) {return ({ int x = 1; x + argc;});}
])],[have_statement_expression_ext=yes],[have_statement_expression_ext=no])
AC_MSG_RESULT($have_statement_expression_ext)
if test yes = "$have_statement_expression_ext"; then
	AC_DEFINE(HAVE_STATEMENT_EXPRESSION_EXT)
fi


# Check if struct stat contains st_ino.
# MinGW has st_ino, but it doesn't work.
if test yes != "$host_mingw"; then
	AC_MSG_CHECKING(if struct stat contains st_ino)
	AC_COMPILE_IFELSE([AC_LANG_PROGRAM([
		#include <sys/stat.h>
		#include <stdlib.h>
		], [
		struct stat st;
		stat(".", &st);
		if (st.st_ino > 0)
			exit(0);
	])],[have_st_ino=yes],[have_st_ino=no])
	AC_MSG_RESULT($have_st_ino)
	if test yes = "$have_st_ino"; then
		AC_DEFINE(HAVE_STAT_ST_INO)
	fi
fi

PRETTY_ARG_VAR([EXTRA_CPPFLAGS], [extra (Objective) C/C++ preprocessor flags],
	       [-D_GNU_SOURCE -D__USE_GNU])
PRETTY_ARG_VAR([DEBUG_CPPFLAGS], [(Objective) C/C++ preprocessor debug flags],
	       [-DDEBUG])

PRETTY_ARG_VAR([EXTRA_CFLAGS], [extra C compiler flags],
	       [-std=gnu99])
PRETTY_ARG_VAR([WARNING_CFLAGS], [C compiler warning flags],
	       [-Wall])

# Checks for library functions
# ----------------------------
AC_CHECK_FUNCS(fnmatch,[have_fnmatch=yes],[
AC_LIBOBJ([fnmatch])
FNMATCH_CPPFLAGS="-I${srcdir}/fnmatch"
AC_DEFINE(HAVE_FNMATCH)
have_fnmatch=no])
AC_SUBST([FNMATCH_CPPFLAGS])
AC_CHECK_HEADERS(fnmatch.h)
AM_CONDITIONAL([HAVE_FNMATCH], [test "x$have_fnmatch" = "xyes"])

AC_CHECK_FUNCS(asprintf)
AC_CHECK_FUNCS(strstr)
AC_CHECK_FUNCS(strcasecmp stricmp, break)
AC_CHECK_FUNCS(strncasecmp strnicmp, break)

AC_CHECK_FUNCS(mkstemp, have_mkstemp=yes)
if test "$have_mkstemp" != yes ; then
	AC_CHECK_FUNCS(tempnam, have_tempnam=yes)
fi
if test "$have_mkstemp" != yes -a "$have_tempnam" != yes; then
	AC_CHECK_FUNCS(chmod)
	if test "$tmpdir_specified" = yes ; then
		AC_MSG_NOTICE(use of tmpnam overrides temporary directory selection)
	fi
fi

AC_CHECK_FUNCS(opendir findfirst _findfirst, break)
AC_CHECK_FUNCS(strerror)

AC_CHECK_FUNCS(truncate, have_truncate=yes)
# === Cannot nest AC_CHECK_FUNCS() calls
if test "$have_truncate" != yes  ; then
	AC_CHECK_FUNCS(ftruncate, have_ftruncate=yes)
	if test "$have_ftruncate" != yes ; then
		AC_CHECK_FUNCS(chsize)
	fi
fi

AC_CHECK_FUNCS(setenv, have_setenv=yes)
# === Cannot nest AC_CHECK_FUNCS() calls
if test "$have_setenv" != yes ; then
	AC_CHECK_FUNCS(putenv, have_putenv=yes)
	if test "$have_putenv" = yes ; then
		AC_EGREP_HEADER(putenv, stdlib.h, have_putenv_prototype=yes)
		if test "$have_putenv_prototype" = yes ; then
			AC_MSG_CHECKING(putenv prototype)
			AC_EGREP_HEADER([[^A-Za-zo-9_]putenv[ 	]*\(.*const.*\)[ 	]*;],
				stdlib.h, AC_MSG_RESULT(correct),
				[
					AC_MSG_RESULT(no const)
					AC_DEFINE(NON_CONST_PUTENV_PROTOTYPE)
				])
		fi
	fi
fi

dnl
dnl
dnl Check regcomp
dnl
dnl If it is found but broken, abort with a message.
dnl If it is found but cannot be test brokenness, define CHECK_REGCOMP.
dnl If it is not found, use bundled implementation.
dnl
dnl If CHECK_REGCOMP is defined, ctags itself check the brokenness at runtime
dnl (cross-compiling is assumed here).
dnl
dnl
dnl The final result can be check with ./ctags --list-features.
dnl
dnl
AC_CHECK_FUNCS(regcomp,[
AC_MSG_CHECKING(if regcomp works)
AC_RUN_IFELSE([AC_LANG_SOURCE([
#include <sys/types.h>
#include <regex.h>
int main(void) {
	regex_t patbuf;
	return (regcomp (&patbuf, "/hello/", 0) != 0);
}
])],[regcomp_works=yes],[regcomp_works=no],[AC_DEFINE(CHECK_REGCOMP)])
AC_MSG_RESULT($regcomp_works)
if test no = "$regcomp_works"; then
	AC_MSG_ERROR([regcomp() on this system is broken.])
fi
have_regcomp=yes
],[
dnl Using bundled regex implementation
AC_LIBOBJ([regex])
REGCOMP_CPPFLAGS="-I${srcdir}/gnu_regex -D__USE_GNU"
AC_DEFINE(HAVE_REGCOMP)
have_regcomp=no])
AC_SUBST([REGCOMP_CPPFLAGS])
AM_CONDITIONAL([HAVE_REGCOMP], [test "xyes" = "x$have_regcomp"])

have_scandir=no
AC_CHECK_FUNCS(scandir,have_scandir=yes)

have_dirent_h=no
AC_CHECK_HEADERS(dirent.h,have_dirent_h=yes)

dnl Dummy check for setting $PKG_CONFIG.
PKG_CHECK_EXISTS([dummy])
if test "${enable_static}" = "yes"; then
	PKG_CONFIG="$PKG_CONFIG --static"
fi

AC_ARG_ENABLE([xml],
	[AS_HELP_STRING([--disable-xml],
		[disable xml support])])

AH_TEMPLATE([HAVE_LIBXML],
	[Define this value if libxml is available.])
dnl About the condition of version
dnl see https://mail.gnome.org/archives/xml/2010-February/msg00008.html
AS_IF([test "x$enable_xml" != "xno"], [
	PKG_CHECK_MODULES(LIBXML, [libxml-2.0 >= 2.7.7],
			       [have_libxml=yes
			       AC_DEFINE(HAVE_LIBXML)],
			       [AS_IF([test "x$enable_xml" = "xyes"], [
			           AC_MSG_ERROR([libxml2 not found])])])
])
AM_CONDITIONAL(HAVE_LIBXML, test "x$have_libxml" = xyes)

if test "${enable_static}" = "yes"; then
	if test "${have_libxml}" = "yes"; then
		if test "${host_mingw}" = "yes"; then
			dnl -DLIBXML_STATIC needs to be added manually.
			LIBXML_CFLAGS="$LIBXML_CFLAGS -DLIBXML_STATIC"
		fi
	fi
fi

AC_ARG_ENABLE([json],
	[AS_HELP_STRING([--disable-json],
		[disable json support])])

AH_TEMPLATE([HAVE_JANSSON],
	[Define this value if jansson is available.])
dnl This enforces explicit feature usage regardless of the libraries
dnl available on the build system. This avoids automagic dependencies which
dnl can cause issues for source-based distros [1].
dnl [1]: https://wiki.gentoo.org/wiki/Project:Quality_Assurance/Automagic_dependencies
AS_IF([test "x$enable_json" != "xno"], [
	PKG_CHECK_MODULES(JANSSON, jansson,
			       [have_jansson=yes
			       AC_DEFINE(HAVE_JANSSON)],
			       [AS_IF([test "x$enable_json" = "xyes"], [
			           AC_MSG_ERROR([jansson not found])])])
])
AM_CONDITIONAL(HAVE_JANSSON, test "x$have_jansson" = xyes)

AH_TEMPLATE([HAVE_SECCOMP],
	[Define this value if libseccomp is available.])
AC_ARG_ENABLE([seccomp],
	[AS_HELP_STRING([--disable-seccomp],
		[disable seccomp support])])
AS_IF([test "x$enable_seccomp" != "xno"], [
	PKG_CHECK_MODULES(SECCOMP, libseccomp,
			       [have_seccomp=yes
			       AC_DEFINE(HAVE_SECCOMP)],
			       [AS_IF([test "x$enable_seccomp" = "xyes"], [
			           AC_MSG_ERROR([libseccomp not found])])])
])

AC_ARG_ENABLE([yaml],
	[AS_HELP_STRING([--disable-yaml],
		[disable yaml support])])

AH_TEMPLATE([HAVE_LIBYAML],
	[Define this value if libyaml is available.])
AS_IF([test "x$enable_yaml" != "xno"], [
	PKG_CHECK_MODULES(LIBYAML, yaml-0.1,
			       [have_libyaml=yes
			       AC_DEFINE(HAVE_LIBYAML)],
			       [AS_IF([test "x$enable_yaml" = "xyes"], [
			           AC_MSG_ERROR([libyaml not found])])])
])
AM_CONDITIONAL(HAVE_LIBYAML, test "x$have_libyaml" = xyes)


# Checks for missing prototypes
# -----------------------------
AC_MSG_NOTICE(checking for new missing prototypes)

CHECK_PROTO(stat,	sys/stat.h)
CHECK_PROTO(lstat,	sys/stat.h)
if test "$have_truncate" = yes ; then
	CHECK_PROTO(truncate, unistd.h)
fi
if test "$have_ftruncate" = yes ; then
	CHECK_PROTO(ftruncate, unistd.h)
fi

# Process library configuration options
# -------------------------------------

ICONV_LIBS=
if test "$enable_iconv" != no ; then
	save_LIBS="$LIBS"
	#
	# Working around for issue #1620
	#
	LIBS="$LIBS $LIBXML_LIBS"
	LIBS="$LIBS $JANSSON_LIBS"
	LIBS="$LIBS $SECCOMP_LIBS"
	LIBS="$LIBS $LIBYAML_LIBS"
	LIBS="$LIBS $ASPELL_LIBS"
	LIBS="$LIBS -liconv"
	#
	AC_MSG_CHECKING(for iconv_open or libiconv_open with -liconv)
	AC_LINK_IFELSE([AC_LANG_PROGRAM([
			#include <iconv.h>
		], [iconv_open("fr", "to")])], [
		#
		# iconv_open is available in libiconv.
		#
		LIBS="$save_LIBS"
		ICONV_LIBS=-liconv
		AC_MSG_RESULT([yes(iconv)])
		AC_DEFINE(HAVE_ICONV)
		], [AC_LINK_IFELSE([AC_LANG_PROGRAM([
		#include <iconv.h>
		], [libiconv_open("fr", "to")])],[
		#
		# libiconv_open is available in libiconv
		#
		LIBS="$save_LIBS"
		ICONV_LIBS=-liconv
		AC_MSG_RESULT([yes(libiconv_open)])
		AC_DEFINE(HAVE_ICONV)
		AC_DEFINE(ICONV_USE_LIB_PREFIX)
		],[LIBS="$save_LIBS"
		AC_MSG_RESULT(no)
		AC_CHECK_FUNC(iconv_open, [
		#
		# iconv_open is available in libc.
		# No extra -l flag is needed.
		#
		AC_DEFINE(HAVE_ICONV)
		], [
		AC_MSG_ERROR([Could not find libiconv. Please install libiconv and libiconv-devel.])])])
		])
	AC_SUBST([ICONV_LIBS])
fi

# Checks for declaration
# ----------------------
AC_CHECK_DECLS([__environ], [], [], [
[#include <unistd.h>]
])
AC_CHECK_DECLS([_NSGetEnviron],[],[],[
[#include <crt_externs.h>]
])
AC_CHECK_DECLS([strnlen],[have_strnlen=yes],[],[
[#include <string.h>]
])
AM_CONDITIONAL(HAVE_STRNLEN, test "x$have_strnlen" = xyes)

AC_MSG_CHECKING(whether strnlen is declared for build)
TEMP_DIR_FOR_CHECKING=$(mktemp -d || exit 1)
cat > "$TEMP_DIR_FOR_CHECKING/test.c" <<EOF
#include<string.h>
int main() { strnlen("abcd",1); return 0;}
EOF

if $CC_FOR_BUILD $CPPFLAGS_FOR_BUILD $CFLAGS_FOR_BUILD -o "$TEMP_DIR_FOR_CHECKING/test.o" "$TEMP_DIR_FOR_CHECKING/test.c" > /dev/null 2>&1 ; then
    have_strnlen_for_build=yes
else
    have_strnlen_for_build=no
fi
AC_MSG_RESULT($have_strnlen_for_build)
AM_CONDITIONAL(HAVE_STRNLEN_FOR_BUILD, test "x$have_strnlen_for_build" = xyes)

if test "$CTAGS_NAME_EXECUTABLE" != ctags ; then
	AC_MSG_NOTICE(Changing name of 'ctags' for $CTAGS_NAME_EXECUTABLE)
fi

if test "$ETAGS_NAME_EXECUTABLE" != etags ; then
	AC_MSG_NOTICE(Changing name of 'etags' for $ETAGS_NAME_EXECUTABLE)
fi

# Output files
# ------------

AC_CHECK_FUNCS(mblen)

AC_CONFIG_FILES([Makefile
		 man/ctags.1.rst
		 man/ctags-client-tools.7.rst
		 man/ctags-incompatibilities.7.rst
		 man/ctags-optlib.7.rst
		 man/ctags-lang-julia.7.rst
		 man/ctags-lang-python.7.rst
		 man/ctags-lang-verilog.7.rst
		 man/ctags-lang-inko.7.rst
		 man/readtags.1.rst
		 ])

AC_OUTPUT

# vim:ts=4:sw=4:
