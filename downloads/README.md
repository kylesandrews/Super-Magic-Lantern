# FujikanonX parameter write tests 05

Target: original Canon EOS 5D, Canon firmware 1.1.1, with a working FujikanonX
card. **This new build has not yet been tested on hardware.** It adds three
manual parameter write-and-restore actions. No test runs on startup or recipe
selection. Recipe application and automatic JPEG-only enforcement remain off.

## Install the update

1. Extract this ZIP on your computer and run `python3 VERIFY.py` from its top
   folder. Expect **177 files verified**; do not proceed if any check fails.
2. In Canon's menu, select User Def. 1 based on Standard. Confirm sharpness
   **3**, contrast **0**, saturation **0**, color tone **0**.
3. Power off, wait for card activity to stop and insert the CF card in the
   reader. Back up its working root `autoexec.bin` and ML folder/configuration.
4. Replace only the root `autoexec.bin` with `candidate/autoexec.bin` from this
   package. Keep existing ML support files and configuration. Eject, then boot.
   Do not run a FIR, re-install Magic Lantern, or change camera boot flags.
5. Open Other Tools → Prefs → Picture Style check (read-only check 03).
   Confirm active raw 00000021 and the four baseline values above.

## Run one test at a time

Use **Other Tools → Prefs**. Each action opens a confirmation page and needs
its own **SET** press. Opening a page alone does not write; MENU cancels.

| Run in this order | Menu action | Requested round trip | Final Canon value |
|---|---|---|---|
| 1 | Contrast write test | 0 → −1 → 0 | Contrast 0 |
| 2 | Saturation write test | 0 → −1 → 0 | Saturation 0 |
| 3 | Color tone write test | 0 → +1 → 0 | Color tone 0 |

For **each** test:

1. Open its action, read the confirmation page, and press SET once.
2. The firmware menu closes. Leave the shutter and other controls untouched
   for about five seconds. Do not capture, change modes or open menus during
   this interval. Software context checks are not an atomic shutter lock.
3. Reopen **the same action** and photograph its result. PASS means a fresh
   observer event reported the changed value and another reported restoration.
4. Independently check Canon's native User Def. 1 editor: the tested control
   must be back at **0**, sharpness **3**, and the other controls **0**.
5. Continue to the next test only after PASS and the native Canon comparison.
   Send the three result photos and confirm the final Canon values.

You may perform these three separate tests in the same boot. Each parameter
allows only one attempt per boot; duplicate SET presses cannot re-run it, and
transactions cannot run concurrently. The previously validated Sharpness
write test remains available but does not need to be repeated.

## If anything fails

- **REFUSED — no writes attempted**: baseline values/readback or shooting-idle
  conditions were missing. Stop and report the screen.
- **FAIL — test failed; original restored**: change acknowledgment failed but
  a fresh restoration event arrived. Check Canon, stop and report it.
- **RESTORE UNCONFIRMED — check Canon**: restoration was not established.
  Restore the tested control to **0** using Canon's menu (or sharpness **3**
  if you ran that test). Check all baseline values, stop and report it.
- **BLOCKED — earlier test failed**: another test already failed in this boot;
  no additional transaction will start. Stop rather than rebooting to retry.
- **Task creation failed — no writes**: the test task did not start. Report it.

If the camera hangs, remove the battery promptly. Restore the backed-up
working binary/configuration on the computer. Do not delete autoexec.bin.
A binary rollback does not undo settings already saved by Canon: restore the
four baseline values through Canon's own menu as well.

## Exact scope and validation limits

The new actions whitelist these User Def. 1 properties and permanent four-byte
payloads: 02060019 contrast (0/ffffffff), 0206001b saturation (0/ffffffff),
0206001c color tone (0/1). Existing sharpness testing remains 0206001a (3/4).
Signed -1 is represented as ffffffff. No other value, slot, style-selection,
white-balance or image-quality write is added. Only an explicitly confirmed
single parameter is changed, then restored; no multi-parameter sequence is
automatically launched.

All four baseline observations must be valid. The selected parameter must
start at its baseline; all other parameters must retain theirs. Every
acknowledgment requires a new event count and the requested value. Missing
messages cannot pass using stale cached state. Active style, capture/menu/
playback state, half-shutter, sensor cleaning, job state and shutdown are
checked. Unknown/missing data, an unexpected value or continued activity
prevents blind restoration and produces RESTORE UNCONFIRMED. Fresh values
that match a request do not prove the event's origin under concurrent manual
activity; keep the camera untouched.

The portable waits are bounded. Scheduling and the existing Canon ROM request
function are outside that timing bound. The action makes at most two requests
and does not blindly retry after unconfirmed restoration. Any non-PASS result
blocks further tests until the next boot. It does not persist a failure log
across boots; retain result photographs.

Host sanitizer tests cover all four transactions under normal/failure/stale-
event/interruption/conflict conditions, camera-adapter target/value/size
restrictions, concurrent-start refusal, independent attempt tracking and
failure lockout. Tests and compilation do not substitute for camera results.
