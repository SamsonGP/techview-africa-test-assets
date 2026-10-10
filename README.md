# TechView Africa — Synthetic PDF Compression Test Pack (v1)

This repository is a stable public download location for one synthetic test pack,
published as an unchanged ZIP release asset. It contains only this test pack and
this README. It is **not** part of the TechView Africa application codebase and
holds no source code, configuration, credentials or private content.

The pack is a set of **fictional** test fixtures created to explore how PDF
compression presets change file size and small-text legibility. Every name,
amount, reference and stamp graphic is invented. These are not official or real
documents.

## Download

`TechView-Africa-PDF-Compression-Test-Pack.zip` — attached unchanged to the
release `pdf-compression-test-pack-v1`.

- Size: 20,835,661 bytes
- SHA-256: `49fcba72c8fd953651275b49878d2d48f31947eb5ebbdfa3ac8f8c40ac89d0d2`

Verify after download:

```sh
shasum -a 256 TechView-Africa-PDF-Compression-Test-Pack.zip
```

## Contents

The archive holds four A4 PDFs (three pages each), per-page inspection renders
at two widths, a `results.json` measurement file, a `run_test.py` reproduction
script and a `README.md`. The four PDFs:

| File | Bytes | Pages | Description |
|---|---:|---:|---|
| `01-source-text.pdf` | 4,754 | 3 | ReportLab digital text, standard Helvetica |
| `02-simulated-scan-300dpi.pdf` | 16,678,301 | 3 | 300 DPI colour raster + deterministic mild noise, lossless |
| `03-ebook-150dpi.pdf` | 301,366 | 3 | Ghostscript `/ebook` output from the scan |
| `04-screen-72dpi.pdf` | 100,590 | 3 | Ghostscript `/screen` output from the same scan |

One decimal MB = 1,000,000 bytes. Each page carries 6-, 8-, 10- and 12-point
text, mixed letters/digits, a fictional amount, faint text and a blue sample
stamp; page headings differ.

## Methodology

1. Generate three A4 pages as selectable digital text (ReportLab).
2. Render each page at 300 DPI (Poppler `pdftoppm`) to 2481 × 3508 RGB pixels.
3. Add deterministic per-pixel noise (−4 to 0, shared across channels, NumPy
   `default_rng` seeds 101/102/103) to make a simulated scan, then place each
   image on an A4 PDF page with lossless encoding (ReportLab `drawImage`).
4. Run each Ghostscript preset independently against the scan: `/ebook` and
   `/screen`. Output image dimensions were checked — 1240 × 1754 for `/ebook`
   and 595 × 841 for `/screen` (about 150 and 72 DPI).
5. Record byte counts, SHA-256 hashes and page counts in `results.json`.
6. Render all four PDFs at 390 px and 1170 px (3×) widths for inspection.

Software at generation time: Ghostscript 10.02.1, Poppler/pdftoppm 26.05.0,
ReportLab 4.4.9, Pillow 12.3.0, NumPy 2.3.5, pypdf, on Linux.

Portable reproduction on the included scan (install Ghostscript first, run in a
**new** directory so the supplied files are not overwritten):

```sh
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/ebook \
   -dNOPAUSE -dQUIET -dBATCH -sOutputFile=ebook-reproduced.pdf 02-simulated-scan-300dpi.pdf

gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen \
   -dNOPAUSE -dQUIET -dBATCH -sOutputFile=screen-reproduced.pdf 02-simulated-scan-300dpi.pdf
```

`run_test.py` rebuilds the whole fixture from scratch; it needs the libraries
listed above. Different software or font versions, PDF metadata and IDs can
change hashes and byte counts.

## Limitations

- **Synthetic.** This is an automated comparison with visual spot-checks, not a
  test of a real scanner, physical phone, upload portal or any specific
  consumer app.
- **One controlled document.** Three similar generated pages, not independent
  real-world samples. No photography, handwriting, official seals, barcodes,
  forms or digital signatures were assessed.
- **Simulated scan.** The noise is generated, not measured from a scanner.
- **Illustrative renders.** The PNGs are narrow-screen and zoom simulations, not
  a specific device's browser, viewer, screen density or usability test.
- **No readability score.** 8-point text stayed readable-but-softened in `/ebook`
  and visibly degraded in `/screen`; the 6-point case is deliberately demanding.
  The smallest text was not comfortably readable at full-page 390 px width in any
  version — zoom was needed. No universal readability score or OCR accuracy claim.
- **Size reduction is not general.** A simple, losslessly encoded scan compresses
  a lot; already-optimised scans, photographs, long documents and other inputs
  may behave very differently.
- **No upload attempted.** No portal submission was performed, and the local
  compression commands never sent these files to any online service.

## Attribution

TechView Africa synthetic test, generated with code. All content is fictional.
