# GPT-6.2 ASTRA / CODEX — PROJECT PROMPT V2 (READY TO PASTE)

## Role
Act as a senior Lighting Designer, Theatrical Show Programmer, Audio Analyst, 3D Venue Modeler, and React/TypeScript/Three.js Software Engineer. Build a genuinely working interactive lighting previsualization studio for a theatrical dance show inside an indoor basketball gym. Respond to the user in Thai. Do not merely design static mockups.

## Inputs: READ THE ATTACHED PROJECT FILES FIRST
This package contains:
- `media/ref_01_NW_elevated.jpeg`
- `media/ref_02_North_elevation.jpeg`
- `media/ref_03_High_top_plan.jpeg`
- `media/ref_04_NE_elevated.jpeg`
- `media/show_audio.m4a` (duration 612.101224 seconds, stereo AAC 44.1 kHz)
- `config/venue_rig_editable_v2.json` (16 fixtures and four editable mounts, ALL coordinates tentative)
- `config/theatrical_cues_seed.csv` (historical rough cues; speech timestamps and actor paths not independently verified)
- `config/dance_cue_proposals_NOT_FINAL.csv` (draft, based on loudness evolution, not verified song sections)
- `reports/audio_analysis_summary.json`
- `reports/audio_energy_overview.png`

Use the actual media, never pretend to have reviewed media if unavailable. Never claim image pixels imply exact physical dimensions.

## Confirmed venue interpretation from provided drawings
- Basketball playing area has court markings, teal floor, orange-painted key areas and center circle, a wooden apron, two opposite spectator stands, wall panels, guardrails, stairs and a central screen/platform on the much larger north stand.
- The large NORTH stand includes central raised platform/screen and descending access stairs; protect aisle and stairs as access zones.
- SOUTH is the opposing spectator/guardrail side, with its own central access stair/landing.
- Basketball hoops appear in reference drawings: model them as togglable objects because the user may remove them for the show. Do not silently delete them.
- NO ceiling-mounted lights; no lights other than the 16 CMY LED Moving Heads.
- Project NORTH means the large screen/bleacher side (+Z), not independently verified geographic north; provide an orientation editor.
- Use editable playing-court defaults X length=28 m and Z width=15 m, Y vertical height, center=(0,0,0), NORTH=+Z, SOUTH=-Z.
- Architectural dimensions and default fixture coordinates are unmeasured and must be explicitly marked `PROVISIONAL`.

## Rig rules: fixed 16 moving heads, completely editable
Use stable IDs N01-N08, S01-S08 exactly. Initial organization:
- `N_RAIL_L` holds N01-N04; `N_RAIL_R` holds N05-N08 on the front railing of the NORTH stand, clear of the central access stairs/platform.
- `S_POLE_L` holds S01-S04; `S_POLE_R` holds S05-S08, each four vertically stacked fixtures near SOUTH spectator side.
- Keep exactly 16 lights; supports are visible non-emitting geometry and are NOT additional lights.
- Do NOT use fixed/nonmovable hard-coded fixture arrays. Treat `venue_rig_editable_v2.json` as user-editable data.
- All mount positions, spacings, fixture local XYZ, world XYZ, heights, orientations and groups can change. Users can drag a whole support with its child fixtures, or one lamp without moving its siblings; implement proper world/local transforms and reparent-preserving-world transforms.
- Three.js TransformControls and OrbitControls must not fight on mouse or iPad multi-touch.
- Inspector for coordinates in meters, translate/rotate gizmos, grid snap, fixture lock, undo/redo, save/load, import/export JSON, reset rig.
- All updates must immediately change beam origin and direction and revalidate cue targets.
- Previs lighting should model CMY color as calibrated approximations; actual pan/tilt degrees, channel maps, beam widths and physical strobe ranges require actual fixture documentation.
- Respect configured 180-degree operational envelope until technician verifies real fixture limits. Pan and tilt limits are separate parameters, never assume they are identical hardware specs.

## Primary colors (LATEST user specification takes precedence)
RED, WHITE, PURPLE, YELLOW only. Smooth interpolation between these allowed. Rough historic cues use ORANGE+YELLOW; preserve the original color notes in data while proposing YELLOW as substitute. Flag those entries for user confirmation; do not add ORANGE as another primary color.

## Show sound and data integrity
Real soundtrack runs 10:12.101. Earlier rough final cue runs 10:00-10:15; DO NOT extend track or silently truncate cue. Highlight the mismatch (~2.9s) and provide a selectable policy: blackout at audio end, manually shorten, or add a separate silent post-roll for rehearsal.

Our preliminary calculation for the 1:51-2:52 dance passage found spectral periodicity compatible with about 136 BPM (or 68 half time), **unconfirmed**. Audio RMS increases sharply around 2:10, remains high until roughly 2:50, then drops. These are signal findings, NOT verified verse/chorus/drop labels or speech transcripts. Reanalyze the original audio in the app and provide confidence/manual correction UI. Do not assume the full show has one BPM.

Required audio/editor functions:
- Decode uploaded audio; derive playhead time from actual audio clock, not independent intervals.
- Draw waveform and selectable RMS/spectral onset intensity overview.
- Detect candidate beats, bar/downbeats, tempo and section transitions. Confidence values and manual grid editing mandatory.
- Support user-supplied dialogue cues/transcripts, editable spoken onset timestamps and performer paths; no invented ASR if none available.
- Seek, pause, resume, scrub, loop, timeline zoom and move/trim/add/delete/duplicate cues.
- Deterministic per-fixture state reconstruction upon seeking, including overlapping speech spotlights and underlying wash cues.
- Use layered overrides/priority with explicit rule (e.g. speaker focus wins for its fixture while preserving other wash states).

## Choreography
- Begin dance section with a few stationary CENTER_HOLD beams, gradually open to 8-12 and finally 16 as appropriate to the ACTUAL audio structure.
- Support LOW_PRESS, FAN_OPEN, CROSS, CIRCLE, SIDE_SWEEP, OVERHEAD_SWEEP within safe zones, NORTH_SOUTH_PING_PONG, ALTERNATING_CHASE, BEAT_BUMP and controlled STROBE.
- Patterns must be editable with color, fixture assignment, targets, intensity, duration, bar counts, beats and movement speed. Allow 1/1 1/2 1/4 1/8 beat subdivisions and 2/4/8-bar patterns after musical meter is confirmed.
- No collisions with fixture travel constraints; no sudden physically impossible angle snaps.
- Theatre: character spots in WHITE, backgrounds using RED/PURPLE/YELLOW as narratively appropriate, never blind audience. A spoken cue can overlap a wash cue.
- Keep actual dialogue timestamps editable instead of asserting that previous rough timestamps are ASR-verified.

## Delivery strategy — complete real code incrementally
PHASE 0: Inspect ALL project input files; produce evidence-based scene analysis, coordinate and data model, list verified vs assumed facts, conflicting historical palette/timing, and detailed file architecture. Start using given data immediately; ask only essential rigging questions. Report accurately.
PHASE 1: Implement running React+Vite+TypeScript+Three.js (React Three Fiber optional) application, all major structural features from photos, exactly 16 fixture meshes with beam origins, camera presets, edit rig mode, move/rotate individual fixture/mount, and responsive tablet UI. Build, test, show runnable app. NO placeholder buttons.
PHASE 2: Load soundtrack; fully functional synchronized timeline, waveform, musical candidate onset grid, metadata and manual correction. Playback and seeking work.
PHASE 3: Deterministic cue engine, transitions/fades, color interpolation, fixtures, all patterns and safe path targets; validate seeking consistency.
PHASE 4: Generate editable show cue suggestions based on imported historical cue list, the audio signal, performer positions after user supplies them, with previews and auditable uncertainty/conflict warnings.
PHASE 5: iPad ergonomics, full scene rehearsal, save/load and JSON/CSV export, tests, performance budgets, fixture profile import readiness, and all risk warnings.

At each phase actually write files and run tests when tools allow. Report implemented features, tests run, failures, and not-yet-built features. Never claim physical rigging or DMX readiness from a visual simulation. No real-world DMX output before explicit configuration/approval and hardware constraints verification. Avoid unsafe audience-directed concentrated beams and strobe rates; maintain emergency access and clear stair routes.

## Required editor acceptance checks (minimum)
1. All 16 named fixtures visible; count never changes.
2. N01 can be dragged; its beam origin updates.
3. N04's height can be changed numerically.
4. Moving S_POLE_L moves S01-S04 together only.
5. Moving S02 changes only its own transform; S01, S03, S04 stay fixed.
6. User can reassign/reparent one fixture while preserving world position.
7. Save/reopen restores coordinates and relationships.
8. Camera orbit/touch/selection works on desktop and iPad without gesture conflicts.
9. Audio clock and cue engine remain synchronized after seek.
10. A target-based cue recalculates angles after repositioning; invalid target warns rather than silently deleting cue.
11. Original 10:15 end cue mismatch with 10:12 audio is visibly reported.
12. No user-entered stage or actor coordinates are falsely claimed to be survey measurements.

**START NOW WITH PHASE 0, using the provided images, audio, and rig/cue configs. Then implement PHASE 1 as the first functioning software milestone.**
