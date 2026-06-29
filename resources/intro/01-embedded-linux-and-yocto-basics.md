# 1. Embedded Linux and Yocto Basics

## 1.1 The building blocks of an embedded Linux system

Before talking about Yocto, it helps to know what an embedded Linux system is
actually made of. Almost every such system is assembled from four core elements.

1. **Toolchain**
   The compiler and the other tools needed to create code that runs on your
   target device. This is the foundation: everything else is produced by the
   toolchain. Because the machine you build on (the host) is usually a different
   architecture than the device you build for (the target), this is normally a
   *cross* toolchain (for example, an x86-64 host producing ARM binaries).

2. **Bootloader**
   The first program that runs when the board powers on. It performs low-level
   hardware initialization and then loads the Linux kernel into memory and starts
   it. Common examples are U-Boot and GRUB.

3. **Kernel**
   The heart of the system. It manages system resources (CPU, memory, scheduling)
   and provides the interface between software and hardware through device
   drivers.

4. **Root filesystem (rootfs)**
   Everything that exists as files once the kernel has finished initializing: the
   shared libraries, the command-line utilities, configuration files, and the
   programs that make up user space.

There is also a fifth, application-specific element worth naming explicitly:

5. **Your application(s)**
   The collection of programs specific to what the product is supposed to do.
   This is what turns a generic Linux box into a device that weighs groceries,
   plays movies, controls a robot, or flies a drone. Strictly speaking these
   programs live inside the root filesystem, but it is useful to think of them as
   a distinct concern because they are the reason the device exists.

The Yocto Project exists to produce all of these elements, consistently and
reproducibly, from source.

## 1.2 What does the word "yocto" mean?

"Yocto" is the smallest SI metric prefix. Just as `m` (milli) means 10^-3 and
`u` (micro) means 10^-6, **yocto (y) means 10^-24**. The name was chosen to
evoke "very small," which fits the project's focus on building tiny, tightly
scoped Linux images for constrained devices.

## 1.3 What is the Yocto Project?

The Yocto Project provides open source, high-quality infrastructure and tools to
help developers create their own custom Linux distributions for any hardware
architecture. In plain terms: it is not itself a Linux distribution you download
and run. It is a *build system* and a set of shared recipes that let you produce
your own distribution, tailored exactly to your hardware and your product.

### A short history

The Yocto Project was founded in **2010**, in an effort to reduce duplicated work
across the embedded Linux industry and to provide resources and information for
both new and experienced developers. It was created as a collaboration among:

- many hardware manufacturers (chip and board vendors)
- open source operating system vendors
- electronics companies

The Yocto Project is also a working group of the **Linux Foundation**, which is
the neutral home that lets competing companies cooperate on shared
infrastructure.

## 1.4 Why use the Yocto Project? (Advantages)

1. **Widely adopted across the industry**
   A large ecosystem of semiconductor, operating system, software, and service
   vendors support and ship products based on the Yocto Project. Companies that
   have used or supported it include Intel, Arm, AMD, NXP, Dell, LG, Juniper
   Networks, and many others. Wide adoption means good documentation, active
   community support, and vendor-supplied support for real hardware.

2. **Architecture agnostic**
   It supports Intel/x86, ARM, MIPS, PowerPC (PPC), RISC-V, and other
   architectures. Chip vendors create and supply Board Support Packages (BSPs)
   for their hardware. If you have custom silicon, you can write a BSP for it.
   The project also supports a wide range of device emulation through QEMU (the
   Quick Emulator), so you can test without physical hardware.

3. **Images and code transfer easily**
   Because the build is defined by metadata rather than tied to one machine,
   output can move between architectures without switching to a new development
   environment. The same workflow targets a different chip by changing one
   setting.

4. **Flexibility through layering**
   Through customization and a layer model, a team can start from the base
   distribution and shape it into exactly what their product needs.

5. **Ideal for constrained embedded and IoT devices**
   Unlike a full general-purpose distribution, Yocto lets you build exactly what
   you need. You add only the packages and feature support the device actually
   requires, which keeps the image small, the attack surface low, and boot fast.

6. **Uses a layer model**
   Related functionality is grouped into separate bundles called layers. You add
   these grouped functionalities incrementally as needed. Layers let you extend
   the system, make customizations, and keep functionality organized and
   separable (for example, a vendor BSP layer stays cleanly separate from your
   own application layer).

## 1.5 A mental model: the Yocto Project as a machine

A useful way to understand what Yocto does is to treat it like a computing
machine that takes an input and produces an output.

**Input (your specification: what you want)**

- Kernel configuration
- Hardware / machine name (which board or emulator)
- Packages and binaries to install
- Distribution policy and features

**Output (a complete Linux-based embedded product)**

- Linux kernel
- Root filesystem
- Bootloader
- Device tree
- Toolchain (an SDK you can hand to application developers)

You describe what you want; the build system turns that description into a
bootable product. The rest of this module is about how that machine is built and
how you drive it.

---

Next: `02-yocto-architecture.md` looks at the components that make up the build
system itself: Poky, metadata, OpenEmbedded, BitBake, and BSPs.
