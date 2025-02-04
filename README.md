
# Zephyr Binding Test

## Overview

A simple binding that prove both internal and over-tcp communication between AFB api's
prints "Hello World" to the console.

## Building and Running

### Zephyr Side

In order to build this application you need AFB libraries in your Zephyr environment:

- afb-binding
- afb-libafb
- rp-lib-utils

These libraries can be obtained by using [our internal fork of zephyr](http://git.ovh.iot/zephyr/zephyr) on branch `@aaillet/binder`.


This application can be built and flashed on ST B_U585I_IOT02A Discovery kit w/ PHYTEC link board ETH shield as follows.

Please connect the ST-LINK/V3 embedded debug tool interface of the board to your computer using the CN8 micro-USB connector.

```bash
west build -b b_u585i_iot02a --shield link_board_eth -p always
west flash -r openocd
```

If you encounter issue flashing the board please check your USB cable and try another one.

### Linux side

To be able to communicate with a Zephyr firmware running AFB binder, you need the Binder on your host, please follow [this doc](https://docs.redpesk.bzh/docs/en/master/redpesk-os/afb-binder/afb-getting.html)

## Sample Output

### Zephyr side

Connect the board UART1 to your PC using CN8 micro-USB connector and access the virtual port using a serial terminal (minicom, putty, etc.) with default settings (115200 8N1).

```bash
*** Booting Zephyr OS build v3.7.0-8-g000d245c565b ***
[00:00:00.004,000] <inf> net_dhcpv4_client_sample: Run dhcpv4 client
[00:00:00.004,000] <inf> net_dhcpv4_client_sample: Start on enc424j600@0: index=1
[00:00:01.545,000] <inf> ethdrv: Link up
[00:00:01.545,000] <inf> ethdrv: 100Mbps
[00:00:01.545,000] <inf> ethdrv: full duplex
[00:00:05.551,000] <inf> net_dhcpv4_client_sample: DHCP Option 42: 192.168.1.1
[00:00:05.558,000] <inf> net_dhcpv4_client_sample: DHCP Option 42: 192.168.1.1
[00:00:05.558,000] <inf> net_dhcpv4: Received: 192.168.1.51
[00:00:05.558,000] <inf> net_dhcpv4_client_sample:    Address[1]: 192.168.1.51
[00:00:05.558,000] <inf> net_dhcpv4_client_sample:     Subnet[1]: 255.255.255.0
[00:00:05.558,000] <inf> net_dhcpv4_client_sample:     Router[1]: 192.168.1.1
[00:00:05.558,000] <inf> net_dhcpv4_client_sample: Lease time[1]: 86400 seconds
DEBUG: [REQ/API tuto0] Hi debugger!
NOTICE: [REQ/API tuto0] Hi observer! I'm tuto0 and I received hello from duc
DEBUG: [REQ/API tuto0] Hi debugger!
NOTICE: [REQ/API tuto0] Hi observer! I'm tuto0 and I received hello from duc
```

### Linux

```bash
# Terminal N°1
afb-binder -p 7777 --rpc-client tcp:192.168.1.51:1234/tuto1
# Terminal N°2
> afb-client localhost:7777/api tuto1 hello duc
ON-REPLY 1:tuto1/hello: OK
{"jtype":"afb-reply","request":{"status":"success","code":0},"response":"Hello duc!"}

> afb-client localhost:7777/api tuto1 hello duc
ON-REPLY 1:tuto1/hello: OK
{"jtype":"afb-reply","request":{"status":"success","code":0},"response":"Hello duc!"}
```