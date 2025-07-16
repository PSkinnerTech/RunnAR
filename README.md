# RunnAR - AR Orienteering Runner Game

An immersive AR orienteering challenge that combines JROTC-inspired navigation principles with cutting-edge augmented reality technology. Navigate to control points using digital compass and mini-map tools in real-world outdoor environments.

![Unity](https://img.shields.io/badge/Unity-2022.3%20LTS-blue)
![ARKit](https://img.shields.io/badge/ARKit-6.0+-green)
![Platform](https://img.shields.io/badge/Platform-iOS-lightgrey)
![License](https://img.shields.io/badge/License-MIT-yellow)

## Overview

RunnAR transforms outdoor spaces into competitive orienteering courses using augmented reality. Players navigate to virtual control points using only compass bearings and terrain awareness—no GPS breadcrumbs—encouraging real navigation skills like those taught in JROTC programs.

### Key Features

- **Real-World AR Navigation**: 5 persistent control points spawned in football field-sized areas
- **Authentic Tools**: Digital compass and mini-map HUD (no direct GPS paths)
- **Competitive Timing**: Beat personal bests with ghost replay system
- **Physical Gameplay**: Encourages real movement and terrain awareness
- **Multiplayer Ready**: 1v1 recapture mode planned (MVP is single-player)
- **Cross-Platform**: iPhone 15 Pro Max MVP, XREAL One port planned

## Game Mechanics

### Single-Player MVP Mode
- Navigate to 5 AR control points using compass and map
- Collect any 3 out of 5 points to win
- Beat your personal best times
- Ghost replay shows your optimal path
- Escalating difficulty (distance and terrain complexity)

### Planned 1v1 Mode
- Shared AR sessions via Lightship
- Tug-of-war recapture mechanics
- First to control 3/5 points wins
- Strategic positioning and route planning

### JROTC-Inspired Elements
- Sequential navigation to control points
- Timed events with penalties for errors
- Map and compass-only navigation (no GPS assistance)
- Code verification at each checkpoint
- Leadership and skill-building focus

## Technical Stack

- **Engine**: Unity 2022.3 LTS/6
- **AR Framework**: Niantic Lightship ARDK 3.13.0
- **AR Foundation**: 6.0+
- **Target Platform**: iOS 15.0+ (iPhone 15 Pro Max optimized)
- **Persistence**: Lightship Persistent Anchors + GPS hybrid
- **Networking**: Lightship Shared AR (for multiplayer)
- **Storage**: PlayerPrefs (MVP), Firebase (planned)

## Architecture

### Core Components
- **Checkpoint Spawner**: Manages 5 persistent AR control points
- **Navigation System**: Digital compass and mini-map HUD
- **Timing Engine**: Per-leg and total run timers with penalties
- **Collection Mechanics**: Code verification and capture system
- **Progression System**: Personal bests and ghost replay

### Safety Features
- AR tracking quality monitoring
- Boundary detection via Lightship semantics
- "Invalid path" penalties (through buildings/water)
- Auto-pause on poor conditions

## Getting Started

### Prerequisites
- Unity 2022.3 LTS or 6
- iOS development setup (Xcode, Apple Developer account)
- iPhone 15 Pro Max (or compatible ARKit device)
- Lightship developer account and API key

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/PSkinnerTech/RunnAR.git
   cd RunnAR
   ```

2. **Open in Unity**
   - Open Unity Hub
   - Add project from disk
   - Select the RunnAR folder

3. **Install Dependencies**
   - Window > Package Manager
   - Add from Git URL: `https://github.com/niantic-lightship/ardk-upm.git`
   - Install AR Foundation 6.0+, TextMeshPro, Input System

4. **Configure Lightship**
   - Get API key from [lightship.dev](https://lightship.dev)
   - Lightship > Settings > Enter API key
   - Enable Niantic Lightship SDK in XR Plug-in Management

5. **iOS Setup**
   - Project Settings > Player > iOS
   - Set Bundle Identifier
   - Add required Usage Descriptions:
     - Camera: "AR navigation"
     - Location: "Checkpoint locating"
     - Compass: "Digital compass"

### Building and Running

1. **Prepare Test Data**
   - Create `Assets/Resources/Points.json` with 5 GPS coordinates
   - Use the format from `docs/PRD.md` Task 1

2. **Build to Device**
   - File > Build Settings > iOS
   - Build and Run to connected iPhone
   - Test outdoors for GPS accuracy

## Project Structure

```
RunnAR/
├── Assets/
│   ├── Samples/               # Lightship ARDK samples
│   ├── Resources/
│   │   └── Points.json        # Control point coordinates
│   ├── Scripts/
│   │   ├── CheckpointSpawner.cs
│   │   ├── ARCompass.cs
│   │   └── MiniMap.cs
│   └── Prefabs/
│       └── CheckpointPrefab
├── docs/
│   ├── PRD.md                 # Product Requirements Document
│   └── GAME_MECHANICS.md      # Detailed game design
└── README.md
```

## Development Roadmap

### Phase 1: MVP (Complete)
- [x] Project setup with Lightship ARDK
- [x] Basic scene duplication from VPS samples
- [x] GitHub repository setup

### Phase 2: Core Mechanics (In Progress)
- [ ] Checkpoint spawning with persistent anchors
- [ ] Navigation HUD (compass + mini-map)
- [ ] Timing and scoring system
- [ ] Collection mechanics with code verification
- [ ] Personal best tracking

### Phase 3: Polish & Testing
- [ ] Device testing and optimization
- [ ] Safety features and boundary detection
- [ ] Ghost replay system
- [ ] UI/UX improvements

### Phase 4: Multiplayer Extension
- [ ] 1v1 shared AR implementation
- [ ] Recapture mechanics
- [ ] Lobby and matchmaking

### Phase 5: Platform Expansion
- [ ] XREAL One port for hands-free play
- [ ] Performance optimizations
- [ ] Advanced terrain detection

## Educational Context

This project was developed as part of the [Gauntlet AI](https://gauntletai.com) cohort program, designed to showcase innovative AR/AI applications while honoring JROTC training principles. The game serves as both a technical demonstration and a team-building activity for hiring partners.

### Learning Objectives
- Real-world AR development with Lightship ARDK
- GPS/AR hybrid positioning systems
- Multiplayer AR synchronization
- Mobile game optimization
- JROTC leadership and navigation skills

## Contributing

This project is primarily for demonstration and educational purposes. For suggestions or collaboration opportunities:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## About

**Created by Patrick Skinner** | PSkinnerTech

A Gauntlet AI project - pushing the boundaries of what an AI Engineer is capable of.

### Connect
- Twitter: [@PSkinnerTech](https://x.com/PSkinnerTech)
- LinkedIn: [Patrick A. Skinner](https://linkedin.com/in/patrickaskinner/)
- Website: [patrickskinner.tech](https://patrickskinner.tech)
- Gauntlet AI: [@joingauntletai](https://x.com/joingauntletai) | [gauntletai.com](https://gauntletai.com)

## Acknowledgments

- **Niantic Lightship Team** for ARDK documentation and samples
- **Gauntlet AI Cohort** for testing and feedback
- **JROTC Programs** for navigation and leadership inspiration
- **Unity Community** for AR development tutorials and best practices

---

*"Navigate with purpose. Lead with precision. Run with AR."*
