# Wada Sanzō Colour Guide

An interactive web tool for exploring colour combinations from Wada Sanzō's *Color Schemes* (配色総鑑, 1934).

## Features

- **Colour library** — all colours rendered as swatches with name, hex, and combination count
- **Versatility ranking** — colours scored by the quality of their neighbourhood in the combination graph, not just how many combinations they appear in
- **Combination browser** — every dyad, triad, and tetrad a colour belongs to, shown as swatch strips
- **Close-match alternatives** — perceptual neighbours (ΔE 5–30 in CIELAB) for any colour
- **Sort & filter** — by versatility score, connection count, or alphabetically; filter by combination type or hue family

## Running locally

Requires a local HTTP server (fetch won't work over `file://`).

```bash
# Python
python -m http.server 8000

# Node
npx serve .
```

Then open [http://localhost:8000](http://localhost:8000).

## Data

`colors.json` contains 160+ colours sourced from Wada's combinations, each with RGB/CMYK values and a list of combination IDs. Combination membership, types (dyad/triad/tetrad), and versatility scores are all derived client-side at load time.

## Versatility Score

```
score(C) = ( Σ use_count(c) for c in compatible_colours(C) ) / |compatible_colours(C)|
```

Where `compatible_colours(C)` is the union of all colours that share a combination with C, excluding C itself. This rewards colours whose partners are themselves highly connected, and penalises colours whose partners are dead ends.

## Background

Wada Sanzō (1883–1967) was a Japanese painter and colour theorist who founded the Japan Standard Color Association and created the first systematic mass-produced analysis of colour pairings in Japan. His eleven-volume *Color Schemes* series catalogued dyads, triads, and tetrads with bilingual (English/kanji/hiragana) descriptions.
