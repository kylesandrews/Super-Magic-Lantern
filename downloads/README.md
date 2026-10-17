# FujikanonX Picture Style diagnostic 03

For an already-working FujikanonX card on the original Canon EOS 5D with
firmware 1.1.1. This new build has NOT been tested on hardware. Its observer
makes no settings writes; inherited Other Tools retain their existing powers.

## Update and first comparison

1. Extract the package on your computer. Run `python3 VERIFY.py` from the
   extracted top folder; all checksums must pass (177 files).
2. Power off and wait for card activity to finish. Back up the card's working
   root `autoexec.bin` and ML folder using the card reader.
3. Copy `candidate/autoexec.bin` to the card root, replacing that file only.
   Keep existing ML files/configuration. Do not run an installer or change
   camera boot flags. The package contains no FIR.
4. Eject the card, boot, press DELETE/TRASH to open the firmware menu, and
   acknowledge any beta warning. From Film recipes, MENU → Other Tools →
   Prefs → **Picture Style check**. Open the action with the native controls.
5. Photograph the complete **Picture Style check 03** screen and send it back.
   Leave User Def. 1 settings unchanged for this first comparison: Canon's
   previous screen showed sharpness 3 and contrast/saturation/color tone 0.
6. MENU returns to Prefs; half-shutter exits to shooting. If a hang occurs,
   remove the battery promptly and restore your exact backed-up working binary
   and configuration on the computer. Do not delete autoexec.bin as recovery.

## What changed

The uploaded Canon firmware package was extracted as data, without running its
Windows executable. It identifies version 1.1.1 and its decoded checksum
passes. Offline analysis of Canon's GUI dispatcher identifies separate
four-byte properties for User Def. 1:

| Field | Property |
|---|---|
| Contrast | 02060019 |
| Sharpness | 0206001a |
| Saturation | 0206001b |
| Color tone | 0206001c |

Diagnostic 02 incorrectly expected one 24-byte property inherited from newer
camera layouts. Its subscribed 02060007 is not the User Def. 1 parameter block.
Diagnostic 03 adds four narrow subscriptions, using the existing property
registration mechanism. It adds no polling, memory hooks or property writes.
All nine inherited cache copies are now guarded against incorrect lengths.
Canon's uploaded firmware itself is not included or redistributed.

The display shows the active style code plus each scalar's signed decimal
value, raw hex, observed length and event count. `waiting` means no packet has
arrived since boot. `unexpected size` means a packet arrived with a length
other than four; no value is decoded from it. Captured values are the last
received messages, not an independently polled current-state guarantee.

These IDs are supported by offline disassembly, but readings and value ranges
still need camera comparison. Receiving numbers does not validate writes,
acknowledgments, persistence or the effect on photographs. No recipes are
applied and JPEG-only capture is not enforced.

After the first comparison, a separate manual 3→4 sharpness change through
Canon's menu can test whether just the sharpness value and event count change.
Leave all settings unchanged until that first screenshot is reviewed.
