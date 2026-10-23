# virtualbox-coreboot

VirtualBox 7.2.20 source patches that let VirtualBox run a **coreboot** firmware ROM as
the guest's EFI firmware. Companion to the firmware forks `org-ai-assisted/coreboot` and
`org-ai-assisted/edk2` (the coreboot + UefiPayload that boots Kicksecure to a rendered
desktop under VirtualBox).

VirtualBox has no public git upstream, so this repo pins only the two modified source
files against the 7.2.20 baseline (see `BASELINE.md`). The first commit is pristine 7.2.20;
the second is our change, so `git diff <baseline>..<head>` (or `git format-patch -1`) is the
applyable patch.

## The two changes

- `src/VBox/Devices/EFI/DevEFI.cpp` -- new `CorebootRom` device config (default off). When
  set, the configured `EfiRom` is loaded as a raw coreboot image mapped at the top of 4GB
  (reset vector `0xFFFFFFF0`) instead of being parsed as an OVMF firmware volume. The shared
  device state is kept byte-identical to the normal path (a vestigial flash just below the
  ROM), so the ring-0 registration is unchanged. Enable per-VM:
  `VBoxInternal/Devices/efi/0/Config/CorebootRom=1` plus `EfiRom=<coreboot.rom>`.
- `src/VBox/Devices/PC/DevACPI.cpp` -- trigger the ACPI sleep transition on `SLP_EN`, not on
  the `SLP_TYP` value alone. coreboot legitimately pre-arms `SLP_TYP` (writes `SUS_TYP|SCI_EN`
  with `SLP_EN` clear); acting on `SLP_TYP` without `SLP_EN` spuriously powered the VM off, so
  `reset -s` / ACPI S5 did not behave.

## Build / use

Apply the diff onto a pristine VirtualBox 7.2.20 tree (see `BASELINE.md` for the source
tarball), build VirtualBox as usual, then start a VM with the extradata above and a coreboot
`.rom` built from the companion forks.
