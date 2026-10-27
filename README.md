# QR Code Generator · 二维码生成器

> Generate a QR code that doesn't look like every other QR code: 16 module styles, 4 corner styles, 5 frames, banners, a centre logo with automatic dot punching, gradients and L/M/Q/H error correction — export PNG at any size or vector SVG.

**English** | [简体中文](README.zh-CN.md)

<a href="https://bushimashen.github.io/qr-code/"><b>▶ Live demo</b></a>

## Why "style" is more than decoration

A QR code is a grid of dark and light modules. Anything you do to it is a trade against scanning robustness,
so this generator keeps a hard line between **the data pattern** and **the paint on top of it**:

- The module matrix comes straight from `qrcode-generator` and is never altered by styling.
- The three **finder patterns are always drawn complete and solid** (square, rounded or dot), whatever the
  module style — they're what a scanner locks onto first.
- Decorative frames and banners are drawn **outside the quiet zone**, never over the data.
- A centre logo punches out the modules underneath it, which is exactly what error correction is for —
  hence the hint to use **Q or H** when you add one.

## What you can control

| | |
|---|---|
| **Module style** | 16: square, rounded, dots, liquid, classy, ring, line, jitter, dsj, 2.5D, vline, diamond, plus, target, fade, randrect |
| **Corner style** | follow style / square / rounded / dot |
| **Frame** | none / thin line / rounded card / double line / dashed |
| **Banner** | none / top / bottom / both, with your own text |
| **Fill** | solid, linear gradient, radial gradient (two colours) |
| **Background** | any colour, or fully transparent |
| **Error correction** | L 7% / M 15% / Q 25% / H 30% |
| **Logo** | any image, size and padding adjustable, optional punching out the dots beneath |
| **Export** | PNG at 512 / 1024 / 2048, or vector SVG |

## Implementation notes

**The drawing is a list of primitive ops in module coordinates**, not direct canvas calls. Each style emits
`path` / `circle` / `poly` / `ring` / `rect` / `text` ops, and two separate backends consume them — one draws to
`<canvas>`, one serialises to `<svg>`. That's why the PNG and the SVG are guaranteed to look identical, and why
adding a new style is a single `case` in one switch.

**Random styles use a seeded PRNG.** `jitter`, `dsj` and `randrect` are random, but the seed is
`hash(styleId + seed)`, so the same content and settings always re-render the same code — otherwise your design
would change every time you touched a slider. The "reshuffle" button bumps the seed when you actually want a new roll.

**Chinese content needs the UTF-8 encoder.** `qrcode-generator` defaults to single-byte encoding; without
`qrcode.stringToBytes = qrcode.stringToBytesFuncs['UTF-8']`, Chinese text encodes into garbage. That one line is
the difference between a working code and a pretty-but-useless one.

## Usage

Download the folder and open `index.html` — the relative path to `vendor/` must stay intact.

```bash
python -m http.server 8000   # or serve it any way you like
```

## Please test-scan before printing

The preview is a rendered approximation of what a scanner sees, not a verification. Before you send a design to a
printer, scan it with two different phones — especially if you used a logo, a gradient with low contrast, or a very
dense style like `plus` / `line` at small physical sizes. Also: very long content forces a denser matrix, and dense
matrices tolerate styling far less.

## License

MIT for the page code — see [LICENSE](LICENSE).
`vendor/qrcode.js` is qrcode-generator 2.0.4 by Kazuhiko Arase, MIT — see [vendor/qrcode-NOTICE.txt](vendor/qrcode-NOTICE.txt).
