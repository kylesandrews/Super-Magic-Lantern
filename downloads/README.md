# FujikanonX Sharpness write test 04

Original Canon EOS 5D / Canon firmware 1.1.1, with an already-working Beta 4 /
FujikanonX card. **This new build is not yet hardware-tested.** Unlike the prior
read-only diagnostic, it adds a manual action that writes one camera setting.
Recipes remain preview-only; image quality and all other parameters are untouched
by this test. The inherited Other Tools still have their original behavior.

## Before running

1. Extract the ZIP and run `python3 VERIFY.py` in its top folder; all 177
   checks must pass. Back up the working card binary, ML folder and configuration.
2. In Canon's menu select User Def. 1, based on Standard. Set sharpness **3**,
   contrast **0**, saturation **0**, and color tone **0**. The action refuses
   to run if the observer reports different parameter values or missing data.
3. Power off, wait for card activity to finish, and use the card reader to
   replace only the root `autoexec.bin` with `candidate/autoexec.bin` from this
   package. Keep existing support files/configuration. No FIR or boot-flag
   change is involved. Eject the card and boot.
4. Open Other Tools → Prefs → Picture Style check. The read-only observer
   still identifies itself as check 03. Confirm active raw 00000021, sharpness
   3 and the other three controls 0. If different or waiting, stop and report it.

## Run exactly once

1. Open Other Tools → Prefs → **Sharpness write test**. Opening the action
   displays a confirmation screen; it does not write anything.
2. Read the screen. MENU cancels. To run, press **SET** once.
3. The firmware menu closes. Keep the shutter and all controls untouched;
   do not take a photograph or change camera mode during this short test.
   Wait about five seconds for completion. The action runs in a separate task.
4. Reopen Other Tools → Prefs → **Sharpness write test** and photograph the
   result screen. Repeated SET presses cannot rerun it during the same boot.
5. Independently check Canon's User Def. 1 editor: sharpness must be back at
   **3**. Send the result photo and confirm Canon's value. A read-only diagnostic
   screenshot after the test is also useful.

## Results

- **PASS — saw 4, then restored 3**: fresh observer events reported both
  requested values. This validates only this narrow round trip, subject to
  the independent Canon menu comparison. It does not validate other values,
  other write properties, JPEG effects or the production recipe backend.
- **REFUSED — no writes attempted**: wrong initial values, missing observer
  data, or no safe shooting-idle window. Check Canon's values and report it.
- **FAIL — test failed; restored 3**: the 4 acknowledgment was not established,
  but a fresh 3 acknowledgment was observed. Stop and report the result.
- **RESTORE UNCONFIRMED — check Canon**: restoration could not be established.
  Open Canon's menu and manually restore sharpness to **3**; also confirm the
  other three controls are 0. Stop testing. Do not assume it is restored.
- **Task creation failed — no writes**: the test task did not start. Report it.

If the camera hangs, remove the battery promptly. Restore the previously
working binary/configuration on the computer. A binary rollback alone does
not reverse settings already saved by Canon: independently restore sharpness
3 through Canon's own menu. Never delete autoexec.bin as a recovery method.

## Scope and limits

The only new write target is **0206001a**, User Def. 1 sharpness, and its only
payloads are 32-bit **4** followed by **3**. Permanent separate payload storage
avoids transient stack buffers. No setting writes run at startup or on recipe
selection. The action allows one attempt per boot and has a separate SET
confirmation. It uses the existing Canon property-request wrapper, whose stub
address FFAB3D44 was checked against the supplied 1.1.1 image's function code.

Before writes and while waiting, it checks shooting idle, no firmware/native
menu, no playback/review, no half-shutter, no sensor cleaning, no reported capture
job and no shutdown request. These software checks are not an atomic shutter
lock; keep the camera untouched. The legacy ROM request itself and task scheduling
are outside the portable transaction's timing guarantees.

Readback requires a new event count and the requested value. An old cached
value alone cannot pass. Restoration is attempted after a failed change when
safe state returns, even if the change acknowledgment timed out. A changed
active style, changed other controls, unexpected sharpness, missing data or
continued camera activity prevents a blind restoration write and produces
RESTORE UNCONFIRMED instead. No extra retry is sent after an unconfirmed
restoration. Late/absent messages cannot be excluded by host tests; check Canon.

Host sanitizer tests cover the normal round trip, stale/missing events,
restore failure, interrupted activity, manual state conflicts, initial-value
rejections, explicit task launch, one attempt per boot, property/value/length
restrictions and task-creation failure. The ARM build and package checksum
checks are separate from the hardware test you are about to perform.
