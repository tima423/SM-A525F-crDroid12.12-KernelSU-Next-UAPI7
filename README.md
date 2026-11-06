# KernelSU-Next for Samsung Galaxy A52s (SM-A525F)

**Device:** Samsung SM-A525F (`a52q`)  
**ROM:** crDroid Android 16, release 12.12  
**Kernel:** Linux 4.14.357  
**KernelSU-Next:** UAPI 7; Manager 3.4.2 (33355), package `com.rifsxd.ksunext`

[Русская версия](README-RU.md)

> Device- and ROM-specific. Do not use on another model or ROM. The boot image has not been tested on a physical phone. Keep a known-good boot image and recovery method before attempting installation.

## Project contents and publication status

The project source tree is intended to contain `kernel/` and `KernelSU-Next/`, with upstream license files retained. The kernel source is based on `673b80046ab92a5823fe10181f06be31382ea041`; KernelSU-Next is based on `0c9c4438a6273c5bd80c9fd8816a4c9801e108fe`.

The full source archive has not yet been attached to this repository. The provided archive contains generated object files and Android CMake cache data, so it must be cleaned before publication to honor the requirement not to publish build results. No boot image, APK, or private signing key is included here.

The changes target KernelSU-Next UAPI v7. They add a root-request dialog with a 30-second timeout and grant choices of once, 10 minutes, 30 minutes, or forever. The dialog and grant behavior have **not** been verified on a physical device; no guarantee is made that the root prompt works.

## Requirements

Use a Linux Android-kernel build environment with `make`, the usual kernel build dependencies, Clang 21, and an AArch64 GCC cross-toolchain (`aarch64-linux-gnu-`). The commands below are run from `kernel/`.

## Build

Configure the device kernel:

```sh
make O=out ARCH=arm64 LLVM=1 LLVM_IAS=1 CROSS_COMPILE=aarch64-linux-gnu- \
  CLANG_TRIPLE=aarch64-linux-gnu- vendor/lineage-a52q_defconfig
```

Build the compressed kernel image:

```sh
make O=out ARCH=arm64 LLVM=1 LLVM_IAS=1 CROSS_COMPILE=aarch64-linux-gnu- \
  CLANG_TRIPLE=aarch64-linux-gnu- \
  KSU_VERSION_OVERRIDE=33355 KSU_VERSION_TAG_OVERRIDE=v3.4.2 \
  KSU_MANAGER_PACKAGE=com.rifsxd.ksunext KSU_NEXT_MANAGER_SIZE=791 \
  KSU_NEXT_MANAGER_HASH=25586317e06049d54e7c2f35ccb71ec06254cdad56db38f1cde09f44e3982b05 \
  -j12 Image.gz
```

This configuration sets `CONFIG_MODULES=y`, but no drivers are configured as modules, so there are no driver `.ko` files; drivers are built into the kernel. Build outputs such as `Image.gz`, `vmlinux`, `System.map`, and `.config` must remain local and must not be committed.

A clean rebuild has not been independently reproduced for this publication. Do not treat the commands as a verified build result until you run them successfully in the stated environment.

## Installation notes

This repository does not distribute a boot image or APK. Build the kernel and package the boot image for your own device/ROM; obtain/build the Manager separately. Back up the current boot image and verify your restore method before flashing. Use only a device-appropriate procedure; a raw `.img` is not an Odin `.tar` package. If the device fails to boot or root does not work, restore the backup.

The Manager package is `com.rifsxd.ksunext`, version 3.4.2 (33355), UAPI 7. The referenced APK was signed with a local debug certificate; its private signing key is **not included**. Its signing-certificate SHA-256 is:

```
25586317e06049d54e7c2f35ccb71ec06254cdad56db38f1cde09f44e3982b05
```

Android does not allow an in-place app update when the signatures differ. A locally rebuilt APK signed with another key may require uninstalling the existing Manager first; kernel manager-size/hash settings must also match the APK being used.

## Known limitations

- Neither the boot image nor the root-request dialog has been tested on a physical phone.
- Build reproducibility has not been verified for this publication.
- No driver `.ko` files are expected with the supplied configuration.
- Boot images, APKs, generated build outputs, and private keys are intentionally excluded.
- Do not flash on devices other than SM-A525F (`a52q`) with crDroid 16 / 12.12.

## Credits and licensing

Retain and follow the license files included with the source projects. KernelSU-Next and the kernel are upstream projects; this repository describes device-specific changes and build instructions.
