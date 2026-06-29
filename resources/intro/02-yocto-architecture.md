# 2. Yocto Project Architecture

The Yocto Project is an umbrella over several cooperating components. This
section walks through each one and then shows how they nest together.

## 2.1 Poky: the reference distribution

**Poky is the reference distribution of the Yocto Project.** Here "reference"
means "example": Poky is a working example of how to build your own custom Linux
distribution from source. The Yocto Project uses Poky to build images (kernel,
system software, and applications) for targeted hardware.

At the technical level, Poky is a combined repository that bundles together:

- **BitBake** - the build engine (the task executor)
- **OpenEmbedded Core (oe-core)** - the base layer of shared metadata
- **meta-yocto-bsp** - reference Board Support Packages
- **meta-poky** - Poky-specific configuration and policy
- **Documentation** - the source files for the Yocto manuals

Important: **Poky does not contain prebuilt binaries.** It is source plus build
instructions. When you build, everything (including the toolchain) is produced
from source on your machine.

### Poky vs the Yocto Project: what is the difference?

A clean way to remember it, by analogy with Ubuntu:

- **Yocto Project** is the organization / umbrella, the way **Canonical** is the
  company behind Ubuntu.
- **Poky** is the actual thing you download and build, the way **Ubuntu** is the
  distribution Canonical produces.

So "the Yocto Project" is who; "Poky" is what you `git clone`.

## 2.2 Metadata

Outside of Yocto, "metadata" just means data that describes other data.

**In the Yocto world, metadata means the build instructions.** It is the
commands and data that say:

- which versions of which software to use
- where to obtain that software from
- what changes or additions to apply (patches) to fix bugs or to customize the
  software for a particular situation

Metadata is a collection of several file types:

- **Configuration files** (`.conf`) - global and machine/distro settings
- **Recipes** (`.bb` and `.bbappend`) - how to fetch, configure, compile, and
  package one piece of software. A `.bbappend` adds to or overrides an existing
  recipe without editing it directly.
- **Classes** (`.bbclass`) - reusable logic shared across many recipes (for
  example, "this is an autotools project" or "this is a CMake project")
- **Include files** (`.inc`) - shared fragments pulled into recipes or configs

This metadata, organized into layers, is the real "source code" of your
distribution.

## 2.3 OpenEmbedded and oe-core

The **OpenEmbedded Project** provides a best-in-class cross-compile environment
that lets developers create a complete Linux distribution for embedded systems.

So how does it relate to the Yocto Project? They share a common core, but remain
separate organizations with different focuses:

- **OpenEmbedded** provides a comprehensive, broad set of metadata covering a
  very wide variety of architectures, features, and applications. It is *not* a
  reference distribution; it is designed to be the foundation that others build
  on.
- **The Yocto Project** focuses on providing powerful, easy-to-use,
  interoperable, well-tested tools, metadata, and BSPs for a core set of
  architectures and specific boards.

The bridge between them is **OpenEmbedded-Core (`oe-core`)**: the two organizations
agreed to maintain a common, shared core set of metadata (recipes, classes, and
associated files). `oe-core` is the base layer that both projects depend on, which
is why a recipe written for one generally works in the other.

## 2.4 BitBake: the build engine

**BitBake is a core component of the Yocto Project.** It performs roughly the
same role that `make` does in a traditional C project, but at a much larger
scale.

What it actually is:

- A **task scheduler** that parses code written as a mix of Python and shell.
- The parsed code generates and runs **tasks**: ordered sets of steps. The order
  is determined automatically from the dependencies expressed in the metadata.

What it does on a typical build:

- Reads recipes and follows them: fetching source packages, building them, and
  incorporating the results into bootable images.
- Tracks every task so it can guarantee completion, maximize use of available CPU
  cores to reduce build time, and stay predictable and reproducible.

In short: you write recipes; BitBake figures out the dependency order and
executes the whole graph efficiently.

## 2.5 meta-yocto-bsp and Board Support Packages

A **Board Support Package (BSP)** is a collection of information that defines how
to support a particular hardware device, set of devices, or hardware platform.

A BSP typically includes:

- a description of the hardware features present on the device
- kernel configuration for that hardware, plus any additional drivers required
- a list of any additional software components needed beyond a generic Linux
  stack, for both essential and optional platform features

In Poky, the **meta-yocto-bsp** layer maintains a small set of reference BSPs.
These are deliberately minimal examples; real vendor BSPs (meta-intel, meta-ti,
meta-raspberrypi, and so on) live in their own layers.

In the Scarthgap LTS version the reference set in
meta-yocto-bsp centers around:

- BeagleBone (`beaglebone-yocto`)
- Generic ARMv8 / 64-bit Arm machine (`genericarm64`)
- Generic x86 and x86-64 PC class machines

> Always check the current machine list in the actual `meta-yocto-bsp/conf/machine/`
> directory of the branch you check out.

> To develop on hardware that is not one of these reference boards, you add a
> hardware-specific Yocto layer (a vendor BSP) on top of Poky. Poky alone is for
> getting started and for the QEMU emulator targets.

## 2.6 The other pieces in Poky, and a summary

Two more components round out Poky:

- **meta-poky** - Poky-specific metadata and default policy (the configuration
  that makes "Poky" a distribution rather than just raw oe-core).
- **Documentation** - the Yocto Project source files used to produce the manuals.

### Putting it together

Poky, the thing you download, includes:

- some OpenEmbedded components (oe-core)
- BitBake (the build engine)
- demo BSPs (meta-yocto-bsp)
- helper scripts to set up the environment
- the QEMU emulator so you can test the image without hardware

### How the layers nest

The diagram below (an ASCII rendering of the classic Poky reference diagram)
shows the containment relationship. The Yocto Project is the outermost umbrella.
Poky sits inside it. OpenEmbedded-Core plus BitBake form the innermost base that
Poky is built on.

```
+---------------------------------------------------------------------------+
|  YOCTO PROJECT (YP)                                                        |
|                                                                            |
|   +---------------------------------------+    YP-compatible BSPs          |
|   |  POKY                                 |      (e.g. meta-intel, meta-ti)|
|   |                                       |    YP-compatible layers        |
|   |   +-----------------------------+     |    Autobuilder (QA / testing)  |
|   |   |  OPENEMBEDDED-CORE          |     |    Developer tools             |
|   |   |  (oe-core metadata)         |     |    Production build tools      |
|   |   |  +                          |     |    Other layers and components |
|   |   |  BitBake build engine       |     |    Pseudo                      |
|   |   +-----------------------------+     |    Documentation               |
|   |                                       |                                |
|   |   meta-poky                           |                                |
|   |   meta-yocto-bsp                      |                                |
|   |   Documentation                       |                                |
|   +---------------------------------------+                                |
|                                                                            |
|   (OpenEmbedded itself, via meta-openembedded, is the broader foundation   |
|    that oe-core is shared with.)                                           |
+---------------------------------------------------------------------------+
```

Reading it from the inside out:

- **oe-core + BitBake** is the engine and the shared base recipes.
- **Poky** wraps that with policy (meta-poky), reference hardware support
  (meta-yocto-bsp), and documentation, giving you a buildable example
  distribution.
- **The Yocto Project** wraps Poky with everything needed to ship real products:
  vendor BSPs, additional layers, automated QA (the Autobuilder), developer
  tooling, and documentation.

---

Next: `03-build-host-setup.md` covers getting a machine ready to build.
