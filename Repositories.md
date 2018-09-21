:toc:
:toc-placement!:

[cols="7*a"]
|===
| image:images/xenomai-logo.png[Xenomai] |
  link:Start_Here[*Start Here*] | link:Setting_Up[*Setting Up*] |
  link:Programming[*Programming*] | link:Help[*Help!*] |
  link:How_To_Contribute[*Contribute*] | link:SiteMap[*Sitemap*]
|===

toc::[]

I-pipe repositories
===================

As explained in the link:Installing_Xenomai_3[Xenomai installation
guidelines], in dual kernel mode (aka _Cobalt_), Xenomai needs the
host kernel to provide support for the interrupt pipeline feature (aka
_I-pipe_). This feature is developed by different maintainers, for
each supported architecture in separate kernel trees.

Each I-pipe port consists of a set of patches applied to a reference
kernel. The following sections describe the purpose of the various
https://gitlab.denx.de/Xenomai/[I-pipe GIT trees] and the workflow
followed by their respective maintainer.

NOARCH tree
------------

Tree: https://gitlab.denx.de/Xenomai/ipipe-noarch[https://gitlab.denx.de/Xenomai/ipipe-noarch]
Maintainer: Philippe Gerum  <rpm@xenomai.org>

Changes to the mainline kernel which may be common to several or all
architectures for implementing the interrupt pipeline are maintained
in this tree. This provides a common base which may be shared by all
I-pipe architecture ports.

The NOARCH tree is based on stable releases from the mainline kernel
available at
git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git. We
usually track LTS mainline releases.

The development takes place in the _master_ branch of the ipipe-noarch
tree.

[CAUTION]
If you plan to contribute patches, please (re)base them on the tip of
the _master_ branch before sending them to the
mailto:xenomai@xenomai.org[Xenomai mailing list].

When the _master_ branch is deemed stable, a new branch is created
which branches off of the tip of _master_. Stable branches are named
`stable/<upstream-release>-noarch` in the ipipe-noarch tree, named
after the upstream stable release they are based on. Unlike the
_master_ branch, a stable branch is never rebased.  The most recent
stable branch(es) may be updated over time with bug fixes.

Right after a stable branch is created in ipipe-noarch, the _master_
branch is rebased on the next upstream release we are interested in,
and the development resumes from this point.

e.g.

  linux-stable.git                             linux-stable.git
+------------------+                         +------------------+
|     v4.14.58     | ....................... |     v4.14.62     |
+------------------+                         +------------------+
          |                                            |
          |       ipipe-noarch.git                     |
   rebase |           (master)                         |
          |     +------------------+                   |
          |     |      v4.14.58    |                   |
          +---> |         +        | ---+              +---> ...
                |    I-pipe bits   |    |
                +------------------+    |
                                        |
                  ipipe-noarch.git      | branch off
               (stable/4.14.58-noarch)  |
                +------------------+    |
                |                  |    |
                |  ipipe-4.14.58   | <--+
                |                  |
                +------------------+


[NOTE]
Since the NOARCH tree is missing the arch-specific I-pipe code, it is
not suitable for building kernels enabling the interrupt pipeline,
although this tree should still compile cleanly as a regular kernel.

ARM
---

Tree: https://gitlab.denx.de/Xenomai/ipipe-arm[https://gitlab.denx.de/Xenomai/ipipe-arm]
Maintainer: Philippe Gerum  <rpm@xenomai.org>

The I-pipe/ARM port is based on stable branches from the NOARCH tree.
It complements this code with the ARM-specific bits implementing the
interrupt pipeline.  The development takes place in the _master_
branch of the ipipe-arm tree.

[CAUTION]
If you plan to contribute patches, please (re)base them on the tip of
the _master_ branch before sending them to the
mailto:xenomai@xenomai.org[Xenomai mailing list].

When the _master_ branch is deemed stable, a new branch is created
which branches off of the tip of _master_. Stable branches are named
`stable/<upstream-release>-arm` in the ipipe-arm tree, named after the
upstream stable release they are based on. Unlike the _master_ branch,
a stable branch is never rebased.

Right after a stable branch is created in ipipe-arm, the _master_
branch is rebased on the next NOARCH release we are interested in, and
the development resumes from this point.

e.g.

    ipipe-noarch.git                                  ipipe-noarch.git
+-----------------------+                         +-----------------------+
| stable/4.14.58-noarch | ....................... | stable/4.14.62-noarch |
+-----------------------+                         +-----------------------+
          |                                            |
          |       ipipe-arm.git                        |
   rebase |           (master)                         |
          |     +------------------+                   |
          |     |  4.14.58-noarch  |                   |
          +---> |         +        | ----+             +---> ...
                |  I-pipe/ARM bits |     |
                +------------------+     |
                                         |
                   ipipe-arm.git         | branch off
               (stable/4.14.58-arm)      |
               +--------------------+    |
               |                    |    |
               |  ipipe-arm-4.14.58 | <--+
               |                    |
               +--------------------+

[NOTE]
Stable branches from ipipe-arm might be updated with bug fixes on
occasion for some kernel releases, but you should not assume this
happens routinely for any release.