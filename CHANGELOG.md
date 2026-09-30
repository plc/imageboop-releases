# Changelog

All notable changes to Imageboop. Newest first. Versions are semver; every release is tagged
`vX.Y.Z` and published at
[plc/imageboop-releases](https://github.com/plc/imageboop-releases/releases).

## 0.4.0 — 2026-09-29

### Changed

The size controls are now a width, a height, a unit and a checkbox. There is no mode picker,
because which field you fill is what happens. Width alone resizes by width. Height alone resizes
by height. Fill both and the image fits inside them. Leave both blank and nothing is resized.

A blank field does not mean zero and it does not mean "original". It means no opinion, and the
other field decides. The greyed number sitting in a blank field is what that edge will actually
come out at.

This replaced five menu items that mostly described what the fields were already saying. Two of
them, "Fit within" and "Width", give identical answers on a landscape photo, so the menu
reliably produced the question it existed to answer.

Proportional is on by default. Turn it off and each edge stretches to its own number, which
distorts the picture. Internally a filled field is just a scale factor, so pixels, per cent, one
field, two fields, proportional and not all come out of one rule instead of a case each.

Every thumbnail now previews its own result, and updates as you type. One line cannot describe a
queue of different shapes. At 2000 wide a 4284 × 5712 portrait becomes 2000 × 2667 while a
2454 × 1288 landscape becomes 2000 × 1050, and the old line quoted the largest image, which
described neither of the others. An image already under the target shows no arrow, because
nothing about it will change.

Changing any setting after a run puts the tiles back to previewing. Otherwise they sit there
showing what the last run produced under rules that no longer apply.

The aggregate Output row and the running "N images · N MB" tally are gone. The grid was showing
both already.

### Fixed

Dimensions were being truncated into nonsense. The thumbnail caption truncated in the middle,
which is correct for a filename and ruinous for a pair of dimensions: "2454 × 1288 → 2000 ×
1050" came out as "2454 × 128…00 × 1050", which reads as a corrupted number rather than a
shortened line.

A zero or negative size in a hand-edited settings file clamped up to 1px and produced a
one-pixel image. Nonsense now declines to resize instead of destroying the picture.

### Note

Size settings reset once on first launch. The stored format changed shape, and inheriting half
of it would be worse than starting from the defaults.

## 0.3.0 — 2026-09-29

### Changed

The window is genuinely translucent now. It used to be a painted gradient. Liquid Glass only
refracts what is behind it *inside* the window, so the control bar was faithfully refracting a
static picture and looked exactly like a flat panel. The window now blends with what is behind
it, which is what makes the glass on top of it read as glass.

Process is called Boop.

Less copy. Gone: the subtitle under "Drop images here", "Results sit next to each original", the
settings restated underneath the button that sets them, and "No images yet" next to a disabled
button on a window already saying "Drop images here".

The Output line reports the output. The figure on the right was the total size going in, which
the status line already carried. It is now the size that came out, with the saving next to it.

Clear and Boop are the same height. `.glass` and `.glassProminent` do not pad their labels
identically, so the primary button rendered shorter than the one beside it.

The size modes explain themselves on hover, with worked examples.

## 0.2.0 — 2026-09-29

### Added

Help > Send App Feedback… files a GitHub issue against the app, with your app and macOS versions
filled in.

Send posts it without leaving the app, through Imageboop's own GitHub account, so your name is
not attached. Open in Browser files it as you instead, needs no setup, and works whether or not
you trust the first option. Use My Own Account… takes a token of your own, kept in your login
Keychain, if you would rather issues appeared under your name; yours overrides the shipped one.

The app carries a token so Send works out of the box. That token can create issues on the public
downloads repository and do nothing else, which was established by probing the API rather than
assumed.

## 0.1.0 — 2026-09-29

First build. Drop images on a window, pick a size and a format, get them back in a `processed`
folder beside the originals.

### Added

Bulk resize and convert. Reads everything macOS can open, including HEIC, PNG, JPEG, TIFF, GIF,
WebP, PSD and the RAW formats from the major camera makers. Writes PNG, JPEG, HEIC, TIFF or
AVIF, or whatever the file already was.

Five size modes, all preserving aspect ratio: fit within a box, exact width, exact height, a
percentage, or no resize at all. A px / % menu beside the numbers switches between pixels and
scaling, and stays in sync with the mode.

An Output line under the controls showing what the current settings will do, using the largest
image in the queue, and the real bytes written once a run has finished.

Folder drops, walked in full. Any `processed` folder found on the way is skipped, so re-dropping
a folder does not process its own results.

Three destinations: the same folder as the originals by default, a named subfolder beside them,
or one folder of your choosing for everything.

A queue you can rearrange. Drag a tile and the others shuffle aside to open a gap where it would
land; let go and it takes that slot. Order is processing order. Hovering a tile offers a ✕ and a
button that shows the original in Finder, both also on the right-click menu.

Concurrent processing bounded at cores-minus-one, with per-image progress in the grid and a
Cancel that never leaves a half-written file.

Liquid Glass chrome on macOS 26 and later, materials before that.

Update checking, once a day, as a banner rather than a self-installing updater.

### Why it works this way

Nothing is ever overwritten. A destination that already exists gets `-1`, `-2`. Running the same
batch twice at two sizes is an ordinary thing to do, and the version that silently replaced the
first run's output would be destroying work with no undo. The cost is a worse filename. The
alternative is a lost file.

Output goes beside each original rather than into one place. Images dropped from three folders
produce three `processed` folders. A central output directory is one more thing to go and find.

Enlarging is off by default. Scaling a 400px image up to 2000px makes a soft file that is larger
on disk and better in no way.

Orientation is resolved once, at decode, and the output is tagged upright. Sizing against the
stored rather than the displayed dimensions caps portrait photos on the wrong edge. Two tests
against real iPhone HEICs pin it.

Transparency is flattened onto white for JPEG, which has no alpha channel. CoreGraphics leaves
an untouched buffer at zero, so the default is black and a dropped-out logo comes back
unreadable.

Camera metadata is carried across, minus the tags describing the old pixels. An EXIF width of
6000 on a 2000px file is worse than an absent one. GPS comes across too, which the README says
plainly rather than deciding on anyone's behalf.

"Same as original" falls back to JPEG for formats macOS reads but cannot write, and says so in
the status line. Finding the wrong extension in Finder a week later is worse.

### Fixed during the build

Letters could be typed into the size fields. SwiftUI's `TextField(value:format:)` leaves the
binding at its old value when the parse fails, so the field showed text that did not match the
number behind it, and a width of `1` got committed without anyone typing it. The fields now hold
text and derive the number, filtering as you type and settling on blur.

The window accepted a drag of any file. `onDrop(of: [.fileURL])` matches the pasteboard's
declared type rather than the file, so a dragged movie lit the border up and then vanished on
release having done nothing. Drags are refused properly through AppKit now, and anything dropped
by name and skipped is reported.

`AppVersion`'s synthesised `==` contradicted its `<`, which made "1.2" and "1.2.0" different
releases.

The width field came up focused and stayed focused, so anything typed after adding images went
into it and overwrote the number already there. Nothing takes keyboard focus at rest now.

A cancelled "Choose a folder…" could persist across launches, bringing the app up with Process
disabled for a reason set in a previous session. It falls back to writing beside the originals,
as it does if a chosen folder has since been deleted or unmounted.

The grid hung the window when a tile was dragged. The frame reporter sat inside the offset it
was measuring, so moving a tile changed its measurement, which moved it again. No crash reports
exist for this because it was never a crash, just a beachball.

The first background wash was three saturated blobs and looked like a bruise.

### Versioning

`./VERSION` is the single source of truth. `Scripts/version.sh` syncs `Info.plist` from it and
refuses to move backwards. `swift test` fails if the two drift apart, if the version is not
semver, or if this file has no section for it. There is no CI, so the test is what actually
enforces it.
