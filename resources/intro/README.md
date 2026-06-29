# Introduction to the Yocto Project

This module is a self-contained introduction to the Yocto Project: what it is,
why it exists, the pieces it is built from, and how to use it to build and run a
small custom Linux image. It is written so you can read it top to bottom or jump
to a specific topic.

## Who this is for

Anyone who needs to build a custom Linux image for an embedded or constrained
device and has never used the Yocto Project before. Basic comfort with a Linux
command line and Git is assumed. No prior embedded experience is required.

## How this module is organized

Read the files in order. Each builds on the one before it.

1. `01-embedded-linux-and-yocto-basics.md`
   - The four (plus one) building blocks of an embedded Linux system
   - What "yocto" the word means, what the Yocto Project is, and why people use it
   - The "build system as a machine" mental model (inputs and outputs)

2. `02-yocto-architecture.md`
   - Poky, the reference distribution
   - Metadata, recipes, classes, configuration
   - OpenEmbedded and oe-core
   - BitBake, the build engine
   - Board Support Packages (BSPs) and how the pieces fit together

3. `03-build-host-setup.md`
   - Hardware and disk requirements
   - Host packages to install (Scarthgap)

4. `04-build-workflow-and-qemu.md`
   - The end-to-end workflow: clone, branch, init, build
   - Running an image in QEMU
   - Building and running an ARM image
   - Headless (nographic) mode

5. `05-customizing-images.md`
   - Standard reference images (core-image-minimal, core-image-sato)
   - Adding extra packages to your root filesystem

6. `06-exercises-and-challenge.md`
   - The "build for QEMU MIPS" challenge with a worked solution
   - A few extra practice exercises

## A note on versions before you start


These learning resources were developed and tested using the **Scarthgap (5.0)** 
release of Poky. Scarthgap is a Long Term Support (LTS) release and was released 
in **April 2024** and will receive support until **April 2028**.

The examples, commands, and package versions throughout this module assume the 
**Scarthgap (5.0)** release.

If you are using a different Yocto Project release, note that release codenames 
(for example, *zeus*, *dunfell*, *kirkstone*, *scarthgap*, etc.) correspond directly 
to Git branch names. When following the instructions in this course, 
replace `scarthgap` with the branch name of the release you are using.

For example:

```bash
git checkout scarthgap
```

## Learning objectives

By the end of this module you should be able to:

- Explain what the Yocto Project is and how it differs from Poky and OpenEmbedded
- Describe the role of BitBake, metadata, layers, and BSPs
- Set up a build host with the correct packages
- Clone Poky, check out a release branch, and initialize a build environment
- Build `core-image-minimal` and boot it in QEMU
- Switch the target architecture (for example to ARM or MIPS)
- Add an extra package to the root filesystem
