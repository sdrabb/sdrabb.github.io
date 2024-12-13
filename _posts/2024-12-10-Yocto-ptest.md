---
layout: post
---
Including python test into Yocto aka `ptest` let you to add away to test the image you create using python test.

## Execute test from Yocto host env 
Test are located under
`<you-layer>/lib/oeqa/runtime/cases/`
and there are predefined test under:
`poky/meta/lib/oeqa/runtime/cases/*`

```
enable_ptest_and_testimage: |
  IMAGE_CLASSES += " testimage"
  DISTRO_FEAURES:append = " ptest"

  # tests you want to run
  TEST_SUITES = " ping ssh python2 ptest"
  
  IMAGE_INSTALL:append = " ptest-runner"
  TEST_TARGET = "simpleremote"
  TEST_TARGET_IP = "192.168.1.100"
  TEST_SERVER_IP = "192.168.1.103"
```
run them via the following command:
`bitbake -c testimage <image>`

## Include ptest package inside your recipes
Another way to play with ptest is to include them inside the `IMAGE_ROOTFS`.

We can pick as reference the `sshfs-fuse` recipe where inside sources is defined [test_sshfs.py][test_sshfs]
that will be called by `run-ptest` hosted_files

```
SUMMARY = "This is a filesystem client based on the SSH File Transfer Protocol using FUSE"
AUTHOR = "Miklos Szeredi <miklos@szeredi.hu>"
HOMEPAGE = "https://github.com/libfuse/sshfs"
SECTION = "console/network"
LICENSE = "GPL-2.0-only"
DEPENDS = "glib-2.0 fuse3"
LIC_FILES_CHKSUM = "file://COPYING;md5=b234ee4d69f5fce4486a80fdaf4a4263"

SRC_URI = "git://github.com/libfuse/sshfs;branch=master;protocol=https"
SRCREV = "9700b353700589dcc462205c638bcb40219a49c2"
S = "${WORKDIR}/git"

inherit meson pkgconfig ptest

SRC_URI += " \
	file://run-ptest \
"

RDEPENDS:${PN}-ptest += " \
        ${PYTHON_PN}-pytest \
        bash \
"

do_install_ptest() {
        install -d ${D}${PTEST_PATH}/test
        cp -rf ${S}/test/* ${D}${PTEST_PATH}/test/
}

```

## References:
- [2024 OSSE YoctoTesting][yocto-testing]


[yocto-testing]: https://static.sched.com/hosted_files/osseu2024/89/2024_OSSE_YoctoTesting.pdf?_gl=1*qfx7l6*_gcl_au*OTUxMzYwNTgxLjE3MjkwODE4MzU.*FPAU*OTUxMzYwNTgxLjE3MjkwODE4MzU.
[test_sshfs]:https://github.com/libfuse/sshfs/blob/master/test/test_sshfs.py


