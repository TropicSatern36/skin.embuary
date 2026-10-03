# Migration Guide

## Overview

This fork is in the process of being modernized for Kodi 22 while keeping Embuary functionality and Emby integration intact.

## What has changed

- manifest metadata updated for Kodi 22 preparation
- 2K and 4K resolution entries added to the skin manifest
- compatibility-first documentation created
- architecture and UI design documentation added

## Migration expectations

### For existing users

- existing Embuary configuration should be preserved wherever possible
- settings names should not be changed without a migration path
- old settings should be detected and converted when practical
- if a breaking change is required, it should be documented in this file

### For helper add-ons

If helper add-ons are updated for Kodi 22, they must be validated for:

- Python 3 compatibility
- current Kodi API behavior
- Emby/EmbyCon integration
- widget changes
- resume/watched-state integration

## Compatibility notes

- The skin is not yet runtime-validated against Kodi 22
- helper add-ons must be verified before design work proceeds too far
- compatibility work should take precedence over visual redesign

## Recommended migration path

1. Audit and port helper add-ons to Kodi 22
2. Validate XML and navigation behavior
3. Introduce centralized design tokens and reusable components
4. Modernize presentations layer by layer
5. Re-test Emby for Kodi and EmbyCon flows
6. Keep user settings and menu setups stable

## Risk mitigation

- avoid destructive UI rewrites
- preserve existing widget and menu configuration
- do not break existing library support or Emby integration
- ensure focus navigation remains consistent
