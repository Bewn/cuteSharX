From 2af536e72f975da365ab93c2ab3bf1e0525a352a Mon Sep 17 00:00:00 2001
From: Luke Shumaker <lukeshu@parabola.nu>
Date: Tue, 24 Jul 2018 00:00:15 -0400
Subject: [PATCH 00/38] notsystemd-239.1 release
MIME-Version: 1.0
Content-Type: text/plain; charset=UTF-8
Content-Transfer-Encoding: 8bit

________________________________________________________________________________
Preface

  This is the fifth release of "notsystemd", a project to turn the various
  components of systemd into independent pieces that can be used no matter
  which software is used for PID 1; in a similar spirit to eudev or elogind.

  notsystemd is developed as part of the Parabola project.  notsytemd
  development tracks not upstream systemd, but the version of systemd shipped
  by Parabola GNU/Linux-libre (which in turn tracks the version shipped by
  Arch Linux).

________________________________________________________________________________
Notice

   This release drops the GNU Autotools-based build system.  The build system
   now depends on Meson and Ninja.

________________________________________________________________________________
News

   - The GNU Autotools-based build system has been dropped.  The build system
     now depends on Meson and Ninja.
   - nspawn had numerous bugs related to cgroup delegation fixed
   - nspawn should now work on any cgroup setup, including non-systemd
     cgroup-v1/v2 hybrid setups
   - nspawn now accepts more values for UNIFIED_CGROUP_HIERARCHY to allow
     better control of the cgroup setup of the container.  As before, this
     variable may only be set on cgroup-v2-only or systemd setups.
   - udevd/udevadm are now included in the list of things that definitely work
     non non-systemd systems.  The worked in previous releases too, but I
     wasn't yet comfortable saying that they did.

________________________________________________________________________________
Functional Description

  At a minimum, the following programs should be functional on non-systemd
  systems:

    systemd-nspawn

    systemd-machine-id-setup
    systemd-tmpfiles
    systemd-sysusers

    systemd-udevd
    udevadm

  Notes about notsystemd's nspawn:

    systemd-nspawn is a tool for running containers.  By default, it
    attempts to speak with other parts of the systemd family, but this can
    be disabled:

     - By default it attempts to register its containers with machined over
       its D-Bus API.  If machined isn't running, or can't be started
       automatically by dbus-daemon, then this will fail.  If you can't or
       won't run machined, you will need to pass the `--register=no` flag to
       systemd-nspawn.  notsystemd has not yet made any effort to make
       systemd-machined usable on non-systemd systems.

     - If `--register=no`, then by default it attempts to ask systemd to
       create a cgroup ("scope unit" in systemd's terminology) for the
       container over its D-Bus API.  If you aren't running systemd, this
       will fail.  This can be disabled by passing the `--keep-unit` flag to
       systemd-nspawn.  notsystemd has not yet made any effort to implement
       systemd's D-Bus API for non-systemd systems.

    There are many ways to arrange cgroup-v1 hierarchies, and
    cgroup-v1/v2-mixed hierarchies, and so different init systems do it
    differently.  Fortunately, cgroup-v2 is more uniform.

    By default, nspawn will attempt to re-create the cgroup setup of the
    host in the container.  If the host uses cgroup-v2 only, or if it uses
    one of the specific cgroup-v1 or v1/v2-mixed arrangements used by
    systemd: it can translate this in to a few other setups for the
    container:

     - (automatically) If nspawn detects systemd-231 or 232 in the
       container, then it will automatically translate to a cgroup setup
       that these versions understand:

       * container has systemd-231: If the host is using a
         cgroup-v1/v2-mixed setup, then the container will use a systemd's
         cgroup-v1 setup.
       * container has systemd-232: If the host is using a systemd-233's
         cgroup-v1/v2-mixed setup, then the container will use a systemd's
         cgroup-v1 setup.

     - (manually) The cgroup setup of the container can be set using the
       UNIFIED_CGROUP_HIERARCHY environment variable.

       * "legacy", "legacy-sd", or falsey values sets the container to
         systemd's cgroup-v1 setup.
       * "hybrid-sd232" sets the container to systemd-232's
         cgroup-v1/v2-mixed setup.
       * "hybrid" or "hybrid-sd233" sets the container to systemd-v233+'s
         cgroup-v1/v2-mixed hybrid setup.
       * "unified" or truthy values sets the container to cgroup-v2 mode,
       * "inherit" sets the container to inherit the host's cgroup setup
         (potentially overriding the automatic detection above).

    If the host has a cgroup-v1 or v1/v2-mixed setup that is not one used by
    systemd, then it is an error to set $UNIFIED_CGROUP_HIERARCH to anything
    other than "inherit".

  Notes about notsystemd's udevd:

    systemd-udevd should be fully functional on systems using other init
    systems; however, notsystemd does *not* include init-scripts/unit-files
    to launch udevd under other init systems, you will need to get those
    from elsewhere.  Gentoo's "udev-init-scripts" are known to work with
    systemd-udevd on OpenRC systems.

      https://gitweb.gentoo.org/repo/gentoo.git/tree/sys-fs/udev-init-scripts

________________________________________________________________________________
Compiling notsystemd-239.1

  Like previous versions of notsystemd, this release of notsystemd is
  published as a set of patches, rather than as a full source tarball.

  The patches should apply cleanly over Parabola's systemd 239.0-2.parabola3;
  the most recent version of systemd last shipped by Parabola at this time.
  The details of that release can be found at (pay particular attention to the
  prepare() function in the PKGBUILD)

    https://git.parabola.nu/abslibre.git/tree/libre/systemd?id=b6e79542570abfc4fb6ee542fe121bf5c68e3c82

  notsystemd expects that any changes applied by Parabola to already be
  applied (though I would be surprised if you had trouble applying the
  notsystemd patches without without them).  If you do have trouble applying
  them to a different base, see the note about mechanical changes below.

________________________________________________________________________________
Description of changes

  Backports (6):

      These are changes that have been made in upstream systemd, but were
      not included in systemd-239.

    (0001) nspawn: Simplify mkdir_userns() usage, and trickle that up
    (0002) nspawn: Simplify tmpfs_patch_options() usage, and trickle that up
    (0003) nspawn: Move cgroup mount stuff from nspawn-mount.c to nspawn-cgroup.c
    (0004) nspawn: sync_cgroup(): Rename arg_uid_shift -> uid_shift
    (0005) cgroup-util: cg_kernel_controllers(): Fix comment about including "name="
    (0006) nspawn: mount_sysfs(): Unconditionally mkdir /sys/fs/cgroup

  Mostly-mechanical changes (2):

      These changes should have no user-visible affects; they are all code
      cleanup, organization, and plumbing changes that are mechanical in
      nature (search/replace, copy/paste), and may be easier to re-create
      by hand rather than by applying the patch when being applied to a
      different base.

    (0007) nspawn: nspawn-cgroup.{c,h}: s/unified_requested/inner_cgver/
    (0008) nspawn: nspawn.c: s/unified_cgroup_hierarchy/inner_cgver/

  Better 232/233 distinction (4)

      The first 3 patches should have no user-visible affects; they are
      all code cleanup, organization, and plumbing changes for the 4th
      commit, which fixes systemd bug #6310, in which it fails to
      differentiate between systemd-232-style cgroup v1/v2 mixed setups
      and 233-style setups.

    (0009) cgroup-util: Merge the unified_cache and unified_systemd_v232 caches
    (0010) cgroup-util: Add cg_version() to get the raw CGroupUnified enum
    (0011) cgroup-util,nspawn: Use switch cases around CGroupUnified when possible
    (0012) nspawn: Allow the container to inherit a 232-style hybrid (#6310)

  Non-functional changes (16):

      These changes should have no user-visible affects; they are all code
      cleanup, organization, and plumbing changes that set the stage for
      user-visible changes below.

    (0013) cgroup-util: Split out cg_pid_get_path_internal()
    (0014) nspawn: if !cg_ns_supported() then force arg_use_cgns = false
    (0015) nspawn: Expand comments in detect_unified_cgroup_hierarchy()
    (0016) nspawn: Parse UNIFIED_CGROUP_HIERARCHY similarly to any other arg
    (0017) nspawn: Detect the outer_cgver once, and pass that around
    (0018) nspawn: Merge chown_cgroup(), sync_cgroup(), & create_subcgroup() into one cgroup_setup()
    (0019) nspawn: mount_legacy_cgns_supported(): Rename variables to not lie
    (0020) nspawn: get_v1_hierarchies(): Ditch a pointless check for "name=unified"
    (0021) nspawn: Change where we filter the name=systemd hierarchy
    (0022) nspawn: Track the inner child and outer child PIDs separately
    (0023) nspawn: nspawn-cgroup.c: Add dividers/headings
    (0024) nspawn: Add functions for deciding cgroup mounts before performing them
    (0025) nspawn: Decide all cgroup mounts/symlinks before performing any of them
    (0026) nspawn: Split off cgroup_decide_mounts() from mount_cgroups()
    (0027) nspawn: Go ahead and always decide the cgroup mounts in the outer child, not inner
    (0028) nspawn: Hoist the decision to sync cgroups from the callee sync_cgroup() to the caller cgroup_setup()

  Functional changes (10):

      These are the user-visible changes.

    (0029) nspawn: Also allow the user to force hybrid cgroup mode
    (0030) nspawn: Improve --help text
    (0031) nspawn: Clarify sync_cgroup(); tmp dirname, error message
    (0032) nspawn: (Re)mount the systemd hierarchy RO in the outer child, not inner
    (0033) nspawn: Rename chown_cgroup_path → cgdir_chown
    (0034) nspawn: nspawn-cgroup.c: Drastically modify cgroup_setup()
    (0035) cgroup-util,nspawn: Add a special "inherit" cgroup mode for nspawn
    (0036) cgroup-util: Get stricter about mode detection
    (0037) nspawn: detect_inner_cgver_from_image(): Don't assume old systemd
    (0038) nspawn: detect_inner_cgver_from_image(): Be more detailed in the final log_debug

 doc/ENVIRONMENT.md         |  16 +-
 meson.build                |   2 +-
 src/basic/cgroup-util.c    | 122 +++--
 src/basic/cgroup-util.h    |   8 +-
 src/nspawn/meson.build     |   1 +
 src/nspawn/nspawn-cgroup.c | 967 +++++++++++++++++++++++++++++++++----
 src/nspawn/nspawn-cgroup.h |  13 +-
 src/nspawn/nspawn-mount.c  | 457 +-----------------
 src/nspawn/nspawn-mount.h  |   7 +-
 src/nspawn/nspawn.c        | 437 +++++++++++------
 10 files changed, 1282 insertions(+), 748 deletions(-)

-- 
2.18.0

Happy hacking,
~ Luke Shumaker
