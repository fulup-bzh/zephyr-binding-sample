
# Zephyr Binding Sample

## Overview

Same as Jose initial zephyr-binding-test, but organized to be compiled as an external west project.

## Install Zephyr into a podman toolbx

### create zephyr dev container using toolbx/podman
```
   toolbox create --image quay.io/toolbx-images/almalinux-toolbox:9 zephyr
   toolbox enter zephyr
```

### Install dependencies
```
  sudo dnf group install "Development Tools" "C Development Tools and Libraries"
  sudo dnf install cmake ninja-build gperf dfu-util dtc wget which python3.12-pip python3-tkinter xz file python3.12 python3.12-devel SDL2-devel
  sudo dnf install usbutils tio # not part of minimal podman/docker images
```

### Force Pythib 3.12 as default
```
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.12 2
sudo update-alternatives --config python
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.12 2
sudo update-alternatives --config python3
```

### create pyhton venv
```
  sudo chown $USERNAME /opt
  python3 -m venv /opt/zephyr-venv
  source /opt/zephyr-venv/bin/activate
  pip install --upgrade pip
  pip install west pyelftools
```

### Install Zephyr/SDK
  Ref: https://docs.zephyrproject.org/latest/develop/toolchains/zephyr_sdk.html
  https://github.com/zephyrproject-rtos/sdk-ng/releases
```
  cd /opt
  wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.17.0/zephyr-sdk-0.17.0_linux-x86_64.tar.xz
  tar xvf zephyr-sdk-0.17.0_linux-x86_64.tar.xz
  rm zephyr-sdk-0.17.0_linux-x86_64.tar.xz
```

## Building and Running

Create west project workspace
```
unset ZEPHYR_BASE ;# incompatible with zephyr T2 workspace model
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

## Testing (from Linux)

### Install libafb host tools (if needed)
You need to have afb-binder+devtools within you dev environement (Linux|container). If needed install afb host tools
```
wget -O - https://raw.githubusercontent.com/redpesk-devtools/redpesk-sdk-tools/master/install-redpesk-sdk.sh | bash

```

### Connect onto serial console
Warning: you need your user to have group:dialout access to connect /dev/ttyxxx
```
tio /dev/ttyACM0
```

### Import Zephyr API as a Linux shadow API
```
TARGET_IP=192.168.1.57
afb-binder --verbose --alias=/devtools:/usr/share/afb-ui-devtools/binder --monitoring -p 1234 --rpc-client tcp:$TARGET_IP:1234/tuto0 tcp:$TARGET_IP:1234/tuto1
```

### Connect to your Zephyr API through Linux

If your container/firewall allows. Use your prefered browser: http://localhost:1234:/devtools

Using afb-client from a terminal
```
> afb-client localhost:1234/api tuto0 hello duc
ON-REPLY 1:tuto1/hello: OK
{"jtype":"afb-reply","request":{"status":"success","code":0},"response":"Hello duc!"}

> afb-client localhost:1234/api tuto1 hello duc
ON-REPLY 1:tuto1/hello: OK
{"jtype":"afb-reply","request":{"status":"success","code":0},"response":"Hello duc!"}
```

ToBeDone

* dans les modules libafb/zephyr les paths des includes devraient être automatique et non pas dans l'application
* exposer les 2 api en mode debug et mettre 2 verbes différents
