<!--
 - Copyright (C) Internet Systems Consortium, Inc. ("ISC")
 -
 - This Source Code Form is subject to the terms of the Mozilla Public
 - License, v. 2.0. If a copy of the MPL was not distributed with this
 - file, You can obtain one at http://mozilla.org/MPL/2.0/.
 -
 - See the COPYRIGHT file distributed with this work for additional
 - information regarding copyright ownership.
-->
# BIND 9

### Contents

1. [Introduction](#intro)
1. [Reporting bugs and getting help](#help)
1. [Contributing to BIND](#contrib)
1. [Building BIND](#build)
1. [macOS](#macos)
1. [Dependencies](#dependencies)
1. [Compile-time options](#opts)
1. [Automated testing](#testing)
1. [Documentation](#doc)
1. [Change log](#changes)
1. [Acknowledgments](#ack)

### <a name="intro"/> Introduction

BIND (Berkeley Internet Name Domain) is a complete, highly portable
implementation of the Domain Name System (DNS) protocol.

The BIND name server, `named`, can act as an authoritative name
server, recursive resolver, DNS forwarder, or all three simultaneously. It
implements views for split-horizon DNS, automatic DNSSEC zone signing and
key management, catalog zones to facilitate provisioning of zone data
throughout a name server constellation, response policy zones (RPZ) to
protect clients from malicious data, response rate limiting (RRL) and
recursive query limits to reduce distributed denial of service attacks,
and many other advanced DNS features. BIND also includes a suite of
administrative tools, including the `dig` and `delv` DNS lookup tools,
`nsupdate` for dynamic DNS zone updates, `rndc` for remote name server
administration, and more.

BIND 9 began as a complete rewrite of the BIND architecture that was
used in versions 4 and 8.  Internet Systems Consortium
([https://www.isc.org](https://www.isc.org)), a 501(c)(3) US public benefit
corporation dedicated to providing software and services in support of the
Internet infrastructure, developed BIND 9 and is responsible for its
ongoing maintenance and improvement. BIND is open source software
licensed under the terms of the Mozilla Public License, version 2.0.

For a detailed list of changes made throughout the history of BIND 9, see
the file [CHANGES](CHANGES). See [below](#changes) for details on the
CHANGES file format.

For up-to-date versions and release notes, see
[https://www.isc.org/download/](https://www.isc.org/download/).

For information about supported platforms, see [PLATFORMS](PLATFORMS.md).

### <a name="help"/> Reporting bugs and getting help

To report non-security-sensitive bugs or request new features, you may
open an issue in the BIND 9 project on the
[ISC GitLab server](https://gitlab.isc.org) at
[https://gitlab.isc.org/isc-projects/bind9](https://gitlab.isc.org/isc-projects/bind9).

Please note that, unless you explicitly mark the newly created issue as
"confidential," it will be publicly readable. Please do not include any
information in bug reports that you consider to be confidential unless
the issue has been marked as such. In particular, if submitting the
contents of your configuration file in a non-confidential issue, it is
advisable to obscure key secrets; this can be done automatically by
using `named-checkconf -px`.

If you are reporting a bug that is a potential security issue, such as an
assertion failure or other crash in `named`, please do *NOT* use GitLab to
report it. Instead, send mail to
[security-officer@isc.org](mailto:security-officer@isc.org) using our
OpenPGP key to secure your message. (Information about OpenPGP and links
to our key can be found at
[https://www.isc.org/pgpkey](https://www.isc.org/pgpkey).) Please do not
discuss the bug on any public mailing list.

For a general overview of ISC security policies, read the Knowledgebase
article at [https://kb.isc.org/docs/aa-00861](https://kb.isc.org/docs/aa-00861).

Professional support and training for BIND are available from
ISC. Contact us at [https://www.isc.org/contact](https://www.isc.org/contact)
for more information.

To join the __BIND Users__ mailing list, or view the archives, visit
[https://lists.isc.org/mailman/listinfo/bind-users](https://lists.isc.org/mailman/listinfo/bind-users).

If you're planning on making changes to the BIND 9 source code, you
may also want to join the __BIND Workers__ mailing list, at
[https://lists.isc.org/mailman/listinfo/bind-workers](https://lists.isc.org/mailman/listinfo/bind-workers).

### <a name="contrib"/> Contributing to BIND

ISC maintains a public git repository for BIND; details can be found
at [https://www.isc.org/sourceaccess/](https://www.isc.org/sourceaccess/).

Information for BIND contributors can be found in the following files:
- General information: [CONTRIBUTING.md](CONTRIBUTING.md)
- Code of Conduct: [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)
- BIND 9 code style: [doc/dev/style.md](doc/dev/style.md)
- BIND architecture and developer guide: [doc/dev/dev.md](doc/dev/dev.md)

Patches for BIND may be submitted as
[merge requests](https://gitlab.isc.org/isc-projects/bind9/merge_requests)
on the [ISC GitLab server](https://gitlab.isc.org).

By default, external contributors do not have the ability to fork BIND on the
GitLab server; if you wish to contribute code to BIND, you may request
permission to do so. Thereafter, you can create git branches and directly
submit requests that they be reviewed and merged.

If you prefer, you may also submit code by opening a
[GitLab issue](https://gitlab.isc.org/isc-projects/bind9/issues) and
including your patch as an attachment, preferably generated by
`git format-patch`.

### <a name="build"/> Building BIND 9

At a minimum, BIND requires a Unix or Linux system with an ANSI C compiler,
basic POSIX support, and a 64-bit integer type. BIND also requires the
`libuv` asynchronous I/O library, the `nghttp2` HTTP/2 library, the
`jemalloc` memory allocation library, and the OpenSSL cryptography
library.  On Linux, BIND requires the `libcap` library to set process
privileges, though this requirement can be overridden by disabling
capability support at compile time. See [Compile-time options](#opts)
below for details on other libraries that may be required to support
optional features.

Successful builds have been observed on many versions of Linux and Unix,
including RHEL/CentOS/Oracle Linux, Fedora, Debian, Ubuntu, SLES, openSUSE,
Slackware, Alpine, FreeBSD, NetBSD, OpenBSD, macOS, Solaris, OpenIndiana,
OmniOS CE, HP-UX, and OpenWRT.

To build on a Unix or Linux system, use:

		$ autoreconf -fi (if you are building in the git repository)
		$ ./configure
		$ make

If you're using Emacs, you might find `make tags` helpful.

Several environment variables, which can be set before running `configure`,
affect compilation.  Significant ones are:

|Variable|Description |
|--------------------|-----------------------------------------------|
|`CC`|The C compiler to use.  `configure` tries to figure out the right one for supported systems.|
|`CFLAGS`|C compiler flags.  Defaults to include -g and/or -O2 as supported by the compiler.  Please include '-g' if you need to set `CFLAGS`. |
|`LDFLAGS`|Linker flags. Defaults to empty string.|

Additional environment variables affecting the build are listed at the
end of the `configure` help text, which can be obtained by running the
command:

    $ ./configure --help

#### <a name="macos"> macOS

Building on macOS assumes that the "Command Tools for Xcode" are installed.
These can be downloaded from
[https://developer.apple.com/download/more/](https://developer.apple.com/download/more/)
or, if you have Xcode already installed, you can run `xcode-select --install`.
(Note that an Apple ID may be required to access the download page.)

#### <a name="dependencies"> Dependencies

To build BIND you need to have the following packages installed:

    libuv
    pkg-config / pkgconfig / pkgconf

To build BIND from the git repository, you need the following tools
installed:

    autoconf (includes autoreconf)
    automake
    libtool

#### <a name="opts"/> Compile-time options

To see a full list of configuration options, run `configure --help`.

For the server to support DNSSEC, you need to build it with crypto support.
To use OpenSSL, you must have OpenSSL 1.0.2e or newer installed. If the
OpenSSL library is installed in a nonstandard location, specify the prefix
using `--with-openssl=<PREFIX>` on the configure command line. To use a
PKCS#11 hardware service module for cryptographic operations, it will
be necessary to compile and use engine_pkcs11 from the OpenSC project.

To support DNS over HTTPS, the server must be linked with `libnghttp2`.

To support the HTTP statistics channel, the server must be linked with at
least one of the following libraries: `libxml2`
[http://xmlsoft.org](http://xmlsoft.org) or `json-c`
[https://github.com/json-c/json-c](https://github.com/json-c/json-c).
If these are installed at a nonstandard location, then:

* for `libxml2`, specify the prefix using `--with-libxml2=/prefix`.
* for `json-c`, adjust `PKG_CONFIG_PATH`.

To support compression on the HTTP statistics channel, the server must be
linked against `libzlib`. If this is installed in a nonstandard location,
specify the prefix using `--with-zlib=/prefix`.

To support storing configuration data for runtime-added zones in an LMDB
database, the server must be linked with `liblmdb`. If this is installed in a
nonstandard location, specify the prefix using `with-lmdb=/prefix`.

To support MaxMind GeoIP2 location-based ACLs, the server must be linked
with `libmaxminddb`. This is turned on by default if the library is
found; if the library is installed in a nonstandard location,
specify the prefix using `--with-maxminddb=/prefix`. GeoIP2 support
can be switched off with `--disable-geoip`.

For DNSTAP packet logging, you must have installed `libfstrm`
[https://github.com/farsightsec/fstrm](https://github.com/farsightsec/fstrm)
and `libprotobuf-c`
[https://developers.google.com/protocol-buffers](https://developers.google.com/protocol-buffers),
and BIND must be configured with `--enable-dnstap`.

Certain compiled-in constants and default settings can be decreased to
values better suited to small machines, e.g. OpenWRT boxes, by specifying
`--with-tuning=small` on the `configure` command line. This decreases
memory usage by using smaller structures, but degrades performance.

On Linux, process capabilities are managed in user space using
the `libcap` library, which can be installed on most Linux systems via
the `libcap-dev` or `libcap-devel` package. Process capability support can
also be disabled by configuring with `--disable-linux-caps`.

On some platforms it is necessary to explicitly request large file support
to handle files bigger than 2GB.  This can be done by using
`--enable-largefile` on the `configure` command line.

Support for the "fixed" rrset-order option can be enabled or disabled by
specifying `--enable-fixed-rrset` or `--disable-fixed-rrset` on the
configure command line. By default, fixed rrset-order is disabled to
reduce memory footprint.

The `--enable-querytrace` option causes `named` to log every step of
processing every query. The `--enable-singletrace` option turns on the
same verbose tracing, but allows an individual query to be separately
traced by setting its query ID to 0. These options should only be enabled
when debugging, because they have a significant negative impact on query
performance.

`make install` installs `named` and the various BIND 9 libraries. By
default, installation is into /usr/local, but this can be changed with the
`--prefix` option when running `configure`.

You may specify the option `--sysconfdir` to set the directory where
configuration files like `named.conf` go by default, and `--localstatedir`
to set the default parent directory of `run/named.pid`. `--sysconfdir`
defaults to `$prefix/etc` and `--localstatedir` defaults to `$prefix/var`.

### <a name="testing"/> Automated testing

A system test suite can be run with `make check`. The system tests require
you to configure a set of virtual IP addresses on your system (this allows
multiple servers to run locally and communicate with each other). These
IP addresses can be configured by running the command
`bin/tests/system/ifconfig.sh up` as root.

Some tests require Perl and the `Net::DNS` and/or `IO::Socket::INET6` modules,
and are skipped if these are not available. Some tests require Python
and the `dnspython` module and are skipped if these are not available.
See bin/tests/system/README for further details.

Unit tests are implemented using the CMocka unit testing framework. To build
them, use `configure --with-cmocka`. Execution of tests is done by the automake
parallel test driver; unit tests are also run by `make check`.

### <a name="doc"/> Documentation

The *BIND 9 Administrator Reference Manual* (ARM) is included with the source
distribution, and in .rst format, in the `doc/arm`
directory. HTML and PDF versions are automatically generated and can
be viewed at [https://bind9.readthedocs.io/en/latest/index.html](https://bind9.readthedocs.io/en/latest/index.html).

Man pages for some of the programs in the BIND 9 distribution
are also included in the BIND ARM.

Frequently (and not-so-frequently) asked questions and their answers
can be found in the ISC Knowledgebase at
[https://kb.isc.org](https://kb.isc.org).

Additional information on various subjects can be found in other
`README` files throughout the source tree.

### <a name="changes"/> Change log

A detailed list of all changes that have been made throughout the
development of BIND 9 is included in the file CHANGES, with the most recent
changes listed first. Change notes include tags indicating the category of
the change that was made; these categories are:

|Category	|Description	        			|
|--------------	|-----------------------------------------------|
| [func] | New feature |
| [bug] | General bug fix |
| [security] | Fix for a significant security flaw |
| [experimental] | Used for new features when the syntax or other aspects of the design are still in flux and may change |
| [port] | Portability enhancement |
| [maint] | Updates to built-in data such as root server addresses and keys |
| [tuning] | Changes to built-in configuration defaults and constants to improve performance |
| [performance] | Other changes to improve server performance |
| [protocol] | Updates to the DNS protocol such as new RR types |
| [test] | Changes to the automatic tests, not affecting server functionality |
| [cleanup] | Minor corrections and refactoring |
| [doc] | Documentation |
| [contrib] | Changes to the contributed tools and libraries in the 'contrib' subdirectory |
| [placeholder] | Used in the main development branch to reserve change numbers for use in other branches, e.g., when fixing a bug that only exists in older releases |

In general, [func] and [experimental] tags only appear in new-feature
releases (i.e., those with version numbers ending in zero). Some new
functionality may be backported to older releases on a case-by-case basis.
All other change types may be applied to all currently supported releases.

#### Bug report identifiers

Most notes in the CHANGES file include a reference to a bug report or
issue number. Prior to 2018, these were usually of the form `[RT #NNN]`
and referred to entries in the "bind9-bugs" RT database, which was not open
to the public. More recent entries use the form `[GL #NNN]` or, less often,
`[GL !NNN]`, which, respectively, refer to issues or merge requests in the
GitLab database. Most of these are publicly readable, unless they include
information which is confidential or security-sensitive.

To look up a GitLab issue by its number, use the URL
[https://gitlab.isc.org/isc-projects/bind9/issues/NNN](https://gitlab.isc.org/isc-projects/bind9/issues).
To look up a merge request, use
[https://gitlab.isc.org/isc-projects/bind9/merge_requests/NNN](https://gitlab.isc.org/isc-projects/bind9/merge_requests).

In rare cases, an issue or merge request number may be followed with the
letter "P". This indicates that the information is in the private ISC
GitLab instance, which is not visible to the public.

### <a name="ack"/> Acknowledgments

* The original development of BIND 9 was underwritten by the
  following organizations:

		Sun Microsystems, Inc.
		Hewlett Packard
		Compaq Computer Corporation
		IBM
		Process Software Corporation
		Silicon Graphics, Inc.
		Network Associates, Inc.
		U.S. Defense Information Systems Agency
		USENIX Association
		Stichting NLnet - NLnet Foundation
		Nominum, Inc.

* This product includes software developed by the OpenSSL Project for use
  in the OpenSSL Toolkit.
  [https://www.OpenSSL.org/](https://www.OpenSSL.org/)
* This product includes cryptographic software written by Eric Young
  (eay@cryptsoft.com).
* This product includes software written by Tim Hudson (tjh@cryptsoft.com).