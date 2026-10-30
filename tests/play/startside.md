The `startside-*` replays check the patches Startside carries on top of an
upstream release (its classic Mac widget). Each needs a disk that is not in
this repository: someone else's software, used for testing only.

| Script | Disk | SHA-256 |
| --- | --- | --- |
| `startside-simcity-code-prompt.json` | SimCity 1.11 HFS image, `SimCity.img` | `7c599c138febe6e526bcc2ecd16e2bf15c9851aa7da0f41cc2b5a0da547e0403` |
| `startside-shufflepuck.json` | Shufflepuck Café disk image, `shufflepuck_cafe.dsk` | `a3331c320b7e9d3d2398c864c9441bdc8d50b035efe0c0e5fa40db6ba1379c05` |
| `startside-macpaint-1.5.json` | MacPaint 1.5 400K MFS floppy, `Paint_2.dsk` | `735b0f5c557937f7177ef64b94d85b702074ef59d71f67c6a0c95c099d99e483` |
| `startside-macpaint-2.0-personalize.json` | MacPaint 2.0 MacBinary, `MacPaint 2.0.bin` | `1164fa809b3061fab64c213c3ee0b8fdef94b20b255c0d8f78d4725a2f7e658f` |
| `startside-macwrite-1.1.json` | MacWrite 1.1 StuffIt archive of an MFS floppy, `1.1 Write.sit` | `659ee4c47d1ec7a4291f8c1ee7ec59814259224e8cf50a6665141d3fc58779ab` |
| `startside-macpaint-save-name.json` | Gryphel's MacPaint 1.3 gry2 disk (see `macpaint.md`) | `82d534062c5db02f873de7e8003a94159097fe30e2c76dff699cf1130a54ae2b` |
| `startside-macpaint-save-folder.json` | the same | the same |

Run each on a fresh copy of its disk, as the MacPaint replays:

```sh
SYSTEMLESS_SCREEN_WIDTH=512 SYSTEMLESS_SCREEN_HEIGHT=342 SYSTEMLESS_RAM_SIZE=4194304 \
  target/ci-test/systemless SimCity.img --headless \
  --addressing-24-bit --screen-depth 1 \
  --play-script tests/play/startside-simcity-code-prompt.json \
  --play-output /tmp/startside-simcity
```

What each checks:

- SimCity: the code prompt after Dullsville's introduction drawn over the city
  (its window in front of the map), its code marks solid (an enlarged 1-bit
  BitsRect), the population field's three-pixel frame before and after typing
  (TextEdit erases only its lines), and the classic scroll bar's outline arrow.
  The city behind the prompt needs the code card's answer, which is not in the
  repository, so its title bar after a resize, budget arrows and zoning square
  are not replayed.
- Shufflepuck Café: the title, the bar's sound, the table. The queued Sound
  Driver writes (the music rather than a 60 Hz buzz) are checked by
  `legacy_sound_driver_async_writes_play_in_turn`: the play format counts
  non-silent samples and cannot tell the two apart.
- MacPaint 1.5: the MFS floppy loads (with its own System file) and the brush
  paints.
- MacPaint 2.0: typing into the personalize dialog's disabled Name field.
- MacWrite 1.1: the StuffIt archive's MFS floppy loads and typing draws.
- MacPaint 1.3 Save: the name's selection stops at its last character, a click
  places the insertion point, typing inserts there, and a drag selects.
- MacPaint 1.3 Save folder: in the `obj` folder, a press on the folder pop-up
  opens a menu of the path (obj, then the disk), the row under the pointer
  highlighted; released on the disk, the dialog lists the disk's top.

Tetris's window over the menu bar depends on a flag only an embedder sets
(`menu_bar_clips_windows`); the command line does not, so it is not replayed.
