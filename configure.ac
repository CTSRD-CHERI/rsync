dnl Process this file with autoconf to produce a configure script.

AC_INIT([rsync],[3.1.4dev],[http://rsync.samba.org/bugzilla.html])

AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([byteorder.h])
AC_CONFIG_HEADER(config.h)
AC_PREREQ([2.69])

AC_SUBST(RSYNC_VERSION, $PACKAGE_VERSION)
AC_MSG_NOTICE([Configuring rsync $PACKAGE_VERSION])

AC_DEFINE_UNQUOTED(RSYNC_VERSION, ["$PACKAGE_VERSION"], [rsync release version])

LDFLAGS=${LDFLAGS-""}

AC_CANONICAL_HOST

dnl define the directory for replacement function since AC_LIBOBJ does not
dnl officially support subdirs and fails with automake
AC_CONFIG_LIBOBJ_DIR([lib])

# We must decide this before testing the compiler.

# Please allow this to default to yes, so that your users have more
# chance of getting a useful stack trace if problems occur.

AC_MSG_CHECKING([whether to include debugging symbols])
AC_ARG_ENABLE(debug,
	AS_HELP_STRING([--disable-debug],[disable debugging symbols and features]))

if test x"$enable_debug" = x"no"; then
    AC_MSG_RESULT(no)
    ac_cv_prog_cc_g=no
else
    AC_MSG_RESULT([yes])
    dnl AC_DEFINE(DEBUG, 1, [Define to turn on debugging code that may slow normal operation])
    # leave ac_cv_prog_cc_g alone; AC_PROG_CC will try to include -g if it can
fi

dnl Checks for programs.
AC_PROG_CC
AC_PROG_CPP
AC_PROG_EGREP
AC_PROG_INSTALL
AC_PROG_MKDIR_P
AC_PROG_CC_STDC
AC_SUBST(SHELL)
AC_PATH_PROG([PERL], [perl])

AC_DEFINE([_GNU_SOURCE], 1,
          [Define _GNU_SOURCE so that we get all necessary prototypes])

if test x"$ac_cv_prog_cc_stdc" = x"no"; then
	AC_MSG_WARN([rsync requires an ANSI C compiler and you do not seem to have one])
fi

AC_ARG_ENABLE(profile,
	AS_HELP_STRING([--enable-profile],[turn on CPU profiling]))
if test x"$enable_profile" = x"yes"; then
	CFLAGS="$CFLAGS -pg"
fi


# Specifically, this turns on panic_action handling.
AC_ARG_ENABLE(maintainer-mode,
	AS_HELP_STRING([--enable-maintainer-mode],[turn on extra debug features]))
if test x"$enable_maintainer_mode" = x"yes"; then
	CFLAGS="$CFLAGS -DMAINTAINER_MODE"
fi


# This is needed for our included version of popt.  Kind of silly, but
# I don't want our version too far out of sync.
CFLAGS="$CFLAGS -DHAVE_CONFIG_H"

# If GCC, turn on warnings.
if test x"$GCC" = x"yes"; then
	CFLAGS="$CFLAGS -Wall -W"
fi

AC_ARG_WITH(included-popt,
        AS_HELP_STRING([--with-included-popt],[use bundled popt library, not from system]))

AC_ARG_WITH(included-zlib,
        AS_HELP_STRING([--with-included-zlib],[use bundled zlib library, not from system]))

AC_ARG_WITH(protected-args,
        AS_HELP_STRING([--with-protected-args],[make --protected-args option the default]))
if test x"$with_protected_args" = x"yes"; then
	AC_DEFINE_UNQUOTED(RSYNC_USE_PROTECTED_ARGS, 1, [Define to 1 if --protected-args should be the default])
fi

AC_ARG_WITH(rsync-path,
	AS_HELP_STRING([--with-rsync-path=PATH],[set default --rsync-path to PATH (default: rsync)]),
	[ RSYNC_PATH="$with_rsync_path" ],
	[ RSYNC_PATH="rsync" ])

AC_DEFINE_UNQUOTED(RSYNC_PATH, "$RSYNC_PATH", [location of rsync on remote machine])

AC_ARG_WITH(rsyncd-conf,
	AS_HELP_STRING([--with-rsyncd-conf=PATH],[set configuration file for rsync server to PATH (default: /etc/rsyncd.conf)]),
	[ if test ! -z "$with_rsyncd_conf" ; then
		case $with_rsyncd_conf in
			yes|no)
				RSYNCD_SYSCONF="/etc/rsyncd.conf"
				;;
			/*)
				RSYNCD_SYSCONF="$with_rsyncd_conf"
				;;
			*)
                                AC_MSG_ERROR(You must specify an absolute path to --with-rsyncd-conf=PATH)
				;;
		esac
	else
		RSYNCD_SYSCONF="/etc/rsyncd.conf"
	fi ],
	[ RSYNCD_SYSCONF="/etc/rsyncd.conf" ])

AC_DEFINE_UNQUOTED(RSYNCD_SYSCONF, "$RSYNCD_SYSCONF", [location of configuration file for rsync server])

AC_ARG_WITH(rsh,
	AS_HELP_STRING([--with-rsh=CMD],[set remote shell command to CMD (default: ssh)]))

AC_CHECK_PROG(HAVE_REMSH, remsh, 1, 0)
if test x$HAVE_REMSH = x1; then
	AC_DEFINE(HAVE_REMSH, 1, [Define to 1 if remote shell is remsh, not rsh])
fi

if test x"$with_rsh" != x; then
	RSYNC_RSH="$with_rsh"
else
	RSYNC_RSH="ssh"
fi
AC_DEFINE_UNQUOTED(RSYNC_RSH, "$RSYNC_RSH", [default -e command])

AC_CHECK_PROG(HAVE_YODL2MAN, yodl2man, 1, 0)
if test x$HAVE_YODL2MAN = x1; then
    MAKE_MAN=man
else
    MAKE_MAN=man-copy
fi

# Some programs on solaris are only found in /usr/xpg4/bin (or work better than others versions).
AC_PATH_PROG(SHELL_PATH, sh, /bin/sh, [/usr/xpg4/bin$PATH_SEPARATOR$PATH])
AC_PATH_PROG(FAKEROOT_PATH, fakeroot, /usr/bin/fakeroot, [/usr/xpg4/bin$PATH_SEPARATOR$PATH])

AC_ARG_WITH(nobody-group,
    AS_HELP_STRING([--with-nobody-group=GROUP],[set the default unprivileged group (default nobody or nogroup)]),
    [ NOBODY_GROUP="$with_nobody_group" ])

if test x"$with_nobody_group" = x; then
    AC_MSG_CHECKING([the group for user "nobody"])
    if grep '^nobody:' /etc/group >/dev/null 2>&1; then
	NOBODY_GROUP=nobody
    elif grep '^nogroup:' /etc/group >/dev/null 2>&1; then
	NOBODY_GROUP=nogroup
    else
	NOBODY_GROUP=nobody # test for others?
    fi
    AC_MSG_RESULT($NOBODY_GROUP)
fi

AC_DEFINE_UNQUOTED(NOBODY_USER, "nobody", [unprivileged user--e.g. nobody])
AC_DEFINE_UNQUOTED(NOBODY_GROUP, "$NOBODY_GROUP", [unprivileged group for unprivileged user])

# arrgh. libc in some old debian version screwed up the largefile
# stuff, getting byte range locking wrong
AC_CACHE_CHECK([for broken largefile support],rsync_cv_HAVE_BROKEN_LARGEFILE,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#define _FILE_OFFSET_BITS 64
#include <stdio.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(void)
{
	struct flock lock;
	int status;
	char tpl[32] = "/tmp/locktest.XXXXXX";
	int fd = mkstemp(tpl);
	if (fd < 0) {
		strcpy(tpl, "conftest.dat");
		fd = open(tpl, O_CREAT|O_RDWR, 0600);
	}

	lock.l_type = F_WRLCK;
	lock.l_whence = SEEK_SET;
	lock.l_start = 0;
	lock.l_len = 1;
	lock.l_pid = 0;
	fcntl(fd,F_SETLK,&lock);
	if (fork() == 0) {
		lock.l_start = 1;
		_exit(fcntl(fd,F_SETLK,&lock) == 0);
	}
	wait(&status);
	unlink(tpl);
	exit(WEXITSTATUS(status));
}
]])],[rsync_cv_HAVE_BROKEN_LARGEFILE=yes],[rsync_cv_HAVE_BROKEN_LARGEFILE=no],[rsync_cv_HAVE_BROKEN_LARGEFILE=cross])])
if test x"$rsync_cv_HAVE_BROKEN_LARGEFILE" != x"yes"; then
   AC_SYS_LARGEFILE
fi

ipv6type=unknown
ipv6lib=none
ipv6trylibc=yes

AC_ARG_ENABLE(ipv6,
	AS_HELP_STRING([--disable-ipv6],[do not even try to use IPv6]))
if test x"$enable_ipv6" != x"no"; then
	AC_MSG_CHECKING([ipv6 stack type])
	for i in inria kame linux-glibc linux-inet6 solaris toshiba v6d zeta cygwin; do
		case $i in
		inria)
			# http://www.kame.net/
			AC_EGREP_CPP(yes, [
#include <netinet/in.h>
#ifdef IPV6_INRIA_VERSION
yes
#endif],
				[ipv6type=$i;
				AC_DEFINE(INET6, 1, [true if you have IPv6])
				])
			;;
		kame)
			# http://www.kame.net/
			AC_EGREP_CPP(yes, [
#include <netinet/in.h>
#ifdef __KAME__
yes
#endif],
				[ipv6type=$i;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		linux-glibc)
			# http://www.v6.linux.or.jp/
			AC_EGREP_CPP(yes, [
#include <features.h>
#if defined(__GLIBC__) && __GLIBC__ >= 2 && __GLIBC_MINOR__ >= 1
yes
#endif],
				[ipv6type=$i;
AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		linux-inet6)
			# http://www.v6.linux.or.jp/
			if test -d /usr/inet6 -o -f /usr/inet6/lib/libinet6.a; then
				ipv6type=$i
				ipv6lib=inet6
				ipv6libdir=/usr/inet6/lib
				ipv6trylibc=yes;
				AC_DEFINE(INET6, 1, [true if you have IPv6])
				CFLAGS="-I/usr/inet6/include $CFLAGS"
			fi
			;;
		solaris)
			# http://www.sun.com
			AC_EGREP_CPP(yes, [
#include <netinet/ip6.h>
#ifdef __sun
yes
#endif],
				[ipv6type=$i;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		toshiba)
			AC_EGREP_CPP(yes, [
#include <sys/param.h>
#ifdef _TOSHIBA_INET6
yes
#endif],
				[ipv6type=$i;
				ipv6lib=inet6;
				ipv6libdir=/usr/local/v6/lib;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		v6d)
			AC_EGREP_CPP(yes, [
#include </usr/local/v6/include/sys/v6config.h>
#ifdef __V6D__
yes
#endif],
				[ipv6type=$i;
				ipv6lib=v6;
				ipv6libdir=/usr/local/v6/lib;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		zeta)
			AC_EGREP_CPP(yes, [
#include <sys/param.h>
#ifdef _ZETA_MINAMI_INET6
yes
#endif],
				[ipv6type=$i;
				ipv6lib=inet6;
				ipv6libdir=/usr/local/v6/lib;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		cygwin)
			AC_EGREP_CPP(yes, [
#include <netinet/in.h>
#ifdef _CYGWIN_IN6_H
yes
#endif],
				[ipv6type=$i;
				AC_DEFINE(INET6, 1, [true if you have IPv6])])
			;;
		esac
		if test "$ipv6type" != "unknown"; then
			break
		fi
	done
	AC_MSG_RESULT($ipv6type)

	AC_SEARCH_LIBS(getaddrinfo, inet6)
fi

dnl Do you want to disable use of locale functions
AC_ARG_ENABLE([locale],
	AS_HELP_STRING([--disable-locale],[disable locale features]))
AH_TEMPLATE([CONFIG_LOCALE],
[Undefine if you do not want locale features.  By default this is defined.])
if test x"$enable_locale" != x"no"; then
	AC_DEFINE(CONFIG_LOCALE)
fi

AC_MSG_CHECKING([whether to call shutdown on all sockets])
case $host_os in
	*cygwin* ) AC_MSG_RESULT(yes)
                   AC_DEFINE(SHUTDOWN_ALL_SOCKETS, 1,
			    [Define to 1 if sockets need to be shutdown])
		   ;;
	       * ) AC_MSG_RESULT(no);;
esac

AC_C_BIGENDIAN
AC_HEADER_DIRENT
AC_HEADER_TIME
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS(sys/fcntl.h sys/select.h fcntl.h sys/time.h sys/unistd.h \
    unistd.h utime.h grp.h compat.h sys/param.h ctype.h sys/wait.h \
    sys/ioctl.h sys/filio.h string.h stdlib.h sys/socket.h sys/mode.h \
    sys/un.h sys/attr.h mcheck.h arpa/inet.h arpa/nameser.h locale.h \
    netdb.h malloc.h float.h limits.h iconv.h libcharset.h langinfo.h \
    sys/acl.h acl/libacl.h attr/xattr.h sys/xattr.h sys/extattr.h \
    popt.h popt/popt.h linux/falloc.h netinet/in_systm.h netinet/ip.h \
    zlib.h)
AC_HEADER_MAJOR

AC_CACHE_CHECK([if makedev takes 3 args],rsync_cv_MAKEDEV_TAKES_3_ARGS,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <sys/types.h>
#ifdef MAJOR_IN_MKDEV
#include <sys/mkdev.h>
# if !defined makedev && (defined mkdev || defined _WIN32 || defined __WIN32__)
#  define makedev mkdev
# endif
#elif defined MAJOR_IN_SYSMACROS
#include <sys/sysmacros.h>
#endif

int main(void)
{
	dev_t dev = makedev(0, 5, 7);
	if (major(dev) != 5 || minor(dev) != 7)
		exit(1);
	return 0;
}
]])],[rsync_cv_MAKEDEV_TAKES_3_ARGS=yes],[rsync_cv_MAKEDEV_TAKES_3_ARGS=no],[rsync_cv_MAKEDEV_TAKES_3_ARGS=no])])
if test x"$rsync_cv_MAKEDEV_TAKES_3_ARGS" = x"yes"; then
   AC_DEFINE(MAKEDEV_TAKES_3_ARGS, 1, [Define to 1 if makedev() takes 3 args])
fi

AC_CHECK_SIZEOF(int)
AC_CHECK_SIZEOF(long)
AC_CHECK_SIZEOF(long long)
AC_CHECK_SIZEOF(short)
AC_CHECK_SIZEOF(int16_t)
AC_CHECK_SIZEOF(uint16_t)
AC_CHECK_SIZEOF(int32_t)
AC_CHECK_SIZEOF(uint32_t)
AC_CHECK_SIZEOF(int64_t)
AC_CHECK_SIZEOF(off_t)
AC_CHECK_SIZEOF(off64_t)
AC_CHECK_SIZEOF(time_t)

AC_C_INLINE

AC_TYPE_LONG_DOUBLE_WIDER
ac_cv_c_long_double=$ac_cv_type_long_double_wider
if test $ac_cv_c_long_double = yes; then
  AC_DEFINE([HAVE_LONG_DOUBLE],[1],[Define to 1 if the type `long double' works and has more range or precision than `double'.])
fi

AC_TYPE_UID_T
AC_CHECK_TYPES([mode_t,off_t,size_t,pid_t,id_t])
AC_TYPE_GETGROUPS
AC_CHECK_MEMBERS([struct stat.st_rdev,
		  struct stat.st_mtimensec,
		  struct stat.st_mtimespec.tv_nsec,
		  struct stat.st_mtim.tv_nsec],,,[
#ifdef HAVE_SYS_TYPES_H
#include <sys/types.h>
#endif
#ifdef HAVE_SYS_STAT_H
#include <sys/stat.h>
#endif
#ifdef HAVE_UNISTD_H
#include <unistd.h>
#endif])

TYPE_SOCKLEN_T

AC_CACHE_CHECK([for errno in errno.h],rsync_cv_errno, [
    AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <errno.h>]], [[int i = errno]])],[rsync_cv_errno=yes],[rsync_cv_have_errno_decl=no])])
if test x"$rsync_cv_errno" = x"yes"; then
   AC_DEFINE(HAVE_ERRNO_DECL, 1, [Define to 1 if errno is declared in errno.h])
fi

# The following test taken from the cvs sources
# If we can't find connect, try looking in -lsocket, -lnsl, and -linet.
# These need checks to be before checks for any other functions that
#    might be in the same libraries.
# The Irix 5 libc.so has connect and gethostbyname, but Irix 5 also has
# libsocket.so which has a bad implementation of gethostbyname (it
# only looks in /etc/hosts), so we only look for -lsocket if we need
# it.
AC_CHECK_FUNCS(connect)
if test x"$ac_cv_func_connect" = x"no"; then
    case "$LIBS" in
    *-lnsl*) ;;
    *) AC_CHECK_LIB(nsl_s, printf) ;;
    esac
    case "$LIBS" in
    *-lnsl*) ;;
    *) AC_CHECK_LIB(nsl, printf) ;;
    esac
    case "$LIBS" in
    *-lsocket*) ;;
    *) AC_CHECK_LIB(socket, connect) ;;
    esac
    case "$LIBS" in
    *-linet*) ;;
    *) AC_CHECK_LIB(inet, connect) ;;
    esac
    dnl We can't just call AC_CHECK_FUNCS(connect) here, because the value
    dnl has been cached.
    if test x"$ac_cv_lib_socket_connect" = x"yes" ||
       test x"$ac_cv_lib_inet_connect" = x"yes"; then
        # ac_cv_func_connect=yes
        # don't!  it would cause AC_CHECK_FUNC to succeed next time configure is run
        AC_DEFINE(HAVE_CONNECT, 1, [Define to 1 if you have the "connect" function])
    fi
fi

AC_SEARCH_LIBS(inet_ntop, resolv)

# For OS X, Solaris, HP-UX, etc.: figure out if -liconv is needed.  We'll
# accept either iconv_open or libiconv_open, since some include files map
# the former to the latter.
AC_SEARCH_LIBS(iconv_open, iconv)
AC_SEARCH_LIBS(libiconv_open, iconv)

AC_MSG_CHECKING([for iconv declaration])
AC_CACHE_VAL(am_cv_proto_iconv, [
    AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[
#include <stdlib.h>
#include <iconv.h>
extern
#ifdef __cplusplus
"C"
#endif
#if defined(__STDC__) || defined(__cplusplus)
size_t iconv (iconv_t cd, char * *inbuf, size_t *inbytesleft, char * *outbuf, size_t *outbytesleft);
#else
size_t iconv();
#endif
]], [[]])],[am_cv_proto_iconv_arg1=""],[am_cv_proto_iconv_arg1="const"])
      am_cv_proto_iconv="extern size_t iconv (iconv_t cd, $am_cv_proto_iconv_arg1 char * *inbuf, size_t *inbytesleft, char * *outbuf, size_t *outbytesleft);"])
    am_cv_proto_iconv=`echo "[$]am_cv_proto_iconv" | tr -s ' ' | sed -e 's/( /(/'`
AC_MSG_RESULT([$]{ac_t:-
         }[$]am_cv_proto_iconv)
AC_DEFINE_UNQUOTED(ICONV_CONST, $am_cv_proto_iconv_arg1,
		   [Define as const if the declaration of iconv() needs const.])

dnl AC_MSG_NOTICE([Looking in libraries: $LIBS])

AC_REPLACE_FUNCS([inet_ntop inet_pton])

AC_HAVE_TYPE([struct addrinfo], [#include <netdb.h>])
AC_HAVE_TYPE([struct sockaddr_storage], [#include <sys/types.h>
#include <sys/socket.h>])

# Irix 6.5 has getaddrinfo but not the corresponding defines, so use
#   builtin getaddrinfo if one of the defines don't exist
AC_CACHE_CHECK([whether defines needed by getaddrinfo exist],
               rsync_cv_HAVE_GETADDR_DEFINES,[
			AC_EGREP_CPP(yes, [
			#include <sys/types.h>
			#include <sys/socket.h>
			#include <netdb.h>
			#ifdef AI_PASSIVE
			yes
			#endif],
			rsync_cv_HAVE_GETADDR_DEFINES=yes,
			rsync_cv_HAVE_GETADDR_DEFINES=no)])
AS_IF([test x"$rsync_cv_HAVE_GETADDR_DEFINES" = x"yes" -a x"$ac_cv_type_struct_addrinfo" = x"yes"],[
	# Tru64 UNIX has getaddrinfo() but has it renamed in libc as
	# something else so we must include <netdb.h> to get the
	# redefinition.
	AC_CHECK_FUNCS(getaddrinfo, ,
		[AC_MSG_CHECKING([for getaddrinfo by including <netdb.h>])
		AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
		#include <sys/socket.h>
		#include <netdb.h>]], [[getaddrinfo(NULL, NULL, NULL, NULL);]])],[AC_MSG_RESULT([yes])
			AC_DEFINE(HAVE_GETADDRINFO, 1,
				[Define to 1 if you have the "getaddrinfo" function and required types.])],[AC_MSG_RESULT([no])
			AC_LIBOBJ([getaddrinfo])])])
    ],[AC_LIBOBJ([getaddrinfo])])

AC_CHECK_MEMBER([struct sockaddr.sa_len],
		[ AC_DEFINE(HAVE_SOCKADDR_LEN, 1, [Do we have sockaddr.sa_len?]) ],
		[],
		[
#include <sys/types.h>
#include <sys/socket.h>
])

AC_CHECK_MEMBER([struct sockaddr_in.sin_len],
		[ AC_DEFINE(HAVE_SOCKADDR_IN_LEN, 1, [Do we have sockaddr_in.sin_len?]) ],
		[],
		[
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
])

AC_CHECK_MEMBER([struct sockaddr_un.sun_len],
		[ AC_DEFINE(HAVE_SOCKADDR_UN_LEN, 1, [Do we have sockaddr_un.sun_len?]) ],
		[],
		[
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
])

AC_CHECK_MEMBER([struct sockaddr_in6.sin6_scope_id],
		[ AC_DEFINE(HAVE_SOCKADDR_IN6_SCOPE_ID, 1, [Do we have sockaddr_in6.sin6_scope_id?]) ],
		[],
		[
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
])

AC_HAVE_TYPE([struct stat64], [#include <stdio.h>
#if HAVE_SYS_TYPES_H
# include <sys/types.h>
#endif
#if HAVE_SYS_STAT_H
# include <sys/stat.h>
#endif
#if STDC_HEADERS
# include <stdlib.h>
# include <stddef.h>
#else
# if HAVE_STDLIB_H
#  include <stdlib.h>
# endif
#endif
])

# if we can't find strcasecmp, look in -lresolv (for Unixware at least)
#
AC_CHECK_FUNCS(strcasecmp)
if test x"$ac_cv_func_strcasecmp" = x"no"; then
    AC_CHECK_LIB(resolv, strcasecmp)
fi

AC_CHECK_FUNCS(aclsort)
if test x"$ac_cv_func_aclsort" = x"no"; then
    AC_CHECK_LIB(sec, aclsort)
fi

dnl At the moment we don't test for a broken memcmp(), because all we
dnl need to do is test for equality, not comparison, and it seems that
dnl every platform has a memcmp that can do at least that.
dnl AC_FUNC_MEMCMP

AC_FUNC_UTIME_NULL
AC_FUNC_ALLOCA
AC_CHECK_FUNCS(waitpid wait4 getcwd strdup chown chmod lchmod mknod mkfifo \
    fchmod fstat ftruncate strchr readlink link utime utimes lutimes strftime \
    memmove lchown vsnprintf snprintf vasprintf asprintf setsid strpbrk \
    strlcat strlcpy strtol mallinfo getgroups setgroups geteuid getegid \
    setlocale setmode open64 lseek64 mkstemp64 mtrace va_copy __va_copy \
    seteuid strerror putenv iconv_open locale_charset nl_langinfo getxattr \
    extattr_get_link sigaction sigprocmask setattrlist getgrouplist \
    initgroups utimensat posix_fallocate attropen setvbuf usleep)

dnl cygwin iconv.h defines iconv_open as libiconv_open
if test x"$ac_cv_func_iconv_open" != x"yes"; then
    AC_CHECK_FUNC(libiconv_open, [ac_cv_func_iconv_open=yes; AC_DEFINE(HAVE_ICONV_OPEN, 1)])
fi

dnl Preallocation stuff (also fallocate, posix_fallocate function tests above):

AC_CACHE_CHECK([for useable fallocate],rsync_cv_have_fallocate,[
AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <fcntl.h>
#include <sys/types.h>]], [[fallocate(0, 0, 0, 0);]])],[rsync_cv_have_fallocate=yes],[rsync_cv_have_fallocate=no])])
if test x"$rsync_cv_have_fallocate" = x"yes"; then
    AC_DEFINE(HAVE_FALLOCATE, 1, [Define to 1 if you have the fallocate function and it compiles and links without error])
fi

AC_MSG_CHECKING([for FALLOC_FL_PUNCH_HOLE])
AC_PREPROC_IFELSE([AC_LANG_SOURCE([[
        #define _GNU_SOURCE 1
        #include <linux/falloc.h>
        #ifndef FALLOC_FL_PUNCH_HOLE
        #error FALLOC_FL_PUNCH_HOLE is missing
        #endif
    ]])], [
        AC_MSG_RESULT([yes])
        AC_DEFINE([HAVE_FALLOC_FL_PUNCH_HOLE], [1], [Define if FALLOC_FL_PUNCH_HOLE is available.])
    ], [
        AC_MSG_RESULT([no])
    ]
)

AC_MSG_CHECKING([for FALLOC_FL_ZERO_RANGE])
AC_PREPROC_IFELSE([AC_LANG_SOURCE([[
        #define _GNU_SOURCE 1
        #include <linux/falloc.h>
        #ifndef FALLOC_FL_ZERO_RANGE
        #error FALLOC_FL_ZERO_RANGE is missing
        #endif
    ]])], [
        AC_MSG_RESULT([yes])
        AC_DEFINE([HAVE_FALLOC_FL_ZERO_RANGE], [1], [Define if FALLOC_FL_ZERO_RANGE is available.])
    ], [
        AC_MSG_RESULT([no])
    ]
)

AC_CACHE_CHECK([for SYS_fallocate],rsync_cv_have_sys_fallocate,[
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <sys/syscall.h>
#include <sys/types.h>]], [[syscall(SYS_fallocate, 0, 0, (loff_t)0, (loff_t)0);]])],[rsync_cv_have_sys_fallocate=yes],[rsync_cv_have_sys_fallocate=no])])
if test x"$rsync_cv_have_sys_fallocate" = x"yes"; then
    AC_DEFINE(HAVE_SYS_FALLOCATE, 1, [Define to 1 if you have the SYS_fallocate syscall number])
fi

if test x"$ac_cv_func_posix_fallocate" = x"yes"; then
    AC_MSG_CHECKING([whether posix_fallocate is efficient])
    case $host_os in
    *cygwin*)
	AC_MSG_RESULT(yes)
	AC_DEFINE(HAVE_EFFICIENT_POSIX_FALLOCATE, 1,
		  [Define if posix_fallocate is efficient (Cygwin)])
	;;
    *)
	AC_MSG_RESULT(no)
	;;
    esac
fi

dnl End of preallocation stuff

AC_CHECK_FUNCS(getpgrp tcgetpgrp)
if test $ac_cv_func_getpgrp = yes; then
    AC_FUNC_GETPGRP
fi

AC_ARG_ENABLE(iconv-open,
    AS_HELP_STRING([--disable-iconv-open],[disable all use of iconv_open() function]),
    [], [enable_iconv_open=$ac_cv_func_iconv_open])

if test x"$enable_iconv_open" != x"no"; then
    AC_DEFINE(USE_ICONV_OPEN, 1, [Define to 1 if you want rsync to make use of iconv_open()])
fi

AC_ARG_ENABLE(iconv,
    AS_HELP_STRING([--disable-iconv],[disable rsync's --iconv option]),
    [], [enable_iconv=$enable_iconv_open])
AH_TEMPLATE([ICONV_OPTION],
[Define if you want the --iconv option.  Specifing a value will set the
default iconv setting (a NULL means no --iconv processing by default).])
if test x"$enable_iconv" != x"no"; then
	if test x"$enable_iconv" = x"yes"; then
		AC_DEFINE(ICONV_OPTION, NULL)
	else
		AC_DEFINE_UNQUOTED(ICONV_OPTION, "$enable_iconv")
	fi
	AC_DEFINE(UTF8_CHARSET, "UTF-8", [String to pass to iconv() for the UTF-8 charset.])
fi

AC_CACHE_CHECK([whether chown() modifies symlinks],rsync_cv_chown_modifies_symlink,[
  AC_RUN_IFELSE([AC_LANG_SOURCE([[
#if HAVE_UNISTD_H
# include <unistd.h>
#endif
#include <stdlib.h>
#include <errno.h>
    main() {
	char const *dangling_symlink = "conftest.dangle";
	unlink(dangling_symlink);
	if (symlink("conftest.no-such", dangling_symlink) < 0) abort();
	if (chown(dangling_symlink, getuid(), getgid()) < 0 && errno == ENOENT) exit(1);
	exit(0);
    }]])],[rsync_cv_chown_modifies_symlink=yes],[rsync_cv_chown_modifies_symlink=no],[rsync_cv_chown_modifies_symlink=no])])
if test $rsync_cv_chown_modifies_symlink = yes; then
  AC_DEFINE(CHOWN_MODIFIES_SYMLINK, 1, [Define to 1 if chown modifies symlinks.])
fi

AC_CACHE_CHECK([whether link() can hard-link symlinks],rsync_cv_can_hardlink_symlink,[
  AC_RUN_IFELSE([AC_LANG_SOURCE([[
#if HAVE_UNISTD_H
# include <unistd.h>
#endif
#include <stdlib.h>
#include <errno.h>
#define FILENAME "conftest.dangle"
    main() {
	unlink(FILENAME);
	if (symlink("conftest.no-such", FILENAME) < 0) abort();
	unlink(FILENAME "2");
	if (link(FILENAME, FILENAME "2") < 0) exit(1);
	exit(0);
    }]])],[rsync_cv_can_hardlink_symlink=yes],[rsync_cv_can_hardlink_symlink=no],[rsync_cv_can_hardlink_symlink=no])])
if test $rsync_cv_can_hardlink_symlink = yes; then
  AC_DEFINE(CAN_HARDLINK_SYMLINK, 1, [Define to 1 if link() can hard-link symlinks.])
fi

AC_CACHE_CHECK([whether link() can hard-link special files],rsync_cv_can_hardlink_special,[
  AC_RUN_IFELSE([AC_LANG_SOURCE([[
#if HAVE_UNISTD_H
# include <unistd.h>
#endif
#include <stdlib.h>
#include <errno.h>
#define FILENAME "conftest.fifi"
    main() {
	unlink(FILENAME);
	if (mkfifo(FILENAME, 0777) < 0) abort();
	unlink(FILENAME "2");
	if (link(FILENAME, FILENAME "2") < 0) exit(1);
	exit(0);
    }]])],[rsync_cv_can_hardlink_special=yes],[rsync_cv_can_hardlink_special=no],[rsync_cv_can_hardlink_special=no])])
if test $rsync_cv_can_hardlink_special = yes; then
    AC_DEFINE(CAN_HARDLINK_SPECIAL, 1, [Define to 1 if link() can hard-link special files.])
fi

AC_CACHE_CHECK([for working socketpair],rsync_cv_HAVE_SOCKETPAIR,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <sys/types.h>
#include <sys/socket.h>

main() {
       int fd[2];
       exit((socketpair(AF_UNIX, SOCK_STREAM, 0, fd) != -1) ? 0 : 1);
}]])],[rsync_cv_HAVE_SOCKETPAIR=yes],[rsync_cv_HAVE_SOCKETPAIR=no],[rsync_cv_HAVE_SOCKETPAIR=cross])])
if test x"$rsync_cv_HAVE_SOCKETPAIR" = x"yes"; then
    AC_DEFINE(HAVE_SOCKETPAIR, 1, [Define to 1 if you have the "socketpair" function])
fi

AC_REPLACE_FUNCS([getpass])

if test x"$with_included_popt" != x"yes"; then
    AC_CHECK_LIB(popt, poptGetContext, , [with_included_popt=yes])
fi
if test x"$ac_cv_header_popt_popt_h" = x"yes"; then
    # If the system has /usr/include/popt/popt.h, we enable the
    # included popt because an attempt to "#include <popt/popt.h>"
    # would use our included header file anyway (due to -I.), and
    # might conflict with the system popt.
    with_included_popt=yes
elif test x"$ac_cv_header_popt_h" != x"yes"; then
    with_included_popt=yes
fi

AC_MSG_CHECKING([whether to use included libpopt])
if test x"$with_included_popt" = x"yes"; then
    AC_MSG_RESULT($srcdir/popt)
    BUILD_POPT='$(popt_OBJS)'
    CFLAGS="-I$srcdir/popt $CFLAGS"
    if test x"$ALLOCA" != x
    then
	# this can be removed when/if we add an included alloca.c;
	#  see autoconf documentation on AC_FUNC_ALLOCA
	AC_MSG_WARN([included libpopt will use malloc, not alloca (which wastes a small amount of memory)])
    fi
else
    AC_MSG_RESULT(no)
fi

# We default to using our zlib unless --with-included-zlib=no is given.
if test x"$with_included_zlib" != x"no"; then
    with_included_zlib=yes
elif test x"$ac_cv_header_zlib_h" != x"yes"; then
    with_included_zlib=yes
fi
if test x"$with_included_zlib" != x"yes"; then
    AC_CHECK_LIB(z, deflateParams, , [with_included_zlib=yes])
fi

AC_MSG_CHECKING([whether to use included zlib])
if test x"$with_included_zlib" = x"yes"; then
    AC_MSG_RESULT($srcdir/zlib)
    BUILD_ZLIB='$(zlib_OBJS)'
    CFLAGS="-I$srcdir/zlib $CFLAGS"
else
    AC_DEFINE(EXTERNAL_ZLIB, 1, [Define to 1 if using external zlib])
    AC_MSG_RESULT(no)
fi

AC_CACHE_CHECK([for unsigned char],rsync_cv_SIGNED_CHAR_OK,[
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[]], [[signed char *s = ""]])],[rsync_cv_SIGNED_CHAR_OK=yes],[rsync_cv_SIGNED_CHAR_OK=no])])
if test x"$rsync_cv_SIGNED_CHAR_OK" = x"yes"; then
    AC_DEFINE(SIGNED_CHAR_OK, 1, [Define to 1 if "signed char" is a valid type])
fi

AC_CACHE_CHECK([for broken readdir],rsync_cv_HAVE_BROKEN_READDIR,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[#include <sys/types.h>
#include <dirent.h>
main() { struct dirent *di; DIR *d = opendir("."); di = readdir(d);
if (di && di->d_name[-2] == '.' && di->d_name[-1] == 0 &&
di->d_name[0] == 0) exit(0); exit(1);} ]])],[rsync_cv_HAVE_BROKEN_READDIR=yes],[rsync_cv_HAVE_BROKEN_READDIR=no],[rsync_cv_HAVE_BROKEN_READDIR=cross])])
if test x"$rsync_cv_HAVE_BROKEN_READDIR" = x"yes"; then
    AC_DEFINE(HAVE_BROKEN_READDIR, 1, [Define to 1 if readdir() is broken])
fi

AC_CACHE_CHECK([for utimbuf],rsync_cv_HAVE_STRUCT_UTIMBUF,[
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
#include <utime.h>]], [[struct utimbuf tbuf;  tbuf.actime = 0; tbuf.modtime = 1; exit(utime("foo.c",&tbuf));]])],[rsync_cv_HAVE_STRUCT_UTIMBUF=yes],[rsync_cv_HAVE_STRUCT_UTIMBUF=no])])
if test x"$rsync_cv_HAVE_STRUCT_UTIMBUF" = x"yes"; then
    AC_DEFINE(HAVE_STRUCT_UTIMBUF, 1, [Define to 1 if you have the "struct utimbuf" type])
fi

AC_CACHE_CHECK([if gettimeofday takes tz argument],rsync_cv_HAVE_GETTIMEOFDAY_TZ,[
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <sys/time.h>
#include <unistd.h>]], [[struct timeval tv; gettimeofday(&tv, NULL);]])],[rsync_cv_HAVE_GETTIMEOFDAY_TZ=yes],[rsync_cv_HAVE_GETTIMEOFDAY_TZ=no])])
if test x"$rsync_cv_HAVE_GETTIMEOFDAY_TZ" != x"no"; then
    AC_DEFINE(HAVE_GETTIMEOFDAY_TZ, 1, [Define to 1 if gettimeofday() takes a time-zone arg])
fi

AC_CACHE_CHECK([for C99 vsnprintf],rsync_cv_HAVE_C99_VSNPRINTF,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <sys/types.h>
#include <stdarg.h>
void foo(const char *format, ...) {
       va_list ap;
       int len;
       char buf[5];

       va_start(ap, format);
       len = vsnprintf(0, 0, format, ap);
       va_end(ap);
       if (len != 5) exit(1);

       if (snprintf(buf, 3, "hello") != 5 || strcmp(buf, "he") != 0) exit(1);

       exit(0);
}
main() { foo("hello"); }
]])],[rsync_cv_HAVE_C99_VSNPRINTF=yes],[rsync_cv_HAVE_C99_VSNPRINTF=no],[rsync_cv_HAVE_C99_VSNPRINTF=cross])])
if test x"$rsync_cv_HAVE_C99_VSNPRINTF" = x"yes"; then
    AC_DEFINE(HAVE_C99_VSNPRINTF, 1, [Define to 1 if vsprintf has a C99-compatible return value])
fi


AC_CACHE_CHECK([for secure mkstemp],rsync_cv_HAVE_SECURE_MKSTEMP,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
main() {
  struct stat st;
  char tpl[20]="/tmp/test.XXXXXX";
  int fd = mkstemp(tpl);
  if (fd == -1) exit(1);
  unlink(tpl);
  if (fstat(fd, &st) != 0) exit(1);
  if ((st.st_mode & 0777) != 0600) exit(1);
  exit(0);
}]])],[rsync_cv_HAVE_SECURE_MKSTEMP=yes],[rsync_cv_HAVE_SECURE_MKSTEMP=no],[rsync_cv_HAVE_SECURE_MKSTEMP=cross])])
if test x"$rsync_cv_HAVE_SECURE_MKSTEMP" = x"yes"; then
    case $host_os in
    hpux*)
	dnl HP-UX has a broken mkstemp() implementation they refuse to fix,
	dnl so we noisily skip using it.  See HP change request JAGaf34426
	dnl for details. (sbonds)
	AC_MSG_WARN(Skipping broken HP-UX mkstemp() -- using mktemp() instead)
	;;
    *)
	AC_DEFINE(HAVE_SECURE_MKSTEMP, 1, [Define to 1 if mkstemp() is available and works right])
	;;
    esac
fi


AC_CACHE_CHECK([if mknod creates FIFOs],rsync_cv_MKNOD_CREATES_FIFOS,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <stdio.h>
#include <sys/stat.h>
#include <errno.h>
main() { int rc, ec; char *fn = "fifo-test";
unlink(fn); rc = mknod(fn,S_IFIFO,0600); ec = errno; unlink(fn);
if (rc) {printf("(%d %d) ",rc,ec); return ec;}
return 0;}]])],[rsync_cv_MKNOD_CREATES_FIFOS=yes],[rsync_cv_MKNOD_CREATES_FIFOS=no],[rsync_cv_MKNOD_CREATES_FIFOS=cross])])
if test x"$rsync_cv_MKNOD_CREATES_FIFOS" = x"yes"; then
    AC_DEFINE(MKNOD_CREATES_FIFOS, 1, [Define to 1 if mknod() can create FIFOs.])
fi

AC_CACHE_CHECK([if mknod creates sockets],rsync_cv_MKNOD_CREATES_SOCKETS,[
AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <stdio.h>
#include <sys/stat.h>
#include <errno.h>
main() { int rc, ec; char *fn = "sock-test";
unlink(fn); rc = mknod(fn,S_IFSOCK,0600); ec = errno; unlink(fn);
if (rc) {printf("(%d %d) ",rc,ec); return ec;}
return 0;}]])],[rsync_cv_MKNOD_CREATES_SOCKETS=yes],[rsync_cv_MKNOD_CREATES_SOCKETS=no],[rsync_cv_MKNOD_CREATES_SOCKETS=cross])])
if test x"$rsync_cv_MKNOD_CREATES_SOCKETS" = x"yes"; then
    AC_DEFINE(MKNOD_CREATES_SOCKETS, 1, [Define to 1 if mknod() can create sockets.])
fi

#
# The following test was mostly taken from the tcl/tk plus patches
#
AC_CACHE_CHECK([whether -c -o works],rsync_cv_DASHC_WORKS_WITH_DASHO,[
rm -rf conftest*
cat > conftest.$ac_ext <<EOF
int main() { return 0; }
EOF
${CC-cc} -c -o conftest..o conftest.$ac_ext
if test -f conftest..o; then
    rsync_cv_DASHC_WORKS_WITH_DASHO=yes
else
    rsync_cv_DASHC_WORKS_WITH_DASHO=no
fi
rm -rf conftest*
])
if test x"$rsync_cv_DASHC_WORKS_WITH_DASHO" = x"yes"; then
    OBJ_SAVE="#"
    OBJ_RESTORE="#"
    CC_SHOBJ_FLAG='-o $@'
else
    OBJ_SAVE='	@b=`basename $@ .o`;rm -f $$b.o.sav;if test -f $$b.o; then mv $$b.o $$b.o.sav;fi;'
    OBJ_RESTORE='	@b=`basename $@ .o`;if test "$$b.o" != "$@"; then mv $$b.o $@; if test -f $$b.o.sav; then mv $$b.o.sav $$b.o; fi; fi'
    CC_SHOBJ_FLAG=""
fi

AC_SUBST(OBJ_SAVE)
AC_SUBST(OBJ_RESTORE)
AC_SUBST(CC_SHOBJ_FLAG)
AC_SUBST(BUILD_POPT)
AC_SUBST(BUILD_ZLIB)
AC_SUBST(MAKE_MAN)

AC_PATH_PROG([STUNNEL], [stunnel], [stunnel], [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/sbin])
AC_PATH_PROG([STUNNEL4], [stunnel4], [$STUNNEL], [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/sbin])

AC_CHECK_FUNCS(_acl __acl _facl __facl)
#################################################
# check for ACL support

AC_MSG_CHECKING([whether to support ACLs])
AC_ARG_ENABLE(acl-support,
	AS_HELP_STRING([--disable-acl-support],[disable ACL support]))

if test x"$enable_acl_support" = x"no"; then
    AC_MSG_RESULT(no)
else
    case "$host_os" in
    *sysv5*)
	AC_MSG_RESULT(Using UnixWare ACLs)
	AC_DEFINE(HAVE_UNIXWARE_ACLS, 1, [true if you have UnixWare ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1, [Define to 1 to add support for ACLs])
	;;
    solaris*)
	AC_MSG_RESULT(Using solaris ACLs)
	AC_DEFINE(HAVE_SOLARIS_ACLS, 1, [true if you have solaris ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	;;
    *hpux*)
	AC_MSG_RESULT(Using HPUX ACLs)
	AC_DEFINE(HAVE_HPUX_ACLS, 1, [true if you have HPUX ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	;;
    *irix*)
	AC_MSG_RESULT(Using IRIX ACLs)
	AC_DEFINE(HAVE_IRIX_ACLS, 1, [true if you have IRIX ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	;;
    *aix*)
	AC_MSG_RESULT(Using AIX ACLs)
	AC_DEFINE(HAVE_AIX_ACLS, 1, [true if you have AIX ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	;;
    *osf*)
	AC_MSG_RESULT(Using Tru64 ACLs)
	AC_DEFINE(HAVE_TRU64_ACLS, 1, [true if you have Tru64 ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	LIBS="$LIBS -lpacl"
	;;
    darwin*)
	AC_MSG_RESULT(Using OS X ACLs)
	AC_DEFINE(HAVE_OSX_ACLS, 1, [true if you have Mac OS X ACLs])
	AC_DEFINE(SUPPORT_ACLS, 1)
	;;
    *)
	AC_MSG_RESULT(running tests:)
	AC_CHECK_LIB(acl,acl_get_file)
	AC_CACHE_CHECK([for ACL support],samba_cv_HAVE_POSIX_ACLS,[
	    AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
#include <sys/acl.h>]], [[ acl_t acl; int entry_id; acl_entry_t *entry_p; return acl_get_entry( acl, entry_id, entry_p);]])],[samba_cv_HAVE_POSIX_ACLS=yes],[samba_cv_HAVE_POSIX_ACLS=no])])
	AC_MSG_CHECKING(ACL test results)
	if test x"$samba_cv_HAVE_POSIX_ACLS" = x"yes"; then
	    AC_MSG_RESULT(Using posix ACLs)
	    AC_DEFINE(HAVE_POSIX_ACLS, 1, [true if you have posix ACLs])
	    AC_DEFINE(SUPPORT_ACLS, 1)
	    AC_CACHE_CHECK([for acl_get_perm_np],samba_cv_HAVE_ACL_GET_PERM_NP,[
		AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
#include <sys/acl.h>]], [[ acl_permset_t permset_d; acl_perm_t perm; return acl_get_perm_np( permset_d, perm);]])],[samba_cv_HAVE_ACL_GET_PERM_NP=yes],[samba_cv_HAVE_ACL_GET_PERM_NP=no])])
	    if test x"$samba_cv_HAVE_ACL_GET_PERM_NP" = x"yes"; then
		AC_DEFINE(HAVE_ACL_GET_PERM_NP, 1, [true if you have acl_get_perm_np])
	    fi
	else
	    if test x"$enable_acl_support" = x"yes"; then
		AC_MSG_ERROR(Failed to find ACL support)
	    else
		AC_MSG_RESULT(No ACL support found)
	    fi
	fi
	;;
    esac
fi

#################################################
# check for extended attribute support
AC_MSG_CHECKING(whether to support extended attributes)
AC_ARG_ENABLE(xattr-support,
    AS_HELP_STRING([--disable-xattr-support],[disable extended attributes]),
    [], [case "$ac_cv_func_getxattr$ac_cv_func_extattr_get_link$ac_cv_func_attropen" in
	*yes*) enable_xattr_support=maybe ;;
	*) enable_xattr_support=no ;;
	esac])
AH_TEMPLATE([SUPPORT_XATTRS],
[Define to 1 to add support for extended attributes])
if test x"$enable_xattr_support" = x"no"; then
    AC_MSG_RESULT(no)
else
    case "$host_os" in
    *linux*|*netbsd*)
	AC_MSG_RESULT(Using Linux xattrs)
	AC_DEFINE(HAVE_LINUX_XATTRS, 1, [True if you have Linux xattrs (or equivalent)])
	AC_DEFINE(SUPPORT_XATTRS, 1)
	AC_DEFINE(NO_SYMLINK_USER_XATTRS, 1, [True if symlinks do not support user xattrs])
	AC_CHECK_LIB(attr,getxattr)
	;;
    darwin*)
	AC_MSG_RESULT(Using OS X xattrs)
	AC_DEFINE(HAVE_OSX_XATTRS, 1, [True if you have Mac OS X xattrs])
	AC_DEFINE(SUPPORT_XATTRS, 1)
	AC_DEFINE(NO_DEVICE_XATTRS, 1, [True if device files do not support xattrs])
	AC_DEFINE(NO_SPECIAL_XATTRS, 1, [True if special files do not support xattrs])
	;;
    freebsd*)
	AC_MSG_RESULT(Using FreeBSD extattrs)
	AC_DEFINE(HAVE_FREEBSD_XATTRS, 1, [True if you have FreeBSD xattrs])
	AC_DEFINE(SUPPORT_XATTRS, 1)
	;;
    solaris*)
	AC_MSG_RESULT(Using Solaris xattrs)
	AC_DEFINE(HAVE_SOLARIS_XATTRS, 1, [True if you have Solaris xattrs])
	AC_DEFINE(SUPPORT_XATTRS, 1)
	AC_DEFINE(NO_SYMLINK_XATTRS, 1, [True if symlinks do not support xattrs])
	;;
    *)
	if test x"$enable_xattr_support" = x"yes"; then
	    AC_MSG_ERROR(Failed to find extended attribute support)
	else
	    AC_MSG_RESULT(No extended attribute support found)
	fi
	;;
    esac
fi

if test x"$enable_acl_support" = x"no" -o x"$enable_xattr_support" = x"no" -o x"$enable_iconv" = x"no"; then
    AC_MSG_CHECKING([whether $CC supports -Wno-unused-parameter])
    OLD_CFLAGS="$CFLAGS"
    CFLAGS="$CFLAGS -Wno-unused-parameter"
    AC_LINK_IFELSE([AC_LANG_PROGRAM([[#include <stdio.h>]], [[printf("hello\n");]])],[rsync_warn_flag=yes],[rsync_warn_flag=no])
    AC_MSG_RESULT([$rsync_warn_flag])
    if test x"$rsync_warn_flag" = x"no"; then
	CFLAGS="$OLD_CFLAGS"
    fi
fi

case "$CC" in
' checker'*|checker*)
    AC_DEFINE(FORCE_FD_ZERO_MEMSET, 1, [Used to make "checker" understand that FD_ZERO() clears memory.])
    ;;
esac

AC_CONFIG_FILES([Makefile lib/dummy zlib/dummy popt/dummy shconfig])
AC_OUTPUT

AC_MSG_RESULT()
AC_MSG_RESULT([    rsync ${RSYNC_VERSION} configuration successful])
AC_MSG_RESULT()
if test x$HAVE_YODL2MAN != x1; then
    AC_MSG_RESULT([    Note that yodl2man was not found, so pre-existing manpage files will be])
    AC_MSG_RESULT([    used w/o change (if available) -- no .yo file changes will be used.])
    AC_MSG_RESULT()
fi
