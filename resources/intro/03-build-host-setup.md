# 3. Setting Up the Build Machine

A Yocto build compiles an entire Linux distribution from source, including the
toolchain. That means the build host needs real resources and a known-good set
of packages. Get this right before doing anything else; most "it failed
mysteriously" problems for beginners are a missing host package or too little
disk space.

## 3.1 Hardware and OS prerequisites

- **Disk space:** at least **90 GB** of free space for a `core-image-sato`
  build for `qemux86-64`. Much more (150 GB +) is recommended if you run multiple builds or
  cache build artifacts. (For just `core-image-minimal` you can get by with
  less, but plan for tens of GB.)
- **RAM:** at least **8 GB**. More RAM (16 GB +) and more CPU cores (8 + cores) make builds
  dramatically faster, since BitBake parallelizes across cores.
- **Operating system:** a recent, supported Linux distribution. Officially
  tested distributions include recent releases of Ubuntu, Debian, Fedora,
  openSUSE, and AlmaLinux. Rolling-release and development distributions are often not
  supported.
- Minimum tool versions (automatically provided by most modern Linux distributions):
  - Git 1.8.3.1 or later
  - tar 1.28 or later
  - Python 3.8.0 or later
  - gcc 8.0 or later
  - GNU make 4.0 or later

> **Note:** These are the minimum supported versions for the Scarthgap (5.0) release.
> Most current Linux distributions provide significantly newer versions. If you are using
> a different Yocto Project release, check that release's system requirements,
> as newer releases may require newer tool versions.

If your distribution is too old to meet the tool-version minimums, the Yocto
Project provides a `buildtools` tarball that supplies known-good versions of
Git, tar, Python, and friends without upgrading your whole system.


## 3.2 Building on Windows or macOS

Yocto Project builds are not supported natively on Windows or macOS. Instead, use one of the following approaches to provide a Linux build environment:

- **Dual boot**: Install a supported Linux distribution (such as Ubuntu, Debian, Fedora, or openSUSE) alongside Windows or macOS-compatible hardware. Boot directly into Linux when performing Yocto Project development. This provides the best performance and compatibility.

- **Virtual machine (VM)**: Run Linux inside a virtualization platform such as **VMware Workstation**, **VMware Fusion**, **VirtualBox**, or **Parallels Desktop**. Install a supported Linux distribution in the VM and follow the Linux instructions in this course. Ensure the VM has sufficient CPU cores, memory, and disk space, as Yocto builds are resource-intensive.

- **WSL 2 (Windows Subsystem for Linux 2)**: On Windows 10 or later, install a Linux distribution inside WSL 2 and follow the Linux instructions. WSL 2 provides good performance and integration with Windows while maintaining a Linux-compatible environment.

- **Docker containers**: Run a Linux container using Docker Desktop or Docker Engine and perform the build inside the container. This approach provides a reproducible build environment and simplifies dependency management. However, some workflows may require additional configuration for device access, networking, or file permissions.



For this module, a native or virtualized Ubuntu install is assumed.

## 3.3 Installing the required host packages (Ubuntu / Debian)

Package names vary by distribution. The command below is the current list for
the Scarthgap LTS on Ubuntu/Debian. Run it once on a fresh host.

```
sudo apt install build-essential chrpath cpio debianutils diffstat file \
    gawk gcc git iputils-ping libacl1 liblz4-tool locales python3 python3-git \
    python3-jinja2 python3-pexpect python3-pip python3-subunit socat texinfo \
    unzip wget xz-utils zstd
```

You also need the `en_US.UTF-8` locale enabled:

```
sudo locale-gen en_US.UTF-8
```

**UPDATED (why this differs from the old command):**
The original notes used a package list aimed at the zeus release and an old
documentation URL (`yoctoproject.org/docs/latest/...`). A few things changed:

- The Python 2 package (`python`) and `pylint3` were dropped; everything is
  Python 3 now.
- Build-host extras like `gcc-multilib`, `libsdl1.2-dev`, `libegl1-mesa`, and
  `xterm` are no longer in the base required set (some were only ever needed for
  optional GUI/QEMU graphical features).
- New required packages appeared, notably `zstd`, `file`, `libacl1`,
  `python3-subunit`, and `locales`.
- The current documentation lives at **https://docs.yoctoproject.org** (the old
  `yoctoproject.org/docs/latest` path is gone). For the exact list matching the
  branch you build, see the "System Requirements" page of that branch, for
  example
  https://docs.yoctoproject.org/scarthgap/ref-manual/system-requirements.html

If you build on Fedora, openSUSE, or another distribution, use the package list
from the System Requirements page for your distribution rather than the apt
command above.

## 3.4 Quick verification

After installing, confirm the key tools are present and recent enough:

```
git --version
tar --version
python3 --version
gcc --version
locale | grep UTF-8
```

If all of these return sensible, recent values, the host is ready.

---

Next: `04-build-workflow-and-qemu.md` walks through the actual build.
