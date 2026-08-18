# CroissAndro kernel prebuilts

This repository is the reviewed binary handoff between the CroissAndro kernel
build and Android product build. It contains kernel artifacts that AOSP may
consume reproducibly; it contains neither ACK source nor Kleaf configuration.

The AOSP local manifest currently mounts this repository at:

```text
aosp/prebuilts/kernel/croissandro/6.18/
```

## Repository family

| Repository | Responsibility |
|---|---|
| [`manifest`](https://github.com/croissandro/manifest) | Adds CroissAndro projects to the AOSP and ACK workspaces |
| [`croissandro`](https://github.com/croissandro/croissandro) | Android product, board and device policy |
| [`kernel-build`](https://github.com/croissandro/kernel-build) | Hyper-V kernel configuration and Kleaf build logic |
| [`kernel-prebuilts`](https://github.com/croissandro/kernel-prebuilts) | Reviewed kernel artifacts consumed by AOSP — this repository |

## Current state

The repository is intentionally empty apart from project metadata and
licenses. PI-0 builds only `system.img` and has no kernel consumer. Do not add
placeholder binaries or copy arbitrary output from `kernel/out`.

PI-1 must define and test the exact Android boot-image contract before the
first artifact publication. At minimum, that decision must cover:

- the filename consumed by `TARGET_PREBUILT_KERNEL` or its replacement;
- whether AOSP consumes `bzImage` directly or a packaged boot image;
- diagnostic ramdisk ownership;
- required built-in drivers versus loadable modules;
- kernel version and Android kernel compatibility requirements.

## Publication requirements

Every published set must be reproducible and traceable. Record:

- ACK manifest branch and exact `common` commit;
- `kernel-build` commit;
- compiler/toolchain revision;
- effective kernel configuration or its digest;
- build command and relevant Kleaf flags;
- SHA-256 checksums for every consumed artifact;
- validation results and known limitations.

Keep symbolization artifacts such as `vmlinux`, `System.map`, and
`vmlinux.symvers` when licensing and repository-size policy permit. They are
not boot inputs, but they are essential for diagnosing kernel failures.

## Version policy

The path currently declared by `aosp-manifest.xml` is `6.18`. That directory
name is an interface contract, not a label for whichever kernel was built most
recently.

The current exploratory ACK checkout is mainline 7.x. Do not publish those
artifacts under `6.18`. Before PI-1, either select and pin an ACK 6.18 source
line or change the AOSP publication path and device contract together.

Release manifests should pin this repository to a reviewed commit or tag
rather than follow mutable `main`.

## Consumer boundary

Only the Android device repository may define how these artifacts are packed
into `boot`, `init_boot`, `vendor_boot`, or module partitions. This repository
must not contain device makefiles, BoardConfig policy, ACK source patches, or
host Hyper-V services.

Normal AOSP builds consume a pinned artifact set and must not invoke Kleaf or
depend on a developer's kernel output directory.
