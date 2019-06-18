dotfiles
========

This is my collection of user/application settings ("dotfiles") and personal
scripts. They are mostly adapted to my personal needs, and some scripts make a
few [assumptions](#assumptions) about the environment that may not necessarily
be considered "standard", so it is **not recommended** to just copy-paste them
as-is.

Nevertheless, I try to keep them as clean and non-WTF as possible, and people
are invited to take a look at them, get ideas for their own dotfiles, and drop
comments, suggestions, questions and bug reports if something seems odd.


XDG/FHS
-------

My goal is to keep the top-level user home directory as clean as possible by
honouring the [XDG base directory specification][fdo:xdgspec], adapted to
recreate the [Linux file system hierarchy][man:hier] (FHS) under `~/.local`. In
detail, this means that the following environment variables are set:

| Variable          | Location             |
| ----------------- | -------------------- |
| `XDG_CACHE_HOME`  | `~/.local/var/cache` |
| `XDG_CONFIG_HOME` | `~/.local/etc`       |
| `XDG_DATA_HOME`   | `~/.local/share`     |
| `XDG_STATE_HOME`  | `~/.local/var/lib`   |
| `XDG_LIB_HOME`    | `~/.local/lib`       |
| `XDG_LOG_HOME`    | `~/.local/var/log`   |

> ### Notes
> * `XDG_LIB_HOME`, `XDG_LOG_HOME` and `XDG_STATE_HOME` are non-standard, but
>   they are nevertheless necessary for representing the FHS locally.
> * `~/.local/var` is technically not supposed to be on this level (as this is a
>   variant of `/usr/local`), but for keeping it compact, I keep it here as
>   well.
> * Some applications unfortunately do not honour the XDG basedir specs, so I
>   additionally [set environment variables][file:pam_environment] or [write
>   wrapper scripts][dir:wrappers]&mdash;or simply weep (see also [issue
>   #7][issue:7]). The [*XDG Base Directory support*][aw:xdgsupport] article in
>   the Arch Linux wiki contains a list of applications that honour the specs
>   (or can be made to do so).

Furthermore, the `$PATH` variable is expanded to contain the following
locations:

| Location                    | Description |
| --------------------------- | --- |
| `~/.local/bin`              | User-specific executables (not tracked) |
| `~/.local/lib/dotfiles/bin` | User-specific executables provided by this repository |
| `~/.local/lib/utils/bin`    | User-specific executables provided by the [utils][gh:utils] repository |
| `~/.local/opt/altera/...`   | Various paths containing [Altera Quartus II][wp:quartus]-specific executables |


Usage
-----

For using the dotfiles, I clone this repository to `~/.local`, and then create
the symlink `~/.pam_environment → ~/.local/lib/dotfiles/pam_environment`.


Assumptions
-----------

The dotfiles have primarily been used on Arch Linux (and for limited use-cases
on Debian, too, although there are minor issues with tmux and [major issues with
PAM][issue:8], both related to Debian shipping antique versions of software).

Minimally expected software is **git**, **PAM**, **systemd** and **zsh**, for a
basic (headless) setup.

`/usr/sbin`, `/sbin` and `/bin` are assumed to have [merged][an:usrmerge] into
`/usr/bin` (note that this is a more extreme case of the [`/usr`
merge][fdo:usrmerge]).


Policies
--------

* Applications whose configuration is mixed up with other data (or generally not
  supposed to be manually edited) is put into `XDG_STATE_HOME`, reason being
  that I would like to track `XDG_CONFIG_HOME` with git as much as possible.
  This of course only works for applications that allow configuring the location
  of "config" files.

* Application history generally goes into `XDG_STATE_HOME` (see commit f1147a9
  for the reasoning). The only things that go into `XDG_LOG_HOME` are "real"
  logs, i.e. data that is no longer read and used by the application itself. The
  only things that go into `XDG_CACHE_HOME` are files that are non-essential and
  can quickly be regenerated by the application, if needed (which is both not
  the case for history files).

* Shell-agnostic configuration should happen in `XDG_CONFIG_HOME/sh`. This
  allows other, non-zsh shells to work correctly, too, without having to
  duplicate all the shell configuration. The shell-agnostic configuration is
  stored in `environment`, `login` and `config` as well as `profile` and the
  `profile.d` directory for application-specific profile snippets, while only
  shell-specific configuration (prompts, input, history, etc.) should happen in
  zsh's config.

* Although shell aliases are generally more lightweight than wrapper scripts,
  wrapper scripts allow being used from any environment (not just interactive
  shells). So it mostly depends on the application whether we create aliases or
  wrapper scripts for them.


Miscellaneous
-------------

As noted above, these dotfiles represent my personal setup&mdash;nevertheless, I
encourage people to take a look at it, mainly for learning how applications can
be made to (somewhat) conform to the XDG base directory specifications (and thus
have a clean home directory).

There are other, similar "experiments" out there:

* https://github.com/Earnestly/dotfiles (inspired by Plan9's filesystem layout)
* https://github.com/roosemberth/dotfiles (semi-fork of this repo, adapted for
  NixOS)


[an:usrmerge]: https://www.archlinux.org/news/binaries-move-to-usrbin-requiring-update-intervention/
[aw:pam]: https://wiki.archlinux.org/index.php/PAM
[aw:xdgsupport]: https://wiki.archlinux.org/index.php/XDG_Base_Directory_support
[dir:wrapper]: lib/dotfiles/bin
[fdo:xdgspec]: https://specifications.freedesktop.org/basedir-spec/latest/index.html
[fdo:usrmerge]: https://www.freedesktop.org/wiki/Software/systemd/TheCaseForTheUsrMerge/
[file:pam_environment]: pam_environment
[file:pkgbuild]: archlinux/PKGBUILD
[gh:utils]: https://github.com/ayekat/utils
[issue:7]: https://github.com/ayekat/dotfiles/issues/7
[issue:8]: https://github.com/ayekat/dotfiles/issues/8
[issue:12]: https://github.com/ayekat/dotfiles/issues/12
[man:hier]: http://linux.die.net/man/7/hier
[wp:quartus]: https://en.wikipedia.org/wiki/Altera_Quartus
