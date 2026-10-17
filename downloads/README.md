# FujikanonX JPEG quality write test 07

Original Canon EOS 5D / firmware 1.1.1, with an already-working FujikanonX card.
**New build, not yet hardware-tested.** Adds one explicitly confirmed test:
**Large/Fine JPEG (3) → Large/Normal JPEG (2) → Large/Fine JPEG (3)**.
It never requests RAW, another quality value, another Picture Style slot,
white balance, or recipe application. This does not yet enforce JPEG-only mode.

## Install and establish the starting state

1. Extract the ZIP on your computer and run `python3 VERIFY.py` in its top
   folder. All **177 files** must verify successfully.
2. In Canon's native menu restore **Large/Fine JPEG only**, without RAW.
   Select User Def. 1 with sharpness **3**, contrast/saturation/color tone **0**.
   These known baseline values are required; the test does not change them.
3. Power off, wait for card activity to finish, and insert the CF card in your
   computer's reader. Back up root autoexec.bin, ML support files and config.
4. Replace only root `autoexec.bin` with `candidate/autoexec.bin` from this ZIP.
   Keep existing ML files/configuration; eject and boot. No FIR, new installation
   or camera boot-flag change is involved.
5. Under Other Tools → Prefs, open **JPEG quality check** and confirm raw
   **00000003**, length 4. The read-only screen now identifies itself as 07
   and labels only the two modes observed on this camera. Other codes remain
   unverified. Picture Style check 03 remains available for the baseline values.

## Perform one manual round trip

1. Open Other Tools → Prefs → **JPEG quality write test**.
2. Read its confirmation screen. MENU cancels without writes. Press **SET**
   once to run. The firmware menu closes.
3. Leave shutter, mode dial, power switch and other controls untouched for
   about five seconds. Do not capture or open menus while it is running.
4. Reopen **JPEG quality write test** and photograph the result.
5. Independently open Canon's native Quality menu: it must again show
   **Large/Fine JPEG only**, without RAW. Photograph that screen too.
6. Only after PASS and the Canon comparison, take one new photo. Note its file
   number. After powering off and waiting for card activity to end, inspect
   the shot on your computer: the new `.JPG` should have **no `.CR2` with the
   same base filename**. Ignore unrelated older RAW files; do not delete them.
7. Send the two result/menu photos and report that shot's filenames.

No need to repeat the four parameter write tests. They remain available but
are not triggered by the quality test. Each control allows one attempt per
boot, transactions cannot overlap, and any failure blocks later tests in that
boot. Do not reboot merely to retry a failure; report it first.

## Interpreting results

- **PASS — change and restore observed**: fresh events reported quality 2 and
  then quality 3. Independently verify Canon and the new capture files.
- **REFUSED — no writes attempted**: starting quality, baseline Picture Style,
  observed packet sizes/data, or shooting-idle conditions were not as required.
- **FAIL — test failed; original restored**: the change acknowledgment was
  missing, but restoration to 3 received a fresh acknowledgment. Stop and report.
- **RESTORE UNCONFIRMED — check Canon**: a restoration acknowledgment was not
  established, or unsafe/unexpected camera state prevented restoration. Manually
  select **Large/Fine JPEG only** in Canon's menu, then stop and report it.
- **BLOCKED**: an earlier test failed during this boot. No new transaction starts.
- **Task creation failed**: no test writes were launched. Stop and report it.

If the camera hangs, remove the battery promptly, then use the computer to
restore the backed-up working binary/configuration. Do not delete autoexec.bin.
Replacing a binary does not undo settings saved by Canon: independently restore
Large/Fine JPEG and the known Picture Style baseline through Canon's menu.

## Evidence and limits

The read-only comparison on this camera established quality property
**80000016**, four-byte value **3** for Large/Fine, with a user-confirmed new
JPG and no matching CR2. A separate Canon menu and diagnostic comparison
established value **2** for Large/Normal. Normal-mode file output has not been
independently inspected. These observations support this narrow test, not an
assumption about RAW modes or other models' quality constants.

Only permanent four-byte payloads **2** and **3** are allowed for this property.
The selected task uses the existing property wrapper, checks shooting idle and
known state, requires fresh event counts, and attempts restoration after a
failed change when the camera is safe. It will not write blindly after unknown
state, manual conflicts or continued activity. These checks are not an atomic
shutter lock, and the ROM call/task scheduler are outside portable wait bounds.
At most two requests are sent. Keep the camera untouched during the transaction.

Host tests cover the complete quality round trip, no/missing/stale events,
restoration failure, bad starting quality/packet lengths, context changes,
manual conflicts, exact property/value/length restrictions, explicit launch,
one attempt and concurrency/failure lockout. Compilation and host tests do not
replace this camera trial. Recipe effects and automatic quality selection
remain disabled until their own application path is implemented and tested.
