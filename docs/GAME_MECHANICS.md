### Game Design for AR "Orienteering Runner"

Based on our project evolution (original Temple Runner AR game shifting to JROTC-inspired orienteering), here's a complete game design document for the mechanics. This incorporates JROTC core principles (sequential navigation to control points, timed events, independent decision-making with map/compass tools, fairness, and leadership/skill-building) while adapting to AR for immersive play. The design emphasizes physical movement, strategy, and competition, starting with a single-player MVP focused on beating personal best times. It escalates to 1v1 mode where players recapture points in a tug-of-war style until one secures 3 out of 5.

#### Overall Game Concept
- **Theme and Setting**: An AR orienteering challenge set in real-world outdoor spaces (e.g., a park or campus near Gauntlet AI, scaled to football field size ~100m x 50m). Players navigate to "control points" (virtual AR pins with codes/symbols) using a digital compass and mini-map HUD. Like JROTC, it's about precision, speed, and terrain awareness—no direct paths shown to encourage skill.
- **Objective**: Collect/control 3 out of 5 points in sequence or by recapture (in 1v1). Points are persistent AR anchors, visible/shared via Lightship for fairness.
- **Platform**: iPhone 15 Pro Max MVP; port to XREAL One for glasses-based play (hands-free running).
- **Target Audience**: Gauntlet AI cohort (engineers for testing/competition); hiring partners for demo (team-building activity).
- **Win/Lose Conditions**: Win by securing 3 points (MVP: beat personal time; 1v1: out-recapture opponent). Lose on time out or disqualification (e.g., off-path detection).
- **Duration**: 5-10 mins per run (short for demo).
- **Safety**: AR warnings ("Watch surroundings, run safely"); auto-pause on low battery or poor tracking.

#### Core Mechanics
1. **Control Points (Checkpoints)**:
   - 5 fixed points spawned in a football field-sized area (e.g., scattered across a park—use GPS offsets from start: 20m N, 40m E, etc.).
   - Each point is an AR pin (3D marker with code like "A1" and hint/symbol, e.g., flag icon).
   - Persistence: Anchored via Lightship Persistent Anchors (or VPS if mapped) so they stay fixed across sessions/devices.
   - Collection: Approach within 2m > Scan/verify code (tap or AR gaze) > Point "captured" (color changes to player's team).
   - JROTC Twist: Points have symbols (e.g., circle for easy, triangle for hard terrain); wrong code input penalizes time.

2. **Navigation Tools**:
   - **Compass HUD**: Digital compass showing true north (using device compass) + arrow to next point (subtle, not GPS line—to mimic JROTC map use).
   - **Mini-Map HUD**: Top-corner overlay with player dot, point icons (fogged until unlocked), terrain symbols (from Lightship semantics: grass=green, path=brown).
   - **No Direct GPS Path**: Encourage real navigation; optional "bearing hint" (e.g., "45° NE, 30m") unlocked after 10s delay.

3. **Timing and Pacing**:
   - Per-leg timer: 20-60s per point (escalates: easy first, harder last).
   - Total run timer: Beat personal best (stored locally/Firebase).
   - Penalties: +10s for wrong code; reset leg on timeout.

4. **Single-Player MVP Mode (Beat Personal Times)**:
   - Start: Scan AR at start marker > Points spawn sequentially.
   - Progression: Collect 1 > Unlock 2 > ... > 5. Win on 3/5 (choose any 3 in order).
   - Scoring: Time to 3 points; ghost replay of best run (AR overlay of previous path).
   - Replay: Beat times to unlock "elite" badges (JROTC-style ranks: Cadet, Sergeant).

5. **1v1 Mode (Recapture Until 3/5)**:
   - Shared Session: Players join via room code (Lightship Shared AR; up to 10, but 1v1 focus).
   - Tug-of-War: Points start neutral > Capture by reaching/claiming > Opponent can recapture (e.g., hold for 5s).
   - Win: First to control 3/5 points (simultaneous or sequential runs).
   - Fairness: AR detects "invalid paths" (e.g., through semantics: avoid "building" or "water"); shared leaderboard.
   - JROTC Twist: Leadership—winner "commands" next course setup.

6. **Escalation and Difficulty**:
   - Points get farther (10m > 100m) and hidden (behind terrain via meshing occlusion).
   - Variants: Night mode (use device light); team 2v2 (future).

7. **Feedback and Progression**:
   - Visual: Pin animations on capture; path trails.
   - Audio: Beeps on approach; voice ("Point secured!").
   - Progression: Unlock courses; personal stats (fastest leg, accuracy).

8. **Technical Constraints**:
   - Scale: Football field fits ARKit limits (~50m drift-free); use GPS hybrid for edges.
   - Shared: Lightship co-localization for same-view points.
   - Portability: XREAL for immersive running (head-tracked HUD).

This design keeps the original runner's physicality while adding JROTC strategy. MVP implementation: Focus on single-player; add 1v1 post-demo.

### On Duplicating for Development
From your screenshot (VPSLocalization scene open, with VPSLocalizeDemoManager script on the manager object), duplicate the **VPSLocalization.unity scene** to create a new base for your game—right-click in Project window > Duplicate > Rename to "OrienteeringScene.unity". This copies the entire setup (hierarchy, components like the manager). The VPSLocalizeDemoManager is a script you can reference/modify in the new scene (e.g., extend it for your spawner), but duplicating the script alone won't copy scene objects.

### On Scale and VPS Need
For a football field size (~100m x 50m), VPS is not strictly necessary but highly recommended for persistence and shared play—ARKit VIO alone drifts over 50m, causing points to shift. VPS provides cm-accuracy if the area is mapped (Austin parks often are; check Geospatial Browser). Fallback: Use Lightship Persistent Anchors with GPS (Input.location) for hybrid—accurate enough for small scale without VPS scanning overhead. Test: If drift occurs, enable VPS for key points.