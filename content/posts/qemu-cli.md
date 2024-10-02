---
date: 2024-10-02
draft: false
params:
  author: Stradex
title: KVM local server with QEMU and tmux.
---

So I started using QEMU a few weeks ago to emulate a MacPro in my computer, and personally I liked it a lot.
Now I wanted to try launching my own local server to do all my experiments and stuff that can potentially break the OS without fearing of breaking my computer or my Digital Ocean VPS.

## What we would do?

Basically, for this example, we will use QEMU, a Debian image and tmux. We are going to first install debian using QEMU, then we are going to forward the host (my own PC) 22 port to the 2222 guest (QEMU KVM) port so we can access via ssh just using ssh -p 2222 user@localhost

## Let's get to work.

[Official QEMU reference](https://www.qemu.org/download/#linux)

1. Let's first install QEMU. In my case I use a debian-based distro.

```
apt-get install qemu-system
```

2. Then we are going to make a folder for our KVM.

```
mkdir my_kvm
cd my_kvm
```

3. Let's download, in my case, a debian iso image.

```
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/debian-12.7.0-amd64-DVD-1.iso
```

4. Let's create a image for our virtual machine. In mycase I would assing 30GB to my KVM.

```
qemu-img create -f qcow2 virtual_debian.img 30G
```

5. Let's install Debian in our image. In my case, my KVM will have 2GB of RAM.

```
kvm -hda virtual_debian.img -cdrom debian-12.7.0-amd64-DVD-1.iso -m 2048 -net nic -net user
```


6. After I installed Debian in my KVM, I you have to generate your ssh key-pair. It is up to you if you want to use a passphrase or not.

7. After I configured my ssh key par, shut down your KVM and then install tmux.

```
apt install tmux
```

8. After installing tmux, we will use it to be able to start out KVM without graphics and then detach from tmux so we can have our KVM running in the background to then be able to connect to our server via SSH.

For this, we will add a little modification in your KVM QEMU launch params, we will forward the host (Your PC) 2222 port to the guest (KVM machine) 22 ssh port. So then we can connect via ssh to our local KVM using the 2222 port.

```
# First we open tmux
tmux a

# Then we launch our KVM in nographic mode and also map the ports.
kvm -hda virtual_debian.img -m 2048 -net nic -nographic -net user,hostfwd=tcp::2222-:22

# To detach from tmux after the QEMU starts, we can use Ctrl + b + d
```

9. Connect to your KVM that it is running in the background

```
ssh -p 2222 root@localhost
```
