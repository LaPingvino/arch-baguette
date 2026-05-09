# Arch Baguette

Arch Baguette provides an automated build script and GitHub Actions workflow to create an Arch Linux image for ChromeOS Baguette, also known as Crostini Containerless.

Baguette runs Linux distributions directly inside the ChromeOS VM environment without using the traditional LXD-based Crostini container stack.

## Quick Start

> [!CAUTION]
> The build workflow installs dependencies with `apt` and has only been tested on Ubuntu 24.04 runners.

1. Fork this repository.
2. Open the **Actions** tab.
3. Run the **Build Arch Baguette Image** workflow manually.
4. Download the generated `arch-baguette-x86_64` artifact.
5. Extract `arch-baguette-x86_64.img.zst` and place it in your ChromeOS **Downloads** directory.
6. Open `crosh` and create the Baguette VM:

```sh
vmc create --vm-type BAGUETTE \
  --size 30G \
  --source /home/chronos/user/MyFiles/Downloads/arch-baguette-x86_64.img.zst \
  arch

vmc start --vm-type BAGUETTE arch
```

> [!NOTE]
> If you changed the image size in the workflow, use the same or a larger value for `--size`.

7. Enter the VM with `vsh`:

```sh
vsh arch
```

## Local Build

The script can also be run locally on Ubuntu 24.04:

```sh
sudo apt-get update
sudo apt-get install -y \
  arch-install-scripts \
  pacman-package-manager \
  archlinux-keyring \
  btrfs-progs \
  qemu-utils \
  zstd \
  rsync \
  git \
  curl \
  ca-certificates \
  gnupg \
  dirmngr

jj git clone https://github.com/Cusox/arch-baguette
cd arch-baguette

chmod +x ./build-arch-baguette.sh
./build-arch-baguette.sh
```

The compressed image will be written to:

```text
out/baguette_arch_rootfs.img.zst
```

## Launch Arch from the ChromeOS Terminal App

> [!TIP]
> On ChromeOS 147 and later, the `#crostini-containerless` flag is enabled by default.

According to [NixOS Baguette images in ChromeOS](https://aldur.blog/articles/2025/10/29/nixos-baguette-images-in-chromeos#how-to-launch-nixos-from-terminal), the ChromeOS Terminal app can launch a Baguette VM when the VM is named `termina`.

To use the Terminal app directly, create the VM with the name `termina`:

```sh
vmc create --vm-type BAGUETTE \
  --size 30G \
  --source /home/chronos/user/MyFiles/Downloads/arch-baguette-x86_64.img.zst \
  termina
```

> [!CAUTION]
> If your device was upgraded from an older ChromeOS release, the existing Linux VM may still use the legacy Crostini/LXD stack. Remove and reinstall the Linux development environment to migrate it to Baguette.

## References

- [nixos-crostini](https://github.com/aldur/nixos-crostini)
- [NixOS Baguette images in ChromeOS](https://aldur.blog/articles/2025/10/29/nixos-baguette-images-in-chromeos)
