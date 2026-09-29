# Changelog

All notable changes to Imageboop. Newest first. Versions are semver; every release is tagged
`vX.Y.Z` and published at
[plc/imageboop-releases](https://github.com/plc/imageboop-releases/releases).

## 0.1.0 — 2026-09-29

First build. Drop images on a window, pick a size and a format, get them back in a `processed`
folder beside the originals.

### Added

- **Bulk resize and convert.** Reads everything macOS can open — HEIC, PNG, JPEG, TIFF, GIF,
  WebP, PSD and the RAW formats from the major camera makers — and writes PNG, JPEG, HEIC, TIFF
  or AVIF, or whatever the file already was.
- **Five size modes**, all preserving aspect ratio: fit within a box, exact width, exact height,
  a percentage, or no resize at all. A **px / %** menu beside the numbers switches between
  pixels and scaling, and stays in sync with the mode.
- **An Output line under the controls** showing what the current settings will do — the largest
  image in the queue, before and after — updating as the controls move, and showing the real
  bytes written once a run has finished.
- **Folder drops**, walked in full, skipping any `processed` folder found on the way so
  re-dropping a folder does not process its own results.
- **Three destinations.** The same folder as the originals by default, a named subfolder beside
  them, or one folder of your choosing for everything.
- **A queue you can rearrange.** Drag a tile and the others shuffle aside to open a gap where
  it would land; let go and it takes that slot. Order is processing order. Hovering a tile
  offers a ✕ and a button that shows the original in Finder, both also on the right-click menu.
- Concurrent processing bounded at cores-minus-one, with per-image progress in the grid and a
  Cancel that never leaves a half-written file.
- Liquid Glass chrome on macOS 26 and later, materials before that.
- Update checking, once a day, as a banner rather than a self-installing updater.

### Decisions worth keeping

- **Nothing is ever overwritten.** A destination that exists gets `-1`, `-2`. Running the same
  batch twice at two sizes is an ordinary thing to do, and the version that silently replaced
  the first run's output would be destroying work with no undo. Some files to clean up is a
  worse filename; the alternative is a lost file.
- **Output goes beside each original, not into one place.** Images dropped from three folders
  produce three `processed` folders. A central output directory is one more thing to find.
- **Enlarging is off by default.** Scaling a 400px image up to 2000px makes a soft file that is
  larger on disk and better in no way.
- **Orientation is resolved once**, at decode, and the output is tagged upright. Sizing against
  the stored rather than the displayed dimensions caps portrait photos on the wrong edge; the
  fix is pinned by two tests against real iPhone HEICs.
- **Transparency is flattened onto white for JPEG**, which has no alpha channel. CoreGraphics
  leaves an untouched buffer at zero, so the default is black and a dropped-out logo comes back
  unreadable.
- **Camera metadata is carried across**, minus the tags describing the old pixels — an EXIF
  width of 6000 on a 2000px file is worse than an absent one. GPS comes across too; that is
  noted in the README rather than decided on anyone's behalf.
- **"Same as original" falls back to JPEG** for formats macOS reads but cannot write, and says
  so in the status line. Finding the wrong extension in Finder a week later is worse.

### Fixed during the build

- **Letters could be typed into the size fields.** SwiftUI's `TextField(value:format:)` leaves
  the binding at its old value when the parse fails, so the field showed text that did not match
  the number behind it — and a width of `1` got committed without anyone typing it. The fields
  now hold text and derive the number, filtering as you type and settling on blur.
- **The window accepted a drag of any file.** `onDrop(of: [.fileURL])` matches the pasteboard's
  declared type, not the file, so a dragged movie lit the border up and then vanished on release
  having done nothing. Drags are now refused properly through AppKit, and anything dropped by
  name and skipped is reported.
- **`AppVersion`'s synthesised `==` contradicted its `<`**, making "1.2" and "1.2.0" different
  releases.
- **The width field came up focused and stayed focused**, so anything typed after adding images
  went into it and overwrote the number already there. Nothing takes keyboard focus at rest now;
  the fields are for when you click them.
- **A cancelled "Choose a folder…" could persist across launches**, bringing the app up with
  Process disabled for a reason set in a previous session. It falls back to writing beside the
  originals, as it does if a chosen folder has since been deleted or unmounted.
- **The grid hung the window when a tile was dragged.** The frame reporter sat inside the offset
  it was measuring, so moving a tile changed its measurement, which moved it again. There were
  no crash reports because it was never a crash — just a beachball.
- The first background wash was three saturated blobs and looked like a bruise.

### Versioning

`./VERSION` is the single source of truth. `Scripts/version.sh` syncs `Info.plist` from it and
refuses to move backwards; `swift test` fails if the two drift apart, if the version is not
semver, or if this file has no section for it. There is no CI, so the test is what actually
enforces it.
