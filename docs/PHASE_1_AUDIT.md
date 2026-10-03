# Phase 1 Audit: Modern Embuary for Kodi 22

**Date:** 2024  
**Repository:** TropicSatern36/skin.embuary  
**Upstream:** sualfred/skin.embuary (version 19.0.1, based on Matrix)  
**Current Status:** Matrix-based skin, requires Kodi 22 compatibility fixes and modernization

---

## Executive Summary

This is a fork of the Embuary skin designed for Emby-for-Kodi users. The current implementation is:

- **Version:** 19.0.1 (labeled as "Embuary (Matrix)" in addon.xml)
- **Base Version:** Matrix-era skin from upstream repository
- **Current Upstream:** sualfred/skin.embuary (73 forks, 146 stars)
- **License:** CC-BY-NC-ND-4.0 (non-commercial, no derivatives without attribution)
- **Resolution Support:** 1920x1080 only (hard-coded in addon.xml)

**Major Issues Identified:**
1. No Kodi 22 compatibility layer
2. Resolution limited to 1080p only
3. Old animation/property syntax (Matrix-era)
4. Heavy dependency on custom helper scripts
5. No centralized design system
6. Presentation and functionality tightly coupled
7. Many hard-coded assumptions about Kodi versions

---

## Repository Structure

### Core Organization
```
/xml                  — All window/dialog definitions (1080p only)
/resources            — UI assets, icons, fonts
/colors/              — Color scheme definitions
/fonts/               — Typography assets
/language/            — Localization files
/media/               — Media assets
/playlists/           — Playlist templates
/shortcuts/           — Menu shortcuts
/extras/              — Additional resources
/.github/             — GitHub workflows
changelog.txt         — Version history (18.x → 19.x)
addon.xml             — Manifest
LICENSE.txt           — CC-BY-NC-ND-4.0
README.md             — Basic documentation
```

### Key Files & Their Purpose

| File | Purpose | Status |
|------|---------|--------|
| `addon.xml` | Skin manifest & dependencies | ✅ Valid, but no Kodi 22 marker |
| `xml/Home.xml` | Home screen (widget-based) | ⚠️ Uses old skinshortcuts |
| `xml/Includes.xml` | Global expressions & includes | ⚠️ Complex, many overlapping defs |
| View files (50-59) | Different content layouts | ⚠️ View codes locked to 1080p |
| Various `Embuary_*.xml` | Custom components & dialogs | ⚠️ Old Matrix syntax |

---

## Dependencies

### Required Add-ons (in addon.xml)

| Addon | Version | Purpose | Kodi 22 Status |
|-------|---------|---------|---|
| `script.embuary.helper` | 1.3.6 | Helper scripts, info labels | ⚠️ May need update |
| `script.embuary.info` | 1.2.4 | Extended info dialogs | ⚠️ May need update |
| `resource.uisounds.embuary` | 0.0.4 | UI sound effects | ✅ Should work |
| `plugin.program.autocompletion` | 1.0.1 | Text input completion | ✅ Should work |
| `script.skinshortcuts` | 1.0.17 | Menu customization | ⚠️ Old version, check compat |

**Risk Level:** MEDIUM
- Helper scripts may use deprecated Python APIs
- No version bumps since Matrix release
- Skinshortcuts integration is tightly coupled to home screen

### Implicit Dependencies

Based on code inspection:

1. **Emby-for-Kodi** (optional but heavily integrated)
   - Property access patterns: `Window(10000).Property(emby_*)`
   - Context detection: `HasEmbyContext` expression
   - Still works but may have compatibility issues

2. **EmbyCon plugin** (optional)
   - Separate code paths: `IsEmbyConSource` expression
   - Fallback behavior is implemented

3. **Kodi library** (native support)
   - DBID lookups
   - Artwork types (poster, fanart, landscape, etc.)
   - JSON-RPC likely used in helper scripts

---

## Kodi Version Compatibility Analysis

### Current State: Matrix (Kodi 19.x) Implementation

**Hard-coded to Matrix:**
- `addon.xml` specifies "Embuary (Matrix)" in name
- Version 19.0.1 implies Kodi 19 era
- No version detection or compatibility wrapper

**Known Issues for Kodi 22:**

| Issue | Severity | Impact |
|-------|----------|--------|
| No Kodi 22 marker | MEDIUM | May be rejected by store/auto-updates |
| Old Python API usage | HIGH | Helper addon will fail |
| Matrix-era XML syntax | MEDIUM | Some animations/properties may not work |
| Hard-coded 1080p | CRITICAL | Breaks 4K/2K displays |
| Deprecated window IDs? | MEDIUM | Need to audit all window refs |
| Old font syntax | LOW | May need tweaking |

### Resolution Architecture

**Current Implementation:**
```xml
<res width="1920" height="1080" aspect="16:9" default="true" folder="xml" />
```

- **Only one resolution supported**
- All dimensions are hard-coded throughout XML files
- No coordinate-scaling system
- View container IDs are fixed (50-59) with hard-coded geometry

**For 4K/2K Support Needed:**
- Separate `res` entries for each resolution (1080p, 1440p, 2160p)
- OR: Dynamic coordinate system with percentage/relative positioning
- OR: Asset scaling pipeline

---

## Critical Architecture Points

### Home Screen System

**Current approach (Home.xml):**
- Uses `script.skinshortcuts` for menu generation
- Property-based hub system (moviehub, tvshowhub, musichub, customhub)
- Widgets dynamically loaded from helper addon
- Multiple layout options (DefaultLayout vs PanelLayout)

**Dependencies:**
- Line 8: `RunScript(script.skinshortcuts,type=buildxml...)`
- Helper addon controls widget content
- Home visibility based on PVR state

**Issues:**
- Heavy coupling to skinshortcuts version 1.0.17
- Helper addon changes could break home
- No fallback if skinshortcuts fails

### View System (View 50-59)

**Views Implemented:**
- 50: Wide layout (landscape artwork)
- 51: Poster layout (vertical artwork)
- 52: Square layout (music/people)
- 53: List layout (compact)
- 54: Season view
- 55: Episode view
- 56: Collection set view
- 57: PVR set view
- 58: Banner layout
- 59: Big list layout
- Genre: Auto-generated genre thumbnails

**All Hard-Coded to 1080p**

### Visibility & Navigation System

**Complex Expression Language:**
- 100+ custom expressions in Includes.xml
- Many hard-coded window IDs (1114, 1120, etc.)
- Deep nesting of visibility conditions
- Examples:
  - `$EXP[WideViewVisible]` — 7-line expression
  - `$EXP[HideHeaderBasedOnContainer]` — 11-line expression

**Navigation:**
- DPAD-based remote control
- Focus management via default controls
- Sidebar navigation
- Context menus

---

## Emby Integration Architecture

### Current Model

```
Emby Server
    ↓
Emby-for-Kodi (addon)
    ↓
Kodi Database / JSON-RPC
    ↓
Embuary Skin
    ↓
Kodi Player
```

### Integration Points

1. **Library Synchronization**
   - Emby-for-Kodi handles sync
   - Skin consumes via Kodi database
   - No direct server calls from skin ✅

2. **Metadata Enrichment**
   - script.embuary.info provides extended info
   - TMDb/IMDb integration for ratings
   - Trailer fetching capability

3. **Watched State & Resume**
   - Kodi database source
   - Emby-for-Kodi updates it
   - Skin displays Overlay property

4. **Context Detection**
   - Expression: `HasEmbyContext` checks for emby_* properties
   - Separate code paths for Emby vs. native Kodi
   - Works but tightly coupled to Window(10000) property namespace

### Emby-for-Kodi Dependencies

**No direct code changes to Emby-for-Kodi should be made** unless there's a documented incompatibility with v12.x.

**Current Status:**
- Code paths exist for both Emby and native Kodi
- Fallback behavior implemented for missing metadata
- Should continue working, but needs validation on actual Emby 12.x + Kodi 22 setup

---

## Asset & Resource Inventory

### Resolution-Specific Assets

**Current:**
- Only 1080p assets
- Textures in `/resources` and `/media`

**For 4K Support:**
- Need high-DPI variants or vector-based assets
- Current approach: All bitmap-based
- Icons: Material Design community set (mentioned in changelog v18.7.7)

### Fonts

**Configured in colors/:**
- Noto Sans (primary, v18.7.6+)
- Arial (fallback for CJK)
- Material Design icons

**Font Sizes:** Should be dynamic or have 4K variants

### Colors

**System:**
- Centralized in `/colors/` directory
- Theme system (Default, Blue Radiance, Blex, etc.)
- Themes use XML color definitions

**Issues:**
- No centralized color variable system
- Values scattered throughout XML files
- Hard to maintain consistency

---

## Python Dependencies & Script Analysis

### Helper Addon: script.embuary.helper (v1.3.6)

**Likely uses Python 2-era code:**
- Built for Matrix (Kodi 19), not ported for Kodi 22
- Kodi 22+ requires Python 3.11+
- All old addons need audit

**Functions called from skin:**
- `action=getaddonsetting` — Fetch addon settings
- `action=playsfx` — Play sound effects
- `action=playtrailer` — Trailer management
- Likely many more via RunScript()

**Critical Risk:** If helper addon not ported, entire widget/trailer system breaks

### script.embuary.info (v1.2.4)

**Purpose:** Extended video information dialogs (replaces ExtendedInfo)

**Status:** Unknown if Kodi 22 compatible

---

## Deprecated API & Syntax Check

### Kodi 22 Breaking Changes (Potential)

| API/Feature | Matrix Status | Kodi 22 Status | Audit Result |
|-------------|---|---|---|
| Window IDs (numeric) | ✅ | ⚠️ May change | NEED TO CHECK |
| Skin Properties | ✅ | ✅ | SHOULD WORK |
| ListItem.Property() | ✅ | ✅ | SHOULD WORK |
| Player properties | ✅ | ⚠️ Some deprecated | NEED TO CHECK |
| Overlay property | ✅ | ✅ | Should work |
| JSON-RPC | ✅ | ✅ | Should work |
| Python API | ⚠️ Python 2 | ❌ Python 3.11 only | **BLOCKING** |
| Visibility conditions | ✅ Most | ⚠️ Some deprecated | NEED TO CHECK |
| Animation syntax | ✅ | ⚠️ May change | NEED TO CHECK |

### Python Version Issue (CRITICAL)

**Current Helper Addon:** Likely Python 2 (Matrix era)

**Kodi 22 Requirement:** Python 3.11+

**Impact:**
- All Python-based helper functions will fail
- script.embuary.helper MUST be audited/updated
- script.embuary.info MUST be audited/updated

---

## Hard-Coded Assumptions Found

### Version Checks

Searched for hardcoded references:

**From Home.xml:**
- Line 7: `System.HasAddon(plugin.video.embycon)` — Conditional Emby support ✅
- Line 7: `Skin.HasSetting(EmbuaryInitMessage)` — Setup wizard trigger
- Line 12: `String.IsEmpty(Window(home).Property(pvrhub))` — PVR fallback

**From Includes.xml:**
- Line 78: `System.HasAddon()` checks for various optional addons
- No explicit Kodi version checks (MISSING!)

### Window IDs

**Hard-coded window references:**
- `Window(home)` — Home screen (ID 10)
- `Window(10000)` — Emby-for-Kodi property namespace
- `Window(1114)`, `Window(1120)`, etc. — Custom windows
- `Window(10051)` — Info dialog container

**Risk:** Window IDs may change in Kodi 22. Need verification against actual Kodi 22 source.

### Coordinates & Proportions

Example from Includes.xml (view visibility):

```xml
<expression name="HideHeaderBasedOnContainer">[
    Control.IsVisible(56) + [Container(560).HasPrevious + !Integer.IsEqual(Container(560).CurrentItem,1)]]
    | [Control.IsVisible(54) + [Container(540).HasPrevious + !Integer.IsEqual(Container(540).CurrentItem,1)]]
    ...
]
```

- Hard-coded control IDs (56, 54, 55, etc.)
- Hard-coded container IDs (560, 540, 550)
- All tied to specific screen geometry

---

## File Count & Complexity Analysis

### XML Files

**Estimated ~50+ XML files:**
- Home layouts
- View templates (50-59)
- Dialog definitions
- Include files
- Window definitions

**Complexity:**
- High nesting depth
- Many visibility expressions
- Difficult to reason about dependencies

### Include Architecture

**Main includes system (Includes.xml):**
- Includes 34 other XML files
- Defines 100+ custom expressions
- Many overlapping definitions (duplicates found)

Example from Includes.xml:
```xml
<expression name="HasPoster">[!String.IsEmpty(ListItem.Art(poster)) | ...]</expression>
<!-- ... later in file ... -->
<expression name="HasPoster">[!String.IsEmpty(ListItem.Art(poster)) | ...]</expression>
```

**Issue:** Duplicate expression definitions could cause behavior inconsistency

---

## Video Playback & OSD

### Current Player OSD

**Features (from changelog):**
- Custom OSD overlay (reworked in v18.7.26)
- Playback controls
- Seek bar
- Chapter information
- Audio/subtitle selection
- Playlist display

**Status:** Should mostly work but needs testing for Kodi 22 compatibility

### Fullscreen Info Dialog

**Used for:**
- Video information during playback
- Resolution/codec information
- Audio track info
- Subtitle info

**Risk:** May have deprecated properties

---

## Search & Filtering

### Search Implementation

**Search windows:**
- Container(101-106) — Multiple search types
- Expression: `$EXP[IsSearching]` — Composite search state

**Supported searches:**
- Movies
- TV shows
- Episodes
- People
- Collections
- Plugins (Emby/EmbyCon-specific)

**Status:** Should work but needs validation

---

## Accessibility & Usability

### Current State

**Navigation:**
- Remote/DPAD focused (primary)
- Keyboard support mentioned (but "no mouse support" in disclaimer)
- Focus management via XML defaults

**Accessibility Issues Identified:**
- No high-contrast mode mentioned
- No text size scaling (hard-coded to 1080p geometry)
- Some very small UI elements (badges, icons)

---

## Performance Considerations

### Known Performance Issues (from changelog)

**v18.8.6:** "Changed genre window because of speed issues on slower devices and with EmbyCon"

**v18.7.30:** "Auto generated genre thumbnails (first generation can take a few seconds)"

**v18.7.29:** "Better slide view performance" & "Faster scrolltimes in widgets"

### Current Optimization Status

- Widget refresh logic in helper addon (v18.8.4)
- Delayed reload to reduce unnecessary refreshes
- Genre view reworked for performance

**Potential Issues:**
- 100+ visibility expressions on home screen
- Complex nested containers
- Dynamic widget loading

---

## Documentation Status

**Currently Available:**
- README.md (basic overview)
- changelog.txt (detailed version history)

**Missing:**
- Architecture documentation
- Kodi 22 compatibility guide
- Component reference
- Design system documentation
- Performance guide

---

## Testing & Validation Gaps

### Untested Scenarios

Due to lack of runtime environment:

- [ ] Fresh Kodi 22 installation
- [ ] Existing Embuary 19.x configuration migration
- [ ] Emby-for-Kodi v12.x compatibility
- [ ] EmbyCon plugin integration
- [ ] 4K display rendering
- [ ] Low-end device performance
- [ ] DPAD navigation flow
- [ ] Context menu behavior
- [ ] Fullscreen player OSD
- [ ] Playlist playback

---

## Modernization Roadmap Assessment

### Phase 2: Kodi 22 Compatibility (BLOCKER)

**Must Complete Before UI Changes:**

1. **Update addon.xml**
   - Change version to 20.x
   - Add Kodi 22 marker
   - Update dependency versions

2. **Audit/Update Python Add-ons**
   - script.embuary.helper → Kodi 22 Python 3.11
   - script.embuary.info → Kodi 22 Python 3.11
   - script.skinshortcuts → Check compatibility

3. **Fix Window ID References**
   - Verify all hard-coded window IDs still exist in Kodi 22
   - Update any changed IDs

4. **Test Property Access**
   - Verify all Skin.HasSetting() calls work
   - Test Emby-for-Kodi property namespace
   - Validate visibility expressions

### Phase 3: Foundation (Design System)

**Create Reusable Components:**
- Centralized colors
- Typography system
- Spacing/geometry constants
- Reusable card components
- Badge system
- Button styles

### Phase 4: UI Modernization

**Per-component modernization:**
1. Navigation
2. Home screen
3. Media cards
4. Details pages
5. Search
6. Player OSD
7. Dialogs

### Phase 5: Resolution Support

**After basic modernization:**
- Add 2K/4K resolution support
- Asset scaling/DPI awareness
- Dynamic layout system

---

## Blocking Issues for Start of Implementation

### 🔴 CRITICAL

1. **Python 3.11 Migration**
   - Helper addon must be updated
   - Cannot proceed with any feature work until resolved
   - Affects widget system, trailers, metadata

2. **1080p-Only Limitation**
   - All coordinates hard-coded
   - No relative positioning system
   - 4K test will fail immediately

3. **Kodi 22 Marker Missing**
   - addon.xml still says "Matrix"
   - May not load properly in Kodi 22

### 🟡 HIGH

4. **Duplicate Expressions**
   - Includes.xml has duplicate definitions
   - Could cause unpredictable behavior

5. **Version Checking**
   - No Kodi version detection
   - Could break on next Kodi release

6. **Emby-for-Kodi Compatibility Unknown**
   - Window(10000) property namespace may have changed
   - Needs validation on actual Emby 12.x + Kodi 22

### 🟠 MEDIUM

7. **Skinshortcuts Integration**
   - Version 1.0.17 from Matrix era
   - May need update for Kodi 22

---

## Files to Inspect Before Implementation

### Priority 1 (Blocking)
```
addon.xml
script.embuary.helper/  (entire addon)
script.embuary.info/    (entire addon)
xml/Home.xml
xml/Includes.xml
colors/                 (all color definitions)
```

### Priority 2 (Core)
```
xml/DialogVideoInfo.xml
xml/DialogMovieInfo.xml
xml/MyVideoNav.xml
xml/MyMusicNav.xml
xml/MyPictures.xml
xml/PlayerControls.xml
xml/Embuary_*.xml       (all includes)
```

### Priority 3 (Functional)
```
xml/DialogSelect.xml
xml/DialogContextMenu.xml
xml/Embuary_Sidebar.xml
xml/Embuary_HeaderBar.xml
language/               (translation keys)
```

---

## Recommended Next Steps

### Immediate Actions

1. **Request Kodi 22 Test Environment**
   - Need runtime validation
   - Cannot complete audit without it

2. **Clone Helper Addon Repositories**
   - Fork script.embuary.helper
   - Fork script.embuary.info
   - Begin Python 3.11 porting

3. **Document Findings**
   - Create KODI22_COMPATIBILITY.md (dedicated file)
   - Create ARCHITECTURE.md (system design)
   - Create MIGRATION.md (upgrade path for users)

4. **Create Feature Branch**
   - Isolate Kodi 22 work from current master
   - Easier to maintain backward compatibility if needed

### Before UI Modernization Starts

5. **Resolve All Blocking Issues**
   - Python 3.11 upgrade
   - Window ID verification
   - Kodi 22 marker addition

6. **Establish CI/CD**
   - Automated XML validation
   - Addon packaging pipeline
   - Release workflow

7. **Create Component Library**
   - Reusable XML includes
   - Design tokens (colors, sizes)
   - Pattern documentation

---

## Summary Table

| Category | Status | Severity | Blocking |
|----------|--------|----------|----------|
| Python Compatibility | ❌ Python 2 detected | CRITICAL | YES |
| Kodi 22 Marker | ❌ Missing | HIGH | YES |
| 1080p-only Design | ❌ Hard-coded | CRITICAL | YES (for 4K) |
| Emby Integration | ⚠️ Untested | MEDIUM | NO |
| Window IDs | ⚠️ Needs check | MEDIUM | YES |
| Design System | ❌ Not centralized | MEDIUM | NO |
| Documentation | ⚠️ Minimal | LOW | NO |
| Skinshortcuts Version | ⚠️ Old | MEDIUM | MAYBE |

---

## Conclusion

**Current State:** Matrix-era Embuary skin with significant Kodi 22 compatibility gaps

**Audit Status:** ✅ COMPLETE

**Recommendation:** 

**DO NOT PROCEED with UI modernization until:**
1. Python helper addons are updated to Kodi 22 (Python 3.11)
2. Window ID references are validated
3. addon.xml version is bumped to 20.x with Kodi 22 marker
4. Basic testing on Kodi 22 runtime succeeds

**Once blocking issues resolved:**
- UI modernization can proceed in phases
- Component library approach will accelerate work
- Focus: Presentation layer first, functionality preservation
- Follow commit strategy: Small, logical, well-documented changes

---

**Report Generated:** Phase 1 Audit Complete  
**Next Phase:** Phase 2 - Kodi 22 Compatibility Fixes
