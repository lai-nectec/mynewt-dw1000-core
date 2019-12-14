<!--
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
#  KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#
-->

# Decawave DW1002-DEV 

## Overview

This distribution contains the example applications for the dwm1002-DEV. The dw1000 device driver model is integrated into the mynewt-OS (https://github.com/decawave/mynewt-dw1000-core). This driver includes native support for a 6lowPAN stack, Ranging Services, and Location Services, etc. Mynewt and it's tools build environment newt and management tools newtmgt create a powerful environment and deploying large-scale distributions within IoT.

For these examples, we leverage the Decawave dwm1002 module and dwm1002-dev kit. The dwm1002 includes the nrf52832, LSM6DSL(6DoF), LIS2MDL(MAG), LPS22HB(Barometer), and Dual dw1000 transceivers configured for AoA. The dwm1002-dev is a breakout board that supports a Seggar OB-JLink interface with RTT support. The mynewt build environment provides a clean interface for maintaining platform agnostics distributions. The dwm1003-dev and the examples contained herein provide a clean out-of-the-box experience for UWB Location Based Services.

1. To erase the default flash image that shipped with the DWM1001.

```no-highlight
$ JLinkExe -device nRF52 -speed 4000 -if SWD
J-Link>erase
J-Link>exit
$ 
```

2. Build the new bootloader applicaiton for the DWM1001 target.

(executed from within the mynewt-dw1000-app directory).

```no-highlight

newt target create dwm1002_boot
newt target set dwm1002_boot app=@apache-mynewt-core/apps/boot
newt target set dwm1002_boot bsp=@mynewt-dw1000-core/hw/bsp/dwm1002
newt target set dwm1002_boot build_profile=optimized 
newt build dwm1002_boot
newt create-image dwm1002_boot 1.0.0
newt load dwm1002_boot

```

3. On the first dwm1002-dev board build the Two-Way-Ranging (twr_tag) applicaitons for the DWM1002 module. The run command compiled the project and loads the image on the target platform.

(executed from within the mynewt-dw1000-app directory).

```no-highlight

newt target create twr_tag
newt target set twr_tag app=apps/twr_tag
newt target set twr_tag bsp=@mynewt-dw1000-core/hw/bsp/dwm1002
newt target set twr_tag build_profile=debug 
newt run twr_tag 0

```

4. On a second dwm1002-dev build the master side of the Two-Way-Ranging (twr_node) applicaitons as follows. 

(executed from within the mynewt-dw1000-app directory).

```no-highlight

newt target create twr_node 
newt target set twr_node app=apps/twr_node
newt target set twr_node bsp=@mynewt-dw1000-core/hw/bsp/dwm1002
newt target set twr_node build_profile=debug 
newt run twr_node 0

```
To switch from Single-Side to Double-Size, simply comment ./twr_node/main.c as follows: 
```no-highlight

   dw1000_rng_request(inst, 0x4321, DWT_SS_TWR);
   //dw1000_rng_request(inst, 0x4321, DWT_DS_TWR);

```


5. This following example illistratied the intergration of a 10DoF/IMU sensor into the framework. The IMU data is output onto the console every 5s in the exmaple.

```
newt target create twr_tag_imu
newt target set twr_tag_imu app=apps/twr_tag_imu
newt target set twr_tag_imu bsp=@mynewt-dw1000-core/hw/bsp/dwm1002
newt target set twr_tag_imu build_profile=debug
newt run twr_tag_imu 0
```

6. The above examples are configured to use the Segger RTT console interface. This is covered within the mynewt tutorials/Tooling/SeggarRTT (https://mynewt.apache.org/latest/os/tutorials/segger_rtt/). To launch the console simply telnet localhost 19021. Note at time of writing the newt tools does not support multiple connect dwm1001-dev devices. So it is recomended that you connect twr_tag and twr_node examples to different computers or at least the twr_tag to an external battery. If all going well you should see the twr_node example stream range information on the console. 

(executed from within the mynewt-dw1000-app directory).

```no-highlight

telnet localhost 19021

```
