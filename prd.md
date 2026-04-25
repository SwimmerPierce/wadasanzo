# Product Requirements Document
## Wada Sanzo Colour Guide — Interactive Colour Combination Tool

---

## Overview

A web tool for designers and creatives to explore Wada Sanzo's historical colour combinations. The site surfaces all combinations from *Color Schemes* (配色総鑑, 1934), ranks colours by their network versatility, and lets users discover close-match alternatives to any chosen colour.

---

## Background

Wada Sanzō (1883–1967) was a Japanese painter and colour theorist who founded the Japan Standard Color Association and created the first systematic mass-produced analysis of colour pairings in Japan. His 11-volume *Color Schemes* series catalogued dyads, triads, and tetrads with bilingual (English/kanji/hiragana) descriptions. These combinations are celebrated for balancing boldness with subtlety and remain a foundational reference for modern designers.

The site is grounded in his philosophy: **a colour's value is only realised in relationship to other colours**, and colour choices should ask "what does this feel like?" rather than simply grabbing attention.

---

## Goals

1. Make Wada's full combination library browsable and searchable.
2. Surface the most *versatile* colours — those that belong to well-connected combinations, not merely many combinations.
3. Allow users to explore perceptual neighbours of any colour for close-match substitutions.

---

## Features

---

### 1. Colour & Combination Library

**Display all colours** sourced from Wada's combinations, each with:
- Colour swatch (accurate hex/RGB rendering of the historical colour)
- Original Japanese name (kanji + hiragana) and English name
- Combination type: dyad / triad / tetrad
- All combinations the colour appears in, displayed as grouped swatches

**Combination cards** show:
- The full palette swatch strip
- Combination type label
- Names of all member colours

Users can filter the library by:
- Combination size (dyad, triad, tetrad)
- Colour family / hue range
- Mood/tone tags (warm, cool, muted, vivid, earthy, etc.) derived from Wada's descriptions

---

### 2. Versatility Ranking

Colours are ranked by a **Versatility Score**, which measures the quality of a colour's neighbourhood in the combination graph — not just how many combinations it belongs to.

#### Formula

```
Versatility Score = average number of combinations that each of this colour's
                    compatible colours also belongs to
```

Formally, for colour *C* with compatible colour set *S*:

```
score(C) = ( Σ connections(c) for c in S ) / |S|
```

#### Rationale

This penalises colours whose partners are "dead ends" and rewards colours whose partners are themselves highly useful. A colour with 5 compatible colours each appearing in 10 combinations (score = 10) outranks a colour with 10 compatible colours each appearing in only 5 combinations (score = 5).

Tiebreaker: own connection count (higher wins).

#### Ranking display

- Ranked list / leaderboard view showing colour swatch, name, score, and connection count
- Sortable by: Versatility Score (default), own connection count, alphabetical
- Score displayed as a numeric value and a visual bar for quick scanning

---

### 3. Colour Detail Page

Clicking any colour opens a detail view with:

#### a. All Combinations
Full list of every combination this colour appears in, shown as swatch groups.

#### b. Close-Match Alternatives
For the selected colour, display neighbouring colours from the dataset that are **one or two perceptual steps away**, so users can find near-substitutes that preserve the mood of a combination.

- Nearness is measured in **CIELAB (ΔE)** — perceptual distance, not raw hex distance
- "One shade off" = ΔE roughly 5–15
- "Two shades off" = ΔE roughly 15–30
- Each close match links to its own detail page and shows which combinations it belongs to
- This lets a user ask: "I love this colour but it's slightly too warm — what's the closest Wada colour that's cooler?"

#### c. Versatility Score Breakdown
- Score value and rank out of total colours
- Bar chart of compatible colour connection counts so the distribution is visible

---

## User Flows

### Browse & Discover
1. Land on the ranked colour list (Versatility Score order).
2. Filter by hue family or combination type.
3. Click a colour to open its detail page.
4. Explore its combinations and close-match alternatives.

### Find a Substitute
1. Arrive at a colour detail page (via search or browse).
2. View close-match alternatives section.
3. Compare ΔE distances and combination availability.
4. Navigate to the preferred alternative.

### Explore a Combination
1. Click a combination card from any colour's page.
2. See all member colours side by side.
3. Navigate to any member's detail page.

---

## Data Model

```
Colour
  id            string
  name_en       string
  name_ja_kanji string
  name_ja_kana  string
  hex           string
  lab           { L, a, b }          // for ΔE calculations

Combination
  id            string
  type          "dyad" | "triad" | "tetrad"
  colour_ids    string[]
  mood_tags     string[]

DerivedFields (computed, not stored)
  colour.connection_count    // number of combinations colour appears in
  colour.compatible_colours  // union of colour_ids across all combinations minus self
  colour.versatility_score   // formula above
```

---

## Design Principles

- **Faithful rendering**: Wada's colours must be accurately reproduced; use calibrated hex values from verified reproductions of the original cards.
- **Minimal chrome**: The colours are the content. UI elements should be neutral and recede.
- **Typography**: Bilingual labels (English + Japanese) on all colour names, respecting the original bilingual intent of the series.
- **No algorithmic generation**: All combinations are from Wada's documented work, not synthesised. The tool surfaces and analyses; it does not invent.

---

## Out of Scope

- User-generated colour combinations
- Integration with external design tools (Figma, etc.) — v1
- Purchasing or licensing Wada's original prints
- Colour accessibility (WCAG contrast) scoring — potential v2 addition
