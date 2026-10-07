# Epson_scanner

Command-line scanning scripts for an Epson Perfection V350 Photo (GT-F720) on
Linux, using SANE's `scanimage` with Epson's `epkowa` backend (the one iscan uses).

## scanpages: paper pages to a searchable PDF

```
scanpages [options] name      # -> name.pdf, page images in name_pages/
```

Scans a series of pages on the flatbed; press Enter before each page and Ctrl-D
to finish. The pages are kept as lossless PNGs, packed into a compact PDF
(JPEG via `img2pdf`), and given a text layer with `ocrmypdf` (tesseract), which
also deskews and turns upside-down pages.

| Option | Meaning |
|---|---|
| `-r dpi` | resolution, default 300 |
| `-c` | color (default gray) |
| `-a area` | `Letter`, `A4` or `Maximum` (default Letter) |
| `-L lang` | OCR language(s), e.g. `eng+deu` (default eng) |
| `-n` | no OCR, image-only PDF |
| `-b N` | rescan only page N |
| `-s` | rebuild the PDF from the existing `name_pages/*.png` |

Running `scanpages name` again on an existing series continues where it left off.

## scanslides: mounted 35 mm slides in the 1x4 holder

```
scanslides [options] name     # -> name_1.tif ... name_4.tif
```

Makes a quick 300 dpi overview of the transparency strip, finds which of the
four holder windows hold a slide and where each mount's image opening is, then
scans just that opening at high resolution (default 2400 dpi), leaving the
mount out. Options: `-r` resolution, `-d 16` for 16 bit, `-j` JPEG, `-s 1,3`
slot selection, `-R 90|180|270` rotate, `-M` mirror, `-k` keep the overview.
`scanslides -h` lists them all.

The holder geometry is calibrated from an empty-holder scan (window positions
at the top of the script). **Status:** tested with the empty holder only; not
yet tried with real slides. A mode for the 35 mm negative strip is still to come.

## Requirements

`sane-utils` (scanimage), Epson's `iscan` / `epkowa` backend for this scanner,
`imagemagick`, `ocrmypdf` (brings `img2pdf` and `tesseract-ocr`), Python 3 with
numpy and Pillow.

## Notes for this scanner

- Close iscan first; only one program can use the scanner.
- The SANE device name changes after a power cycle; the scripts find it themselves.
- The transparency unit lights a 33 x 244 mm strip and only scans in Color mode.
- If the scanner light blinks red after a transparency scan error, power-cycle it.
