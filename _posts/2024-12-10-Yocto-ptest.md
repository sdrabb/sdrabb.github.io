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

## References:
- [2024 OSSE YoctoTesting][yocto-testing]


[yocto-testing]: https://static.sched.com/hosted_files/osseu2024/89/2024_OSSE_YoctoTesting.pdf?_gl=1*qfx7l6*_gcl_au*OTUxMzYwNTgxLjE3MjkwODE4MzU.*FPAU*OTUxMzYwNTgxLjE3MjkwODE4MzU.



