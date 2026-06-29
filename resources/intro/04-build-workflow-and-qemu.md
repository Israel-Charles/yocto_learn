# 4. Build Workflow and Running in QEMU

This is the core hands-on section: from nothing to a booting Linux image inside
an emulator.

## 4.1 The end-to-end workflow

There are four conceptual steps: get the source, pick a release, set up the
environment, then build.

### Step 1: Download the Poky source code

```
git clone https://git.yoctoproject.org/poky
```

### Step 2: Check out a release branch

A release is just a Git branch. The original notes checked out `zeus` (2019),
which is end of life. Use the current LTS, **scarthgap**.

```
cd poky
git branch -a                 # see what branches exist
git checkout -t origin/scarthgap -b my-scarthgap
```

That last command creates a local branch `my-scarthgap` that tracks the upstream
`scarthgap` branch, which is the recommended way to work on a release. (A plain
`git checkout scarthgap` also works.)

> Why scarthgap? It is the Long Term Support release: published April 2024,
> supported with security and bug fixes until April 2028. LTS releases are the
> right default for anything you intend to maintain. If you specifically need
> the newest features and do not need long support, you can check out a newer
> non-LTS branch instead, but for learning and for products, a LTS version like scarthgap is the
> safe choice. The current release list is always at
> https://docs.yoctoproject.org/migration-guides/index.html

### Step 3: Prepare the build environment

Poky ships a script, `oe-init-build-env`, that you **source** (not execute) to
set up your shell. It configures the environment to use the Yocto build system,
including adding the `bitbake` utility to your `PATH`.

```
source oe-init-build-env [build_directory]
```

- `build_directory` is optional. If you omit it, it defaults to `build`.
- Sourcing the script moves you into the build directory and, on first run,
  creates a `conf/` subdirectory containing two files you will edit often:
  - `local.conf` - your local build configuration (machine, parallelism,
    package format, extra packages, and so on)
  - `bblayers.conf` - the list of layers BitBake will use

You must source this script in **every new terminal** before running BitBake;
it sets environment variables that do not persist between shells.

### Step 4: Build a Linux image

```
bitbake core-image-minimal
```

To also see how long it takes:

```
time bitbake core-image-minimal
```

The first build is slow because everything, including the cross toolchain, is
compiled from source and downloaded; expect anywhere from one to a
few hours depending on the machine. Later builds reuse cached artifacts (the
shared state cache) and are far faster.

#### What is core-image-minimal?

`core-image-minimal` is a small image that is just enough to boot a device. It
is very useful for kernel and bootloader testing and development, and it is one of the
best first image to build because it is the quickest to complete.

### Where the output lands

After a successful build, the images appear under:

```
tmp/deploy/images/<machine>/
```

For example, with the default machine you will find kernel images and root
filesystem images for `qemux86-64` there.

## 4.2 Running the generated image in QEMU

**QEMU (Quick Emulator)** is a free and open source package that performs
hardware virtualization. QEMU-based machines let you test and develop without
real hardware. Emulation is supported for, among others:

- ARM
- MIPS
- MIPS64
- PowerPC (PPC)
- x86
- x86-64

Poky provides a script called `runqemu` that boots a Yocto-generated image in
QEMU. The general form is:

```
runqemu <machine> <zimage> <filesystem>
```

where:

- `<machine>` is the machine/architecture to emulate
  (`qemuarm`, `qemumips`, `qemuppc`, `qemux86`, `qemux86-64`, and so on)
- `<zimage>` is the path to a kernel (for example `zImage-qemuarm.bin`)
- `<filesystem>` is the path to a root filesystem image (for example an `.ext4`
  image) or an NFS directory

In practice you rarely type all three arguments. Because `runqemu` knows where
the build deposited the images, it is usually enough to name the machine and the
image:

```
runqemu qemux86-64 core-image-minimal
```

Run `runqemu` with no arguments to see full usage.

### Exiting QEMU

Exit QEMU by clicking the shutdown icon in the QEMU graphical window, or by
typing `Ctrl-C` in the terminal from which you launched QEMU. (In nographic mode,
see below, you log in at the serial console and can also power down from inside
the guest.)

## 4.3 Building and running an ARM image

By default `local.conf` is set to build for the `qemux86-64` target. To build
for emulated ARM instead, change one line.

1. Edit `conf/local.conf` (inside your build directory) and set the machine:

```
MACHINE = "qemuarm"
```

2. Make sure your environment is set up, build, then run:

```
source oe-init-build-env
bitbake core-image-minimal
runqemu core-image-minimal
```

Because the machine is now `qemuarm`, `runqemu` boots the ARM image. That single
`MACHINE` change is the whole point of the "architecture agnostic" claim from
Lesson 1: the same workflow, one setting different, produces a completely
different target.

## 4.4 Headless mode: nographic

You can launch QEMU without opening a graphical window by adding `nographic`.
This is useful on servers, over SSH, or when you just want a serial console.

```
runqemu qemuarm nographic
```

In nographic mode all guest output appears in your terminal and you log in at the
emulated serial console. To leave QEMU in nographic mode, the usual escape is
`Ctrl-A` then `x`.

---

Next: `05-customizing-images.md` shows how to choose a richer image and how to
add your own packages.
