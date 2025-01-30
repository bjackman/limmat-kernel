# Limmat config for kernel dev

This is my config for [Limmat](https://github.com/bjackman/limmat) for kernel
development.

Smoothly sharing & reusing configs is not a key design goal for Limmat (if you
really want to share your config, it's probably time to invest in actual CI),
and I haven't been very careful to ensure this config is hermetic; it might not
"just work" on your system (see the annoyingly many setup steps below). But it
should serve as a nice starting point and an illustration of Limmat's
functionality.

TODO: I don't think the `vmtest` stuff works on Ubuntu due to AppArmor issues.

## What it does

For every commit:

- Build the kernel under some different configs.
- Build some kselftests.
- Run those kselftests in a VM using [vmtest](https://github.com/danobi/vmtest).
- Run some KUnit tests.
- Run `checkpatch.pl` and check for TODOs and stuff.

  If the commit has a `Checkpatch-ignore` footer, it configures checkpatch
  errors to ignore for that commit. For example `Checkpatch-ignore:
  EMAIL_SUBJECT,COMPLEX_MACRO` will disable checkpatch's title checking and
  rules about macro hackery for that commit.

## Using on Debian

```sh
# Install Cargo (official installation mechanism 🙃)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
# Install Limmat and vmtest, which is used to run selftests on QEMU.
cargo install limmat vmtest

# Install general Linux kernel dev dependencies
sudo apt update
sudo apt install -y build-essential linux-source bc kmod cpio flex ccache \
    libncurses-dev libelf-dev libssl-dev dwarves bison qemu-system-x86

export LIMMAT_CONFIG=$THIS_REPO/limmat.toml

# Download the rootfs used for QEMU testing. (or see below to build it yourself).
cd $(dirname $LIMMAT_CONFIG)/mkosi-rootfs/
wget https://github.com/bjackman/limmat-kernel/releases/download/v0.1/image.tar.zst
mkdir image && tar --use-compress-program=unzstd -xf image.tar.zst -C image

cd path/to/kernel/tree
limmat watch origin/master  # Assuming origin/master is your base branch.
```

## Building rootfs for kselftest.

A pre-built version is provided in the releases. To build it youself:

(Tested on Ubuntu 24.04.1, with AppArmor restrictions disabled)

- `sudo apt install ccache debian-archive-keyrying`
- `sudo apt install pipx && pipx ensurepath`
- `pipx install git+https://github.com/systemd/mkosi.git`
- `cd mkosi-rootfs; mkosi -f`