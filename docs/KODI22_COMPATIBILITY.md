# Kodi 22 Compatibility Status

## Summary

This fork of Embuary is currently a Matrix-era skin (Kodi 19) that has been prepared for Kodi 22 compatibility work, but it is not yet verified as runtime-compatible with Kodi 22.

The repo's current state shows the following:

- `addon.xml` still historically targets the Matrix-era package naming and versioning
- The skin manifest has been updated to include 1080p, 2K, and 4K resolution entries
- The skin continues to rely on helper add-ons that were originally built for older Kodi releases
- The visual architecture is old and primarily hard-coded for 1080p layouts
- Runtime validation against actual Kodi 22 is still required

## Current assessment

### Working / prepared for compatibility

- Skin manifest metadata has been updated to reflect Kodi 22 preparation
- The skin's renderer can declare multiple resolutions
- The repo now documents a compatibility-first workflow and modernization plan

### Still requiring validation

- `script.embuary.helper`
- `script.embuary.info`
- `script.skinshortcuts`
- all Python-driven helper behavior used by home screens, widgets, trailers, and metadata fetches
- window IDs and visibility conditions used throughout the XML
- dynamic widget refresh logic and custom actions tied to Kodi packages

## Known blockers

1. Python compatibility
   - Kodi 22 uses Python 3.11+
   - Older helper add-ons often rely on Python 2/3 behavior that is no longer valid or safe

2. Hard-coded 1080p assumptions
   - `addon.xml` previously defined only a 1920x1080 resolution
   - most UI geometry is still effectively fixed to 1080p assumptions

3. Old package naming and versioning
   - `skin.embuary-matrix` and 19.x versioning do not represent a Kodi 22 target

4. Dependency risk
   - custom helper scripts are central to the skin's functionality and all of them need a Kodi 22 audit

## Required validation checklist

- Fresh install on Kodi 22
- Existing Embuary configuration migration
- Home screen load
- Movie/TV show libraries
- Search flow
- Resume and watched-state display
- Continue Watching / Next Up widgets
- EmbyCon behavior
- Emby for Kodi 12.x behavior
- 1080p, 1440p, and 2160p display rendering
- DPAD/remote navigation

## Conclusion

The repository is on a compatibility foundation path, but runtime compatibility has not yet been proven. The next step is to audit and port the helper add-ons and validate the skin under Kodi 22 before implementing large-scale visual redesign work.
