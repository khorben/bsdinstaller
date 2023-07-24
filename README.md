bsdinstall
==========

This is a fork of bsdinstall, the official FreeBSD installer. The objective is
to keep the architecture and workflow, but to make the code more flexible and
more easily allow fixing issues and continuous testing.

References
----------

The upstream code can be obtained at
<https://cgit.freebsd.org/src/tree/usr.sbin/bsdinstall>.

More information about the installer can be found at
<https://wiki.freebsd.org/BSDInstall>.

Known issues in bsdinstall can be browsed at
<https://bugs.freebsd.org/bugzilla/buglist.cgi?bug_status=__open__&email1=sysinstall%40FreeBSD.org&emailassigned_to1=1&emailcc1=1&emailtype1=equals&list_id=632069&query_format=advanced>.

Examples
--------

To build distfetch:

```shell-session
$ sudo pkg install bsddialog
[...]
$ (cd distfetch &&
	CPATH=/usr/local/include make LDFLAGS="-L/usr/local/lib -lbsddialog -lfetch")
```

To run individual steps in a sequence:

```shell-session
$ cat > test.sh << EOF
#!/bin/sh

BSDINSTALLDIR="\$PWD"
DESTDIR="\$BSDINSTALLDIR/destdir"
BSDINSTALL_DISTDIR="\$BSDINSTALL_DISTDIR/freebsd-dist"
SRCDIR="/usr/src"

TMPDIR="$(mktemp -d)"; export TMPDIR
for target in "\$@"; do
	BSDCFG_SHARE="\$SRCDIR/usr.sbin/bsdconfig/share" \
		BSDINSTALL_CHROOT="\$DESTDIR" \
		BSDINSTALL_SCRIPTS="\$BSDINSTALLDIR/scripts" \
		LOCAL_DISTRIBUTIONS="base.txz kernel.txz lib32.txz" \
		DISTRIBUTIONS="lib32-dbg.txz" \
		./bsdinstall "\$target"
done
$ sh test.sh fetchmissingdists checksum distextract
```

