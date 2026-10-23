# Baseline

- Upstream: VirtualBox 7.2.20 (Oracle), no public git repository.
- Pristine source tarball: https://download.virtualbox.org/virtualbox/7.2.20/VirtualBox-7.2.20.tar.bz2
- The first commit in this repo is the two files taken verbatim from that tarball
  (`src/VBox/Devices/EFI/DevEFI.cpp`, `src/VBox/Devices/PC/DevACPI.cpp`), with only a
  trailing-newline normalization. The second commit is our change.

Recover our patch against pristine upstream:

    git format-patch -1 HEAD          # applyable with patch -p1 / git apply
    git diff HEAD~1 HEAD              # same content as a plain diff
