# secureblue-backup

Backup and disaster recovery for [secureblue OS](https://secureblue.dev/).

This repository provides instructions to implement a complete backup strategy for secureblue's atomic architecture: user data preservation, system configuration capture, layered package reproducibility, deployment state tracking, and base image restoration.

**View the full guide here:** [secureblue-backup](https://x144k.github.io/secureblue-backup)

![Stack Diagram](images/secureblue-backup-diagram.png)

## Prerequisites

This guide assumes you are running secureblue and are comfortable with the terminal. Three axioms govern this strategy: `/usr` is managed base state (never backed up; it is rebased), Btrfs deployment snapshots are local-only recovery tools that die with the disk, and a backup plan is only valid after it has been tested end-to-end.

## My setup

I built this on my daily driver running secureblue on a Lenovo Legion 7 Pro (16IRX8H). Clean install with `borgbackup`, `fastfetch`, `ivpn`, and `keepassxc` layered for daily use.

- **OS:** secureblue (Fedora Silverblue/Kinoite base)
- **Hardware:** Intel i9-13900HX; NVIDIA GeForce RTX 4090
- **Install type:** Clean install / Rebase from Fedora 44

## Why this exists

rpm-ostree systems fragment state across multiple layers. A full-disk clone captures the base image unnecessarily, wasting space and complicating restoration. A file-level backup restricted to `/home` misses `/etc` and `/var` entirely.

This guide documents the specific split: what to preserve, what to ignore, and how to reconstruct a working system from those pieces. It's not about finding a new backup tool; it's about applying existing tools correctly to an atomic filesystem layout.

## Components

- **User Data:** Selective home directory backup, excluding cache, temp, and Flatpak runtime data
- **System Config:** Capture of `/etc` overrides, `/var` persistent state, and custom systemd units
- **Layered Packages:** Reproducible package list export for `rpm-ostree` rebuilds
- **Deployment State:** Deployment hash, rollback history, and pinned deployment tracking
- **Verification:** Automated restore testing and integrity checks
- **secureblue Base:** Atomic OS; restored via `rpm-ostree rebase`, not backed up
