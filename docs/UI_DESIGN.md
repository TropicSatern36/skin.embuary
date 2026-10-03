# UI Design Direction

## Goal

Modernize Embuary to feel like a modern Emby Android / Android TV experience without abandoning Kodi skin behavior or Embuary's functionality.

## Design direction

The target visual language is:

- dark neutral background
- cinematic artwork with controlled overlays
- strong content hierarchy
- large readable typography
- compact but clear metadata
- large focus state for remote navigation
- card-based shelves and consistent spacing
- restrained gradients and minimal visual clutter

## Core visual principles

### 1. Content-first layout

Home and detail screens should prioritize the media and artwork rather than dense text blocks.

### 2. Remote navigation friendliness

Every element must have:

- clear focus state
- predictable movement
- minimal animation delay
- no accidental focus traps

### 3. Consistency

Reusable cards and metadata blocks should be shared across windows and views instead of copied individually.

### 4. Performance-aware design

Kodi skin performance is sensitive. The updated design should avoid excessive nested containers, repeated image controls, and expensive animation loops.

## Component target categories

- Navigation
- Hero / Spotlight
- Media cards
- Continue Watching cards
- Next Up cards
- Ratings and metadata
- Media badges
- Action buttons
- OSD controls
- Search results

## Recommended default theme tokens

- background
- surface
- surface-elevated
- text-primary
- text-secondary
- text-disabled
- accent
- focus
- progress
- success
- error

## Design constraints

- Keep underlying Kodi functionality intact
- Do not invent metadata not available from Kodi/Emby-for-Kodi
- Preserve existing skin shortcuts and customization system
- Keep the interface compatible with 1080p, 2K, and 4K outputs
