# cuteSharX
 aims at a re-implementation of cutefishOS as not debian-with-some-gui-apps, more generally a misc directory for setting up arch with good config and a modern ui post-install, the easy way is just installing the cutefish packages on ach normally but i think it could be interesting to fork the main ui apps and work on them. Attempt to port evrything to wayland if possible.
 
 possible aim for gentoo + guix for total setup control without the burden of re-compiling chromium for several hours  because you changed a use flag. Plus the freedom to sacrifice freedom by not being strictly a guix install.
 
 By starting from nothing but a gentoo stage 3 or arch base + base-devel we should be able to ensure a clean environment for making a stable-bleeding-edge/"curated" distro
 
 Maybe look into xen
 
 aim to use modern tools and minimal dependencies. possibly fork void linux and use runit instead, but realistically systemd is useful and ubiquitous.
 
 start with project in qemu for repoducable builds on different architectures.
