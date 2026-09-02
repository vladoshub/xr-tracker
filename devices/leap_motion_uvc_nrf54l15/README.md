## Overview

The tracker uses a **Leap Motion Controller v1** as the optical tracking camera.

The Leap Motion must be flashed into **UVC mode** using:

[leapmotion/leapuvc](https://github.com/leapmotion/leapuvc)

For best tracking quality, adding a **XIAO nRF54L15 Sense** is strongly recommended. Its built-in IMU provides inertial data that can be combined with optical tracking.

Without the IMU, the tracker can operate in **visual-odometry-only (VO) mode**, but this mode is significantly less reliable with the Ultraleap camera.

## Hardware

### Required

- **Leap Motion Controller v1**
- USB connection for the Leap Motion
- 3D-printed tracker enclosure / mount from this repository

### Strongly recommended

- **Seeed Studio XIAO nRF54L15 Sense**
- USB cable for flashing/configuring the XIAO
- Appropriate wiring/mounting hardware

The XIAO nRF54L15 Sense contains the IMU used by the tracking stack.

## Software / Firmware

### Leap Motion Controller

The Leap Motion Controller v1 must be converted to UVC mode.

Use:

[https://github.com/leapmotion/leapuvc](https://github.com/leapmotion/leapuvc)

Follow the instructions in that repository to flash and verify the controller (You will need Windows once to set it up.).

After conversion, the Leap Motion should be exposed to the operating system as a standard UVC camera.

> **Important:** flashing firmware always carries some risk. Make sure you are using a supported Leap Motion Controller v1 and carefully follow the `leapuvc` instructions.

### XIAO nRF54L15 Sense


Using the IMU is highly recommended.

Without it, tracking falls back to VO-only operation.


## Flash a pre-built XIAO nRF54L15 Sense release (recommended for users)


You do **not** need to build the firmware yourself.

Pre-built firmware is published on the
[XR Controller Releases](https://github.com/vladoshub/xr-controller/releases) page.


Connect the **XIAO nRF54L15 Sense** to the PC using a USB-C data cable. The board
has an onboard SAMD11 CMSIS-DAP debugger, so no external programmer is required.

**Download last release and flash**

```bash
curl -fsSL https://raw.githubusercontent.com/vladoshub/xr-controller/main/scripts/flash_release.sh \
  -o /tmp/flash_release.sh && \
chmod +x /tmp/flash_release.sh && \
/tmp/flash_release.sh --latest
```

**Or clone repository and flash**
On Ubuntu 24.04, install the flashing dependencies:

```bash
sudo apt update
sudo apt install openocd curl
```

Clone this repository:

```bash
git clone https://github.com/vladoshub/xr-controller
cd xr-controller
```

Download `zephyr.hex` from the latest release and flash it automatically:

```bash
./scripts/flash_release.sh --latest
```

Alternatively, download `zephyr.hex` manually from the Releases page and flash it:

```bash
./scripts/flash_release.sh ~/Downloads/zephyr.hex
```

The script flashes and verifies the pre-built image directly through the XIAO's
onboard CMSIS-DAP debugger.

**Zephyr, west, the Zephyr SDK, and firmware compilation are not required for
flashing a release build.**

After flashing, reconnect or reset the board and check the serial interface:

```bash
ls -l /dev/serial/by-id/ 2>/dev/null || true
ls -l /dev/ttyACM*
```

Then verify the live IMU stream:

```bash
./scripts/verify.sh /dev/ttyACM0
```

Replace `/dev/ttyACM0` with the actual device path. On Linux, prefer the stable
`/dev/serial/by-id/...` symlink when available.


### Recommended print settings

The parts should generally work with ordinary FDM printing.

Suggested starting point:

- Layer height: **0.20 mm**
- Walls: **3 or more**
- Infill: **20–30%**
- Material: **PETG recommended**
- PLA can also be used for prototypes

Exact orientation and support requirements may differ between parts.

## Assembly

### 1. Print the enclosure

Print the required STL files from the `models/stl` directory.

If modifications are required for your headset or mounting system, use the FreeCAD sources from `models/freecad`.

### 2. Install the Leap Motion Controller

Place the **Leap Motion Controller v1** into the printed enclosure.

Make sure:

- the cameras are completely unobstructed;
- the controller is held firmly;
- the controller cannot move relative to the headset/tracked object;
- the USB connector is not under mechanical stress.

Rigid mounting is important because any movement of the camera relative to the IMU changes the tracker calibration.

### 3. Install the XIAO nRF54L15 Sense

If using IMU-assisted tracking, install the **XIAO nRF54L15 Sense** in its designated mounting position.

The board should also be mounted rigidly.

Avoid soft adhesive or mounting methods that allow the IMU PCB to move independently from the Leap Motion camera.

### 4. Connect the hardware

Connect:

- Leap Motion Controller to the host PC;
- XIAO nRF54L15 Sense to the host as required by the XR Controller firmware.

Verify that both devices are detected before proceeding with tracker configuration.

## Tracking Modes

### Optical + IMU

**Recommended configuration.**

Uses:

- Leap Motion UVC camera for optical tracking
- XIAO nRF54L15 Sense for inertial measurements

This configuration provides substantially better behaviour during fast movement and temporary degradation/loss of optical tracking.

### VO only

The tracker can also operate without the XIAO nRF54L15 Sense.

In this configuration only visual odometry from the camera is available.

```text
Leap Motion UVC
      │
      ▼
Visual Odometry
      │
      ▼
6DoF pose
```

This mode is useful for testing, but **VO-only tracking currently performs poorly with the Ultraleap camera compared with the IMU-assisted configuration**.

For practical use, the XIAO nRF54L15 Sense is strongly recommended.

## Using with XR Gate

The tracker has been tested with:

[XR Gate](https://github.com/vladoshub/xr-gate)

### VO only

```bash
cd ~/xr-gate-release/xr-gate
./run_xr_client.sh --config leap_motion_uvc
```

### VIO
```bash
cd ~/xr-gate-release/xr-gate
./run_xr_client.sh --config leap_motion_uvc_nrf54l15
```

XR Gate provides the runtime/integration side of the tracking setup.

A typical hardware stack looks like:

```text
               ┌──────────────────────────┐
               │ Leap Motion Controller   │
               │        UVC mode          │
               └────────────┬─────────────┘
                            │
                      Optical tracking
                            │
                            ▼
                    ┌───────────────┐
                    │               │
┌────────────────┐  │   XR Gate    │  ─────► XR runtime
│ XIAO nRF54L15  │──►               │
│ Sense + IMU    │  │               │
└────────────────┘  └───────────────┘
       IMU
```

Refer to the XR Gate documentation for current runtime configuration and launch instructions.

## Preparation Checklist

Before using the tracker, verify that:

- [ ] Leap Motion Controller v1 is flashed to UVC mode
- [ ] Leap Motion appears as a UVC camera
- [ ] Tracker enclosure is printed and assembled
- [ ] Leap Motion is rigidly mounted
- [ ] XIAO nRF54L15 Sense is installed
- [ ] XR Controller firmware is flashed to the XIAO
- [ ] XIAO is detected by the host
- [ ] Camera and IMU cannot move relative to each other
- [ ] XR Gate is installed and configured

The tracker can technically be tested without the XIAO, but IMU-assisted operation is recommended.

## Related Projects

### LeapUVC

UVC firmware/tooling for the Leap Motion Controller:

[https://github.com/leapmotion/leapuvc](https://github.com/leapmotion/leapuvc)