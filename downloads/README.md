# FujikanonX JPEG quality check 06

Original Canon EOS 5D / Canon firmware 1.1.1, already running FujikanonX.
**New build: not yet hardware-tested.** Adds a read-only image-quality observer.
It does not set quality, enforce JPEG-only capture, apply recipes or register
another automatic task. Earlier manually confirmed parameter tests remain
available, but do not need repeating for this comparison.

## Update the working card

1. Extract this package on your computer and run `python3 VERIFY.py` from
   its top folder. All **177 files** must verify successfully.
2. Power off, wait for card activity to finish, insert the CF card in the
   reader, and back up its working root autoexec.bin and ML folder/configuration.
3. Replace only root `autoexec.bin` with this package's `candidate/autoexec.bin`.
   Keep existing support files/configuration. Eject and boot. No FIR or boot
   flag changes are involved.
4. In **Canon's native Quality menu**, select **Large/Fine JPEG only**—the
   option without RAW. Photograph that menu showing the selected option.
5. Open the firmware menu (DELETE/TRASH), then Other Tools → Prefs →
   **JPEG quality check**. Photograph the complete diagnostic screen.
6. Half-press to exit. Take one new photograph normally. Note its file number,
   power off after card activity stops, and inspect that shot on the computer:
   there should be a `.JPG` and no `.CR2` with the same base filename. Existing
   RAW files from earlier shots do not count; do not delete or format anything.
7. Send both screen photographs and report the new shot's filenames. Leave
   Canon set to Large/Fine JPEG for this first comparison. No RAW-mode test or
   additional parameter write test is needed yet.

The manual selection changes quality through Canon's controls. The firmware
check itself only observes. Matching one displayed code to a manual selection
and one file result does not establish automatic quality writes or persistence.

## What the screen reports

- Property **80000016**, identified from the uploaded Canon 1.1.1 image's
  GUI property dispatcher, with an expected four-byte payload.
- Seen/waiting/bad-size status, reported length and event count since boot.
- Raw hex and decimal values only. It deliberately supplies no mode label or
  JPEG-only claim until codes are compared against Canon and capture files.
- No decoded number when data is absent or has an unexpected length; an old
  cached value cannot be displayed as the latest successful packet.

Do not use the inherited shared camera constants (PROP_PIC_QUALITY 8000002f /
PICQ_LARGE_FINE 03010000) as a verified mapping for this original 5D.
The new handler has its own distinct ID and does not change global shared
property definitions, quality controls or the previously validated transactions.

The full build still contains manual parameter write-test actions and other
inherited tools. It is the **JPEG quality check** action that is read-only;
no claim that the entire firmware is write-free is made.

## Rollback

If the camera hangs, remove the battery promptly and restore the known-working
binary/configuration using the card reader. Do not delete root autoexec.bin.
For a responsive camera, power off normally and wait for card activity before
removing the card. Replacing a binary does not reverse settings selected in
Canon's menu. This stage adds no firmware-controlled quality changes.

## Source and host validation

The offline evidence is recorded in `docs/5d-picture-style-investigation.md`
and `docs/canon-111-analysis.json` in the project source. Canon firmware binary
bytes are not included. The original package checksum and version checks
establish internal consistency, not independent signature authenticity.

Tests cover the fifth handler's routing, four-byte decoding, bad/short/long/null
packets, bounded prefixes, stale-value clearing, UI display and back/half-shutter
exits. Compiled observer imports are limited to the portable observation
functions. Tests and ARM compilation do not establish the actual JPEG code.
