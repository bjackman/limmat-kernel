# Limmat config for kernel dev

This is my config for [Limmat](https://github.com/bjackman/limmat) for kernel
development. It's not very hermetic, it can serve as inspiration but don't expect
everything to Just Work on your system too.

TODO: This won't work unless it's stored at `~/src/limmat-kernel`. Need to add a
Limmat feature to avoid this problem. Then update docs below.

TODO: Figure out Ubuntu AppArmor issue then update docs below.

## Using on Debian

```sh
# Install cargo
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
# Install Limmat and vmtest, which is used to run selftests on QEMU.
cargo install limmat vmtest

# Install general Linux kernel dev dependencies
sudo glinux-add-repo prodkernel-gbuild stable
sudo apt update
sudo apt install build-essential linux-source bc kmod cpio flex \
    libncurses-dev libelf-dev libssl-dev dwarves bison qemu-system-x86 \
    grtev3-runtimes gbuild ccache

# TODO: This config MUST be cloned at this exact path! This needs to be fixed.
export LIMMAT_CONFIG=$HOME/src/limmat-kernel/limmat.toml

# Download the rootfs used for QEMU testing. (If you have mkosi installed, you
# could just run that in the mkosi-rootfs/ dir instead of downloading the
# prebuilt one).
cd $(dirname $LIMMAT_CONFIG)/mkosi-rootfs/
wget https://github.com/bjackman/limmat-kernel/releases/download/v0.1/image.tar.zst
mkdir image && tar --use-compress-program=unzstd -xf image.tar.zst -C image

cd path/to/kernel/tree
limmat watch origin/master  # Assuming origin/master is your base branch.
```

As well as Limmat, you need [vmtest](https://github.com/danobi/vmtest)
installed. You also need to run [mkosi](https://github.com/systemd/mkosi) in the
`mkosi-rootfs/` directory of this repo for things to work.

## Using on Ubuntu

Tested on 24.04.1:

- `sudo apt install ccache debian-archive-keyrying`
- `sudo apt install pipx && pipx ensurepath`
- `pipx install git+https://github.com/systemd/mkosi.git`
    - TODO: Figure out AppArmor thing.
- [Install cargo] and `cargo install vmtest`
    - TODO: And also for vmtest...
- Go to kernel directory
- `export LIMMAT_CONFIG=/path/to/this/repo/limmat.toml`
- `limmat watch origin/master` (or whatever other base branch).