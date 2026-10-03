# Design System & Component Library

## Overview

This document defines the reusable design tokens and component architecture for the modernized Embuary skin.

## Color tokens

### Background & Surface

```
background: #0a0e27          (primary dark background)
surface: #1a1f3a              (secondary surface)
surface-elevated: #252d4a    (elevated/modal surface)
```

### Text & Typography

```
text-primary: #ffffff         (main readable text)
text-secondary: #b0b8d4      (secondary/metadata text)
text-disabled: #6b7280       (disabled/inactive text)
text-accent: #e8eff7         (call-to-action text)
```

### Semantic

```
accent: #0ea5e9              (primary action/focus)
focus: #06b6d4               (focus state highlight)
progress: #10b981            (progress indicators)
error: #ef4444               (error states)
success: #10b981             (success states)
warning: #f59e0b             (warning states)
```

## Typography hierarchy

### Font family

- Primary: Noto Sans (current, kept for compatibility)
- Fallback: Arial (for CJK/special chars)
- Icons: Material Design Icons (community extended)

### Font sizes (1080p baseline)

```
hero-title: 60px             (home hero/spotlight)
page-title: 48px             (window/section headers)
section-title: 36px          (shelf headers)
card-title: 20px             (media card titles)
metadata: 16px               (secondary info)
small-text: 14px             (badges, captions)
```

**Scaling for 2K/4K:**
- 1440p: multiply by 1.33x
- 2160p: multiply by 2.0x

## Spacing & geometry

### Standard spacing (1080p)

```
xs: 4px
sm: 8px
md: 16px
lg: 24px
xl: 32px
xxl: 48px
```

### Common component dimensions

**Media cards (1080p):**
- Poster: 180px × 270px
- Landscape: 320px × 180px
- Square: 200px × 200px
- Wide: 480px × 270px

**Containers:**
- Shelf height: 340px (card + spacing)
- Page width: 1920px
- Sidebar width: 300px (optional)

## Animation timing

- Focus change: 150ms (ease-out)
- Fade: 200ms (linear)
- Slide: 250ms (ease-out)
- Reveal: 300ms (ease-out)

**No animations should delay remote interaction.**

## Component categories

### 1. Navigation

- Top bar / horizontal menu
- Sidebar (optional)
- Context menu
- Breadcrumbs

### 2. Hero / Spotlight

- Full-width backdrop
- Poster overlay
- Title + metadata + plot
- Action buttons (Play, Details, Watchlist, Trailer)

### 3. Media cards

- Poster cards
- Landscape cards
- Episode cards
- Collection cards
- Person cards

### 4. Metadata & Info

- Rating display (IMDb, Rotten Tomatoes, user)
- Duration & year
- Genre badges
- Quality badges (4K, HDR, etc.)
- Audio badges (Atmos, DTS:X, etc.)
- Plot text

### 5. Progress indicators

- Progress bar (for resume/continue watching)
- Watched indicator
- Unwatched count badge

### 6. Action buttons

- Primary (Play, Continue)
- Secondary (Details, Watchlist)
- Tertiary (More options)
- Icon buttons

### 7. Lists

- Cast lists
- Collection lists
- Related/recommended items
- Search results

## Reusable XML includes (to create)

**High priority:**
- `Includes.Colors.xml` — Centralized color definitions
- `Includes.MediaCard.xml` — Poster/landscape/square card template
- `Includes.EpisodeCard.xml` — Episode-specific card
- `Includes.Progress.xml` — Progress bar component
- `Includes.MediaBadges.xml` — Quality/audio badge system
- `Includes.Rating.xml` — Rating display component
- `Includes.ActionButtons.xml` — Button styles
- `Includes.Focus.xml` — Focus state animations
- `Includes.Navigation.xml` — Navigation bar
- `Includes.Hero.xml` — Spotlight/hero component

**Medium priority:**
- `Includes.Metadata.xml` — Title/year/runtime/genre block
- `Includes.Cast.xml` — Cast member display
- `Includes.Dialogs.xml` — Common dialog patterns
- `Includes.OSD.xml` — Player OSD components
- `Includes.Search.xml` — Search result formatting

## Implementation rules

1. **No hard-coded colors** — Use color token references
2. **No duplicate components** — Create include, reference it
3. **Responsive proportions** — Use relative sizing where possible
4. **Explicit fallbacks** — Provide default artwork/text
5. **Remote-first focus** — Test all navigation with DPAD
6. **Performance conscious** — Avoid excessive nesting and animation

## Next steps

1. Create centralized color definitions file
2. Build media card include and test across views
3. Create badge system include
4. Build hero/spotlight component
5. Implement navigation bar
6. Refactor home screen to use new components
