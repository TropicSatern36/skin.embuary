# Architecture Overview

## High-level design

The skin remains a presentation layer above Kodi and the Emby-for-Kodi integration layer.

The intended architecture is:

Emby Server
  -> Emby for Kodi
  -> Kodi Library / Kodi Database / Kodi Player
  -> Embuary skin presentation layer

This means the skin should consume metadata and playback state from Kodi rather than trying to own library synchronization or playback control responsibilities.

## Current architecture findings

### 1. Skin structure

The skin is a classic Kodi XML skin using:

- `addon.xml` manifest
- `xml/` directory with window definitions and includes
- helper add-ons for widgets and dynamic metadata
- skinshortcuts for menu generation and custom home items

### 2. Dependency model

The repo depends on helper scripts and other add-ons to fill dynamic content such as:

- widget refresh logic
- helper actions
- home menu generation
- metadata / info orchestration

These are not optional in the current design. They must remain compatible with Kodi 22.

### 3. Emby integration boundary

The skin must not implement:

- library syncing
- watched-state sync
- resume sync
- server auth
- media download/transcode logic

The skin should only present metadata and actions from Kodi/Emby-for-Kodi ecosystems.

### 4. Current risk area

The major risk is that the architecture relies on helper add-ons that were built for older Kodi versions and may not run under Python 3 or current Kodi APIs.

## Target architecture for modernization

The modernization should preserve the existing architecture while restructuring the presentation layer:

- central design tokens for colors, spacing, typography, cards, and actions
- reusable includes for navigation, badges, buttons, media cards, and metadata blocks
- home screen components built from modern card/shelf patterns
- compatibility wrappers for Kodi 22 API differences
- no invasive changes to Emby-for-Kodi add-on behavior

## Design principles

- Preserve functionality
- Prefer presentation-layer refactors over functional rewrites
- Keep Kodi compatibility as the highest priority
- Keep remote/DPAD navigation efficient and predictable
- Maintain Emby-related metadata sources without duplicating backend systems
