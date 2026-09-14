# Android 17 QPR1+ kernel backport summary (17.0)

This branch remains on the android12-5.10 KMI baseline. The work below
focuses on userspace-facing interfaces needed by Android 17 QPR1+ without
changing exported symbol signatures or KMI-sensitive external ABI.

## Audit of existing backports and conventions

- Backport tag conventions are defined in `/README.md` (`UPSTREAM:`,
  `BACKPORT:`, `FROMGIT:`, `FROMLIST:`, `ANDROID:`).
- Existing Binder backports already present in history:
  - `0e10c6560` `BACKPORT: FROMGIT: binder: frozen notification`
  - `69d87eed0` `BACKPORT: FROMGIT: binder: frozen notification binder_features flag`
  - `db11e8297` `BACKPORT: FROMGIT: binder: add delivered_freeze to debugfs output`
  - `433a83ab0` `UPSTREAM: binderfs: add support for feature files`
- Existing filesystem and config support already present:
  - Incremental FS enabled (`CONFIG_INCREMENTAL_FS=y`)
  - F2FS compression/atomic-write ioctls present in `include/uapi/linux/f2fs.h`
  - EROFS/F2FS enabled in `arch/arm64/configs/gki_defconfig`
  - ashmem kept enabled (`CONFIG_ASHMEM=y`) while memfd is available

## Backported in this change

### 1) Binder extended error ioctl support

- Added `struct binder_extended_error` and
  `BINDER_GET_EXTENDED_ERROR` uapi in `include/uapi/linux/android/binder.h`.
- Added per-thread extended error tracking and ioctl handling in
  `drivers/android/binder.c`.
- Source ACK/common commits:
  - `bd32889e841c` (`binder: add BINDER_GET_EXTENDED_ERROR ioctl`)
  - `aed86f8add0e` (`binder: fix atomic sleep when get extended error`)

### 2) Binderfs feature advertisement for extended errors

- Added `features/extended_error` in `drivers/android/binderfs.c` so
  userspace can detect ioctl availability.
- Source ACK/common commit:
  - `d23386ed7019` (`binderfs: add extended_error feature entry`)

## Areas reviewed and intentionally not backported here

- BPF netfilter link type (`BPF_LINK_TYPE_NETFILTER`) and associated helper
  series were not backported in this patchset because it is a larger feature
  dependency chain across BPF and netfilter internals, not a surgical 5.10
  KMI-safe change.
- No additional f2fs/incfs/erofs ioctl backports were required in this tree:
  the Android 17-relevant interfaces reviewed are already present.

