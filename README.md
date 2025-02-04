
# Zephyr Binding Sample

## Overview

Same as Jose initial zephyr-binding-test, but organized to be compiled as an external west project.

## Building and Running

Create west project workspace
```
unset ZEPHYR_BASE
mkdir binding-workspace
cd binding-workspace
west init -m git@github.com:fulup-bzh/zephyr-binding-sample.git
west update --narrow
pip3 install -r zephyr/scripts/requirements-base.txt
```

Add your target board (npx used as sample)
```
# add you target board into west.yml
vi binding-sample/west.yml
  import:
    name-allowlist:
      - xxxx
      - hal_nxp

# update your workspace
west update --narrow
```

You should end-up with something like
```
[fulup@toolbx/zephyr:Zephyr]$ ls -l binding-workspace/
total 12
drwxr-sr-x.  4 fulup fulup 4096 Feb  4 13:33 binding-sample
drwxr-sr-x.  4 fulup fulup 4096 Feb  4 13:40 modules
drwxr-sr-x. 23 fulup fulup 4096 Feb  4 13:40 zephyr
```

Build & flash  binding-sample application
```
# Warning: your target board may need to be 
west build -b frdm_k64f binding-sample
west flash
```

ToBeDone

* dans les modules libafb/zephyr les paths des includes devraient être automatique et non pas dans l'application
