# XR Tracker

A DIY 6DoF tracking modules for [XR Gate](https://github.com/vladoshub/xr-gate)

The tracker is intended primarily for use with [XR Gate](https://github.com/vladoshub/xr-gate) and works only on Linux

This repository contains:

- 3D-printable enclosure and mounting parts
- STL files ready for printing
- FreeCAD source files
- Assembly instructions
- Tracker preparation instructions


## 3D Printing and instructions

This repository includes both:

- `.stl` files for direct printing
- `.FCStd` / FreeCAD source files for modification
- `README.md` instructions

The directory structure is intended to look approximately like this:

```text
xr-tracker/
├── README.md
├── devices/
│   │   └── leap_motion_uvc_nrf54l15
│   │   │   └── models/
│   │   │   │   └── freecad/
│   │   │   │   └── stl/
│   │   │   └── README.md
│   │   └── ...
└── images/
    └── ...
```


## Related Projects

### XR Gate

XR runtime and tracking integration:

[https://github.com/vladoshub/xr-gate](https://github.com/vladoshub/xr-gate)

### XR Controller

Firmware for the XIAO nRF54L15 Sense:

[https://github.com/vladoshub/xr-controller](https://github.com/vladoshub/xr-controller)

Releases:

[https://github.com/vladoshub/xr-controller/releases](https://github.com/vladoshub/xr-controller/releases)



## Status

This project is currently experimental.

The hardware configuration described here has been tested with **XR Gate**, but the design, firmware and tracking algorithms may continue to change.

Contributions, alternative mounts and hardware improvements are welcome.
