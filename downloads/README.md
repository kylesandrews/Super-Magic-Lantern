# FujikanonX Picture Style diagnostic 02

Read-only observation for original Canon EOS 5D / firmware 1.1.1. This is a
new, hardware-untested build. Earlier boot/UI, shortcut and logo checks were
reported working by the user on this camera; that does not validate this new
build or property mappings. No recipe effects or automatic JPEG-only behavior
are enabled. Existing Other Tools can still change settings.

## Update your already-working FujikanonX test card

1. Extract the ZIP on your computer and run `python3 VERIFY.py` from its top
   folder. All checksum checks must pass.
2. Power off, wait for card activity to stop, then insert the CF card in your
   computer's reader. Back up the working card's `autoexec.bin` and `ML` folder.
3. Copy **candidate/autoexec.bin** to the card root, replacing that file only.
   Keep your ML support files and existing configuration. Do not run a FIR or
   change camera boot flags; this package includes no installer.
4. Eject the card, boot the camera, open the firmware menu with DELETE/TRASH,
   and acknowledge any inherited beta warning.
5. From Film recipes, MENU → Other Tools → Prefs → **Picture Style check**.
   Select the action using the native menu controls (SET/right as appropriate).
6. Photograph the complete diagnostic screen and send it back. MENU returns
   to Prefs; half-shutter exits to shooting. The screen refreshes with the
   existing menu drawing cycle; no special logging or card writes are added.

The diagnostic observes messages already delivered to existing handlers. It
never requests properties or selects a Picture Style. Opening the Canon
User Def. 1 editor does not necessarily make it the active capture style.
The active code may therefore differ from User Def. 1 even when that editor
is open; this is not by itself an error.

## What to compare

Your supplied Canon screenshot showed User Def. 1 based on Standard,
sharpness 3, contrast 0, saturation 0, color tone 0. Leave these unchanged
for the first comparison. Expected decoded **User 1** values are therefore
Sharp=3, Contrast=0, Saturation=0, Tone=0 if the historical mapping is correct.

- `Active`: whether a packet has arrived, observed length and event count.
- `Active raw`: the first word. Historical mapping calls 0x21 User Def. 1.
  This mapping is under test; the screen does not assume it is confirmed.
- `User 1`: packet availability, length and event count. Historical expected
  length is 24 bytes, with six little-endian 32-bit words.
- Decoded values: contrast, sharpness, saturation and tone at historical
  offsets. Two raw-word rows include all six words for comparison.
- `Ranges plausible` means only that the numbers fit expected ranges. It
  does not prove mapping, freshness, write support or rendering behavior.

`waiting` means no message has been observed since this boot, not zero-valued
settings. `bad size` means the latest length differs from the historical
expectation; stale decoded values are cleared. Diagnostic 02 also shows a bounded raw
prefix of up to 32 bytes on that screen, for investigation without assuming
a parameter layout. Longer packets are explicitly shown as partial prefixes. Do not try unknown property
IDs, writes or debug tools to force data. If the screen is waiting or reports
bad sizes, send that screen first; no manual parameter changes are needed yet.
Counts are since boot, not timestamps; a captured value is the last delivered
message and is not an independently polled current-state guarantee.

## Rollback

If a hang occurs, remove the battery promptly and restore the exact working
binary and configuration backup on the computer. Do not delete the card's
root autoexec.bin as a recovery method. For a responsive camera, power off
normally and wait for card activity to end before removing the card.

The next gate is matching the captured packets to Canon's screen. A later,
separate controlled test may change a single value through Canon's own UI and
check its observed offset before any recipe-setting backend is enabled.

## Changes from diagnostic 01

The supplied camera photo showed active style `00000021` and a User Def. 1
packet marked `bad size`. Its exact length and counts were unreadable because
the Canon ROM formatter printed `%u` as a literal `u`. Diagnostic 02 uses
supported signed decimal formatting for these fields. Raw prefix capture is
bounded by both the delivered length and a 32-byte limit; no parameter decoding
is enabled for unexpected lengths. All original packets are left unchanged.
Host tests cover the formatter limitation and short/long/raw packet bounds.

Update the working card binary as above; **do not run an installer again**.
Return a full photo of the diagnostic 02 screen with the Canon settings unchanged.
