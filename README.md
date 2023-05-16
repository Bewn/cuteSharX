# cuteSharX
 Possibly the user environment of a possibly named calmunix operating system, a coincidental anagram of mac-linux. The cutefishOS project seems to have died and left with it a cute set of visually mac-like linux tools. I had been working on a reproducable containerized arch installer and this is a fitting way to 1. give the cutefish project a life-after-death and 2. give the arch base install a simple and well-designed UI. Somewhat playfully being an obvious macOS clone visually, osX was just "borrowed" BSD unix, so going full cirle I give the code added by me a BSD license and implement better-than-mac functionality. (there is also the potentential of pre-configuring qemu to run macos itself in a vm but that should be a subproject, and already is by someone else).
 
 aims at a re-implementation of cutefishOS as not debian-with-some-gui-apps, but a proper and different unix system, more generally a misc directory for setting up a kernal and package manager with good config and a modern ui post-install, the system should be easily understood and accessible to a unix oriented user.
 
 Semi-ironically coding this entirely via ssh from an ipad. aim to use waypipe project for wayland `ssh -X` forwarding
 
 It has no strict philosophical stance or guarentees, it just aims to be simple and useful and done in the spirit of readable and intentional code. Not aiming to be an organization or monolith (except the kernel... for now!) but to include known-to-me community-made tools generally without guarantee, arch user repo and github projects used or reccommended. Possible edition Communix with emphasis on modern implementations of standard programs. 
 
 the easy way is just installing the cutefish packages on arch normally but i think it could be interesting to fork the main ui apps and work on them. Attempt to port evrything to wayland if possible.
 
 If you're the type to read arch as ark then it's a good pun on arch linux, but I'm more of an arch with a ch like chair. sharch sounds not so good.
 ...
 
 possible aim for gentoo + guix for total setup control without the burden of re-compiling chromium for several hours  because you changed a use flag. Plus the freedom to sacrifice freedom by not being strictly a guix install.
 
 By starting from nothing but a gentoo stage 3 or arch base + base-devel we should be able to ensure a clean environment for making a stable-bleeding-edge/"curated" distro
 
 Maybe look into xen
 
 aim to use modern tools and minimal dependencies. possibly fork void linux and use runit instead, but realistically systemd is useful and ubiquitous.
 
 start with project in qemu for repoducable builds on different architectures.
 
 bsd license for original code, gpl etc inherited from all dependecies.

undecided how repos will be structured. Maybe this should be strictly the desktop environment and anoyther repo for setting up a reproducable containerized lxc based install with automaticly encrypted base system. 
