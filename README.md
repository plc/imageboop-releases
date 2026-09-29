# Imageboop

A native macOS app for resizing and converting images in bulk. Drop photos on the window, pick a
size and a format, press Process.

**[Download the latest release](https://github.com/plc/imageboop-releases/releases/latest)** —
signed and notarised, so it opens without the right-click dance. macOS 14 or later; universal.

## What it does

```
drop photos            →   pick size + format   →   Process
(or a whole folder)        Fit within 2000px        beside the originals
                           PNG
```

- **Reads what your camera and phone produce.** HEIC, PNG, JPEG, TIFF, GIF, WebP, PSD, BMP, and
  the RAW formats from Nikon, Canon, Sony, Fujifilm and Panasonic. Anything macOS can open.
- **Writes** PNG, JPEG, HEIC, TIFF and AVIF — or the format the file already was.
- **Respects rotation.** A photo taken sideways on a phone comes out the right way up, and is
  sized by how it looks rather than how it is stored.
- **Keeps the camera data.** Exposure, lens, date and location survive into the new file.
- **Never overwrites anything.** Run the same batch twice at different sizes and you get
  `photo.jpg` and `photo-1.jpg`, not one file quietly replaced.

## Size

Every mode keeps the aspect ratio. Nothing is cropped or stretched.

| Mode | What it means |
| --- | --- |
| **Fit within** | Neither edge exceeds the box. 2000 × 2000 over a 4:3 photo gives 2000 × 1500. |
| **Width** | That many pixels wide, height follows. |
| **Height** | That many pixels tall, width follows. |
| **Scale** | A percentage of the original. |
| **Original size** | No resize — just a format change. |

**Enlarge smaller images** is off by default: scaling a 400px image up to 2000px makes a soft
file that is bigger on disk and better in no way.

The **Output** line under the controls shows what the current settings will do — before and
after — and updates as you change them.

## Where output goes

| | |
| --- | --- |
| **Same folder as the original** | The default. |
| **In a subfolder** | Beside each original — `processed` unless you rename it. |
| **Choose a folder…** | One folder for everything. |

## Installing

1. Download the zip from the [releases page](https://github.com/plc/imageboop-releases/releases/latest).
2. Drag `Imageboop.app` to `/Applications`.

Builds are signed with a Developer ID and notarised by Apple, so they open normally.

## Updates

The app checks here for a newer release on launch, at most once a day, and shows a bar at the
top of the window when there is one. **Check for Updates…** in the Imageboop menu forces a
check. It is a notification, not a self-installing updater.

## Notes

`CHANGELOG.md` records what changed in each release.

Metadata, including GPS, is carried across into processed files. If you are publishing photos
and would rather not share where they were taken, strip it separately.

This repository holds the built app, this README and the changelog. The source is private.
Issues and feature requests are welcome here.
