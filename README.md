# Limmat config for kernel dev

This is my config for [Limmat](https://github.com/bjackman/limmat) for kernel
development. It's not very hermetic, it can serve as inspiration but don't expect
everything to Just Work on your system too.

As well as Limmat, you need [vmtest](https://github.com/danobi/vmtest)
installed. You also need to run [mkosi](https://github.com/systemd/mkosi) in the
`mkosi-rootfs/` directory of this repo for things to work.

TODO: This won't work unless it's stored at `~/src/limmat-kernel`. Need to add a
Limmat feature to avoid this problem.

TODO: Figure out what does and doesn't work on my home computer.
