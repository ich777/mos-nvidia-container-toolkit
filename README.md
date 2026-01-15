# MOS nvidia-container-toolkit

nvidia-container-toolkit provides a **packaged Nvidia Container Toolkit**
for use within the MOS ecosystem.

This repository contains the **build scripts, packaging logic, and automation**
required to compile and package the NVIDIA Container Toolkit for MOS.

---

## Overview

The NVIDIA Container Toolkit is built from **upstream sources** and packaged
for integration into MOS.

It enables Docker and other container runtimes to run GPU‑accelerated
containers on systems with supported NVIDIA GPUs.

No functional changes to the upstream toolkit are intended.  
The goal of this repository is to provide **consistent and reproducible
Nvidia Container Toolkit packages** for MOS.

---

## Licensing

The contents of this repository (build scripts, configuration files, and automation)
are licensed under **GPL-3.0**.

The Nvidia Container Toolkit itself remains licensed under its respective
upstream license.

---

## Third-Party Software

This repository builds and packages third-party open-source software.
Packaged components remain licensed under their original upstream licenses.

Refer to `THIRD_PARTY.md` for details.
