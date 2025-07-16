### Extremely Detailed Step-by-Step Guide to Building the AR "Orienteering Runner" Game on Mobile (iPhone) with Unity and Niantic Lightship ARDK

Based on deep research conducted on July 16, 2025 (using web searches for latest tutorials, best practices, and API docs; browsing Niantic's official pages for persistent anchors and shared AR; and cross-referencing with Unity forums/YouTube for navigation HUDs and scoring), this guide builds the game as per your design. Key findings:
- **Lightship ARDK Updates**: ARDK 3.13.0 (April 2025) is stable for VPS/persistent anchors; supports small-scale (~100m) with GPS fusion to minimize drift (tutorials from 2023-2024 remain valid, e.g., VPS for real-location games). Shared AR uses Netcode for 1v1 recapture.
- **Orienteering AR Examples**: No exact matches, but similar navigation games (e.g., Pokémon GO clones) use VPS for points; JROTC adaptations in AR are rare, but mechanics align with Lightship's meshing/semantics for terrain awareness (e.g., detect "invalid paths" via semantic categories like "building").
- **Compass/Mini-Map**: Standard Unity Input.compass for HUD; mini-maps via RenderTexture or GPS plotting (tutorials from 2024 on Unity forums/YouTube for AR outdoor nav).
- **Timing/Scores**: Use PlayerPrefs for local personal bests (simple MVP); Firebase for future shared leaderboards (tutorials abundant, e.g., high scores with Firebase in AR games).
- **Scale Considerations**: For football field size, VPS is optional but ideal for persistence (cm-accuracy in mapped Austin areas); fallback to ARKit + GPS (Input.location) handles ~50m drift-free, per ARDK docs.
- **MVP Focus**: Single-player only (beat times); 5 points, win on 3/5. 1v1 as post-MVP. Total time: 12-20 hours over 2 days.
- **Tech Stack**: Unity 2022.3 LTS/6, Lightship ARDK 3.13.0, AR Foundation 6.0+, Firebase (optional). iOS build for iPhone 15 Pro Max.

Guide structured as a **Kanban board task list** (To Do, In Progress, Done—copy to Trello/Notion). Each task has numbered sub-steps, code snippets, pitfalls, and research-backed tips.

#### Kanban Board Task List
**To Do** (Planning & Setup: Day 1 Morning, 2-3 hours)
- Task 1: Finalize Game Assets and Data Preparation
- Task 2: Set Up Unity Project and Install Dependencies

**In Progress** (Core Development: Day 1 Afternoon - Day 2 Morning, 8-12 hours)
- Task 3: Implement Control Points with Persistent Anchors and GPS Hybrid
- Task 4: Add Navigation Tools (Compass and Mini-Map HUD)
- Task 5: Integrate Timing, Scoring, and Collection Mechanics
- Task 6: Add Escalation, Difficulty, and Feedback Elements

**Done** (Testing & Polish: Day 2 Afternoon, 2-5 hours)
- Task 7: Implement Single-Player MVP Mode and Personal Best System
- Task 8: Test and Debug on Device
- Task 9: Prepare for 1v1 Extension and XREAL Port
- Task 10: Build, Deploy, and Demo Prep

---

### Task 1: Finalize Game Assets and Data Preparation
Gather real-world data and create basic assets for the 5 points.

1. **Map 5 Points**: Use Google Maps or a GPS app to note lat/lon for 5 locations in a ~100m x 50m area (e.g., Austin park). Offsets from start: Point1 (20m N), Point2 (40m E), etc. Create JSON file "Points.json" in Assets/Resources:
   ```json
   [
     {"id":1, "lat":30.2672, "lon":-97.7431, "code":"A1", "symbol":"Circle", "hint":"Near tree", "difficulty":1},
     // Add 4 more with escalating distances
   ]
   ```
   Tip: Ensure points have varied terrain (research: Lightship semantics detect grass/path for hints).
2. **Create Assets**: In Unity, make CheckpointPrefab (Sphere + TextMeshPro for code/symbol). Add audio clips (beep on approach) and textures (flag icons) from free Asset Store (search "AR markers").
3. **Prepare Scoring Data**: Plan local storage for personal bests (times, paths). Research pitfall: Use PlayerPrefs for MVP to avoid Firebase setup delays.
4. **Safety Notes**: Add text asset with warnings ("Run safely, watch traffic").
5. **Time Estimate**: 1 hour. Output: JSON file; prefab ready. Pitfall: Test GPS accuracy outdoors early—ARKit drifts >50m without hybrid.

---

### Task 2: Set Up Unity Project and Install Dependencies
Start from your Lightship samples; configure for mobile AR.

1. **Duplicate Base Scene**: From screenshot, right-click VPSLocalization.unity in Project window > Duplicate > Rename "OrienteeringScene.unity". Open it.
2. **Install Packages** (research: ARDK 3.13.0 requires these for VPS/shared):
   - Window > Package Manager > Add from Git: `https://github.com/niantic-lightship/ardk-upm.git` (core), `https://github.com/niantic-lightship/sharedar-upm.git` (shared).
   - Add "AR Foundation" (6.0+), "XR Interaction Toolkit" (2.5+), "TextMeshPro", "Input System" (for compass).
   - Optional: "Firebase Unity SDK" via Git `https://github.com/firebase/firebase-unity-sdk.git` (for future scores; skip for MVP).
3. **Configure Project**:
   - Lightship > Settings > Enter API key (from lightship.dev).
   - Project Settings > XR Plug-in Management > iOS tab > Enable "Niantic Lightship SDK" and "ARKit".
   - Player > iOS: Bundle ID `com.gauntletai.orienteering`, Min Version 15.0 (for better GPS), Add NSCameraUsageDescription ("AR navigation"), NSLocationWhenInUseUsageDescription ("Checkpoint locating"), NSCompassUsageDescription ("Digital compass").
   - Enable Location Services: Player > Other Settings > Capabilities > Location.
4. **Add Base Components**: In OrienteeringScene, ensure AR Session, AR Session Origin, AR Camera exist (from duplicate). Add EventSystem for UI.
5. **Test Editor Setup**: Hit Play > Check console for AR sim (webcam view). If errors, reimport packages.
6. **Time Estimate**: 1-2 hours. Output: Configured project. Pitfall: API key invalid? Regenerate on Lightship dashboard (research: Common in 2025 updates).

---

### Task 3: Implement Control Points with Persistent Anchors and GPS Hybrid
Spawn 5 persistent points with collection logic.

1. **Add Anchor Manager**: Hierarchy > Add ARPersistentAnchorManager to AR Session Origin.
2. **Create Spawner Script**: New C# "CheckpointSpawner.cs" on new GameObject "GameManager". Load JSON:
   ```csharp
   using Niantic.Lightship.AR.PersistentAnchors;
   using UnityEngine;
   using UnityEngine.XR.ARFoundation;
   using System.Collections.Generic;
   using TMPro;

   [System.Serializable] public class PointData { public int id; public float lat, lon; public string code, symbol, hint; public int difficulty; }

   public class CheckpointSpawner : MonoBehaviour {
       public GameObject checkpointPrefab; // Assign in Inspector
       public ARPersistentAnchorManager anchorManager;
       public List<PointData> points; // Load from JSON
       private GameObject[] checkpoints = new GameObject[5];
       private int collected = 0;
       private int currentIndex = 0;

       void Start() {
           anchorManager = FindObjectOfType<ARPersistentAnchorManager>();
           points = JsonUtility.FromJson<List<PointData>>(Resources.Load<TextAsset>("Points").text); // Load JSON
           SpawnPoints();
       }

       void SpawnPoints() {
           for (int i = 0; i < 5; i++) {
               var payload = new ARPersistentAnchorPayload(points[i].lat, points[i].lon); // VPS/GPS
               anchorManager.TryCreateAnchor(payload, out var anchor);
               checkpoints[i] = Instantiate(checkpointPrefab, anchor.transform.position, Quaternion.identity);
               checkpoints[i].SetActive(false); // Fogged
               var text = checkpoints[i].GetComponentInChildren<TextMeshPro>();
               text.text = points[i].code + "\n" + points[i].hint;
               // Set symbol icon based on points[i].symbol
           }
           checkpoints[0].SetActive(true); // Start with first
       }

       void Update() {
           if (Vector3.Distance(Camera.main.transform.position, checkpoints[currentIndex].transform.position) < 2f) {
               // Verify code (add UI input later)
               collected++;
               Destroy(checkpoints[currentIndex]);
               if (collected >= 3) { Debug.Log("Win!"); return; }
               currentIndex = ChooseNextPoint(); // Random or sequential
               checkpoints[currentIndex].SetActive(true);
           }
       }

       int ChooseNextPoint() { /* Logic for next unlocked point */ return currentIndex + 1; }
   }
   ```
3. **GPS Hybrid Fallback**: Add Input.location.Start() in Start; blend with AR position for >50m (research: Reduces drift per ARDK docs).
4. **Test**: Play > Points spawn; approach to collect. Build to phone for outdoor GPS test.
5. **Time Estimate**: 3-4 hours. Output: Functional points. Pitfall: Drift? Use Lightship World Pose for fusion (tutorials recommend for small scales).

---

### Task 4: Add Navigation Tools (Compass and Mini-Map HUD)
Overlay compass and map for JROTC-style nav.

1. **Create HUD Canvas**: GameObject > UI > Canvas (Screen Space - Overlay) > Name "HUD".
2. **Compass Implementation**: Add Image for needle > Script "ARCompass.cs" on it (research: Unity Input.compass tutorials from 2024):
   ```csharp
   public class ARCompass : MonoBehaviour {
       public Image needle, nextArrow;
       public Transform target; // Next checkpoint
       void Update() {
           needle.transform.rotation = Quaternion.Euler(0, 0, -Input.compass.trueHeading); // True north
           Vector3 dir = target.position - Camera.main.transform.position;
           float angle = Mathf.Atan2(dir.x, dir.z) * Mathf.Rad2Deg;
           nextArrow.transform.rotation = Quaternion.Euler(0, 0, angle); // Subtle arrow
       }
   }
   ```
3. **Mini-Map**: Add RawImage in HUD corner > Script "MiniMap.cs": Use RenderTexture (new camera top-down) or plot dots via GPS (player at center, points as icons fogged until unlocked). Use Lightship semantics for terrain colors.
4. **Bearing Hint**: After 10s, show Text "Bearing: 45° NE, 30m" (calculate via Vector3.Angle).
5. **Test**: Compass rotates; map shows points.
6. **Time Estimate**: 2-3 hours. Output: Nav HUD. Pitfall: Compass inaccurate indoors—enable magnetic heading calibration (docs recommend user calibration prompt).

---

### Task 5: Integrate Timing, Scoring, and Collection Mechanics
Add timers, penalties, and verification.

1. **Timer System**: Add TextMeshPro "TimerText" in HUD > In Spawner:
   ```csharp
   private float legTimer = 20f, totalTimer = 0f;
   void Update() { 
       totalTimer += Time.deltaTime; legTimer -= Time.deltaTime; 
       if (legTimer <= 0) { Penalize(); } // Reset leg
   }
   void Penalize() { legTimer = 20f + 10f; /* Wrong code penalty */ }
   ```
2. **Code Verification**: On approach, show UI input field > Compare to point.code > Success: Capture; Fail: +10s penalty.
3. **Personal Best**: Use PlayerPrefs: `PlayerPrefs.SetFloat("BestTime", totalTimer);` on win; display on start.
4. **Ghost Replay**: Record path (List<Vector3>) on best run; replay as LineRenderer AR overlay (research: Common in racing AR games).
5. **Test**: Time runs; beat best to see ghost.
6. **Time Estimate**: 2 hours. Output: Timed scoring. Pitfall: Use Time.unscaledDeltaTime for pause-resistant timers.

---

### Task 6: Add Escalation, Difficulty, and Feedback Elements
Escalate points; add JROTC twists.

1. **Escalation Logic**: In JSON, set difficulty (1-5); scale timers/distances: easy=20s/10m, hard=60s/100m. Hide hard points via occlusion (Lightship meshing).
2. **Difficulty Twists**: Symbols affect hints (circle=easy hint, triangle=terrain penalty if on "hard" semantic like foliage).
3. **Feedback**: Add ParticleSystem on capture; AudioSource for beeps/voice ("Secured!").
4. **Path Detection**: Raycast from player path; if hits "invalid" semantic (Lightship: buildings/water), penalize (research: Semantics API for real-time checks).
5. **Test**: Harder points take longer; invalid paths reset.
6. **Time Estimate**: 1-2 hours. Output: Full mechanics.

---

### Task 7: Implement Single-Player MVP Mode and Personal Best System
Tie it together for MVP.

1. **Start Sequence**: UI button "Start Run" > Spawn points sequentially > Activate first.
2. **Progression**: On collect, unlock next (win on 3/5 chosen).
3. **Ranks/Badges**: If time < best, award "Sergeant" (UI popup).
4. **End Screen**: Show time, stats; replay option.
5. **Test**: Full run; beat time.
6. **Time Estimate**: 1 hour. Output: Playable MVP.

---

### Task 8: Test and Debug on Device
Validate outdoors.

1. **Editor Tests**: Mock GPS (Lightship playback datasets for sim).
2. **Device Builds**: Build iOS > Test in park: Navigation accuracy, no drift, timers work.
3. **Debug Tools**: Lightship debug overlays for anchors/semantics.
4. **Fix Issues**: Drift? Enable GPS blend (code in Task 3).
5. **Time Estimate**: 2 hours. Output: Stable app.

---

### Task 9: Prepare for 1v1 Extension and XREAL Port
Outline post-MVP.

1. **1v1 Setup**: Add Shared AR manager; sync captures via Netcode (room code join).
2. **Recapture**: Hold 5s to recapture (timer on pin).
3. **XREAL Port**: Download XREAL SDK > Settings > XR > Enable XREAL; test head-tracked HUD.
4. **Time Estimate**: 1 hour (outline). Output: Extension plan.

---

### Task 10: Build, Deploy, and Demo Prep
Ready for cohort.

1. **Final Build**: Add safety warnings > Build iOS.
2. **Deploy**: Ad-hoc via QR for partners.
3. **Demo**: Script ("Navigate like JROTC—beat your time!").
4. **Time Estimate**: 1 hour. Output: Demo-ready app.