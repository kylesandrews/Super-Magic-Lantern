# FujikanonX JPEG look test 08

Original Canon EOS 5D / Canon firmware 1.1.1 with a working FujikanonX card.
**New build, not hardware-tested.** This is one experimental Canon JPEG look,
not a calibrated Fuji X Weekly or Shortstache recipe. The named recipe browser
and JUMP button still select previews; they do not apply named recipes.

This action changes the four already-tested User Def. 1 controls and leaves
them changed for a photograph. A separate explicit Restore action restores
the recorded baseline. **Restore before powering off:** Canon may persist the
settings, while the trial's snapshot/status and writer reservation exist only
in RAM. No automatic startup or shutdown setting changes are added.

## Install and establish baseline

1. Extract the ZIP and run `python3 VERIFY.py` from its top folder. All **177
   files** must verify successfully.
2. In Canon's menu manually select **User Def. 1 based on Standard**, with:
   contrast **0**, sharpness **3**, saturation **0**, color tone **0**.
   Set **Large/Fine JPEG only**, without RAW.
3. Power off, wait for card activity to finish, then back up the card's working
   root autoexec.bin, ML folder and configuration using the computer/card reader.
   Replace only root autoexec.bin with this package's candidate/autoexec.bin.
   Preserve the existing ML files, eject and boot. No FIR or boot flags involved.
4. Choose a stable colorful/textured scene. Keep lighting, framing, focus,
   exposure and white balance the same throughout; use fixed exposure/ISO and
   white balance rather than letting Auto change the comparison. Take a
   **baseline JPEG** and note its filename.
5. Optional read-only checks under Other Tools → Prefs: Picture Style check
   must show active 21 and 0/3/0/0; JPEG quality check must show raw 3.
   The firmware checks those observations but cannot verify that the User Def. 1
   base is Standard. The base is a manual precondition, not an asserted reading.

## Apply, photograph, and restore

1. Open **Other Tools → Prefs → JPEG look test**. It should say Not applied
   this boot. Opening it does not write anything. MENU cancels.
2. Press **SET** to apply. The firmware menu closes. Leave the shutter and
   all controls untouched until the completion notice, or wait at least ten
   seconds before reopening the menu. Do not capture during the transition.
3. Reopen JPEG look test. Continue only if it says **Applied — test photo
   permitted**, not Running, Refused, Failed, State changed or Restore unconfirmed.
4. Half-press to exit. Take **one JPEG of the same scene**, with the same
   exposure, white balance and framing as baseline. Note the filename.
   Do not change quality, Picture Style/base or its parameters while held.
5. Reopen JPEG look test and press **SET** again, now labeled **Restore original**.
   The menu closes. Leave controls untouched for completion or ten seconds.
6. Reopen JPEG look test and photograph its **RESTORED — original values
   observed** result. Independently check Canon's User Def. 1 detail page:
   contrast 0, sharpness 3, saturation 0, tone 0. Check Large/Fine JPEG too.
7. Take a third **restored-baseline JPEG** of the same scene, then power off
   normally and wait for card activity to finish before removing the card.
8. Inspect the three new files: all should be JPG, with no matching CR2 files.
   Send the original JPEG files as file uploads, their baseline/test/restored
   order, the result-screen photo, and confirmation of Canon's restored values.
   File uploads preserve metadata better than screenshots of image previews.

## Exact test values

| Control | Initial/restored | Held test value |
|---|---:|---:|
| User Def. 1 contrast | 0 | -1 |
| Sharpness | 3 | 4 |
| Saturation | 0 | -1 |
| Color tone | 0 | +1 |
| Image quality | Large/Fine (3) | Required unchanged; not written by this action |

Only these four properties and their two respective values are permitted by
the new adapter. No base-style, white-balance, quality, inactive-slot or recipe
writes are added by JPEG look test. Existing manual diagnostic test actions
remain in Other Tools but are blocked while this trial holds the writer
reservation. Original legacy tools are not globally disabled: do not use
unrelated setting-changing tools during this trial.

## Failures and recovery

- **REFUSED**: baseline/quality/active slot or a safe camera state was not observed;
  no trial writes were sent. Stop and report it.
- **FAILED — original values restored**: an application stage failed and automatic
  rollback received fresh acknowledgments. Verify Canon's values and stop.
- **RESTORE UNCONFIRMED** or **State changed**: do not assume the look or original
  values are active. Use Canon's native menu to restore **0/3/0/0**, select the
  Standard base in User Def. 1 and Large/Fine JPEG, and stop/report. The adapter
  refuses to overwrite unrelated unexpected values, a different slot or changed
  quality to force restoration.
- **Task failed**: Apply did not launch. No retry is provided in this boot.
- If restoration's task cannot start, the display reports RESTORE UNCONFIRMED;
  restore the baseline through Canon's menu.

If the camera hangs, remove the battery promptly and restore the working
binary/configuration on your computer. Do not delete autoexec.bin. A binary
rollback or power cycle **does not undo Canon's saved Picture Style values**;
restore the baseline manually. If you powered off while the look was held,
do the same manual restoration before further tests; the in-memory snapshot
cannot be recovered by rebooting. One Apply attempt is allowed per boot.

## Validation limits

Application serializes four writes, marking every attempted field for rollback
before sending it. Each success requires a fresh corresponding observer event
and requested value; it does not update the observer cache to fake a readback.
After all stages it checks all target values together, Fine JPEG and active
User Def. 1. Restore runs through attempted fields in reverse order, requiring
fresh acknowledgments and finally the complete baseline. Failures and manual
state conflicts produce explicit unconfirmed states instead of blind writes.
Software activity checks are not an atomic shutter lock; keep controls untouched
while each transaction runs. The ROM call/task scheduler are outside bounded
portable waits. The held look is not a capture interlock: manual quality or base
changes can change what Canon saves. Live UI checks cover observed scalars,
quality and active slot, not base selection or the photographed result.

Host sanitizer tests cover held state, reverse restoration, each partial-failure
stage, stale acknowledgments, activity/quality/manual conflicts, initial state
rejection, task creation failures, live-state checks and shared writer reservation.
The ARM build and package checks pass separately from this new camera trial.
JPEG output, actual appearance and restored-capture equivalence remain to be
checked from your photographs. No named film recipe has been calibrated yet.
