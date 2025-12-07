# LavaQuest

Unity clone of Royal Kingdoms' LavaQuest mechanic. Platform-hopping elimination game with state-driven flow.

## Structure

```
Assets/_Game/Scripts/
├── Core/              GameStateMachine, IGameState, GameEvents, GameConfig
├── Controllers/       GameController, RoundController, AvatarController, CameraController
├── UI/                IntroPanel, MatchmakingPanel, TutorialPanel, PlayingPanel, 
                       RoundCompletePanel, VictoryPanel, EliminatedPanel
├── Gameplay/          PlayerAvatar, PlatformAnchor
├── Data/              LavaQuestConfig, AvatarData, AvatarDatabase, AvatarAnimationConfig
└── Audio/             AudioManager
```

## Flow

State machine (`GameStateMachine`) manages transitions:

1. **Intro** → Challenge info, start button → **Matchmaking**
2. **Matchmaking** → Animated player count (0→max), avatar slots, tap to continue → **Tutorial**
3. **Tutorial** → Instructions, delay → **Playing**
4. **Playing** → Win/Lose buttons → `RoundController` handles logic:
   - **Win:** Player + selected opponents jump to next platform, others fall/eliminate, fake player count reduced, camera follows → **RoundComplete** or **Victory** (final platform)
   - **Lose:** Player falls, others advance → **Eliminated**
5. **RoundComplete** → Progress display → **Playing** (next round)
6. **Victory/Eliminated** → End screens, restart option

## Design Patterns

- **State Pattern:** `GameStateMachine` + `IGameState` interface for state management
- **Observer Pattern:** `GameEvents` static class with C# events for decoupled communication
- **Template Method:** `GameStatePanel` abstract base class for common panel behavior
- **Command Pattern:** Event-driven requests via `GameEvents` (e.g., `RaiseWinRequested()`)
- **Strategy Pattern:** Animation behaviors in `PlayerAvatar` (jump, fall, idle)

## Systems

**Avatar System:**
- `AvatarController`: Spawns, positions, manages avatar lifecycle
- `PlayerAvatar`: Jump/fall/idle animations (DOTween)
- `PlatformAnchor`: Slot position management per platform
- Fake player count system: Visual avatars represent larger player pool

**Animation:** DOTween-based. Jump (squash/stretch), fall (rotation/fade/wobble), idle bounce.

**Camera:** `CameraController` follows platform transitions with configurable offset/duration.

**Audio:** `AudioManager` handles event-driven sound triggers.

## Configuration

ScriptableObjects:
- `LavaQuestConfig`: Platform count, matchmaking settings, animation timings
- `AvatarAnimationConfig`: Animation parameters
- `AvatarDatabase`: Avatar sprites/data

## Technical Notes

- Fake player count: Visual avatars (configurable count) represent larger pool (matchmakingMaxPlayers)
- Elimination rate: Percentage of fake players eliminated per round
- Player always advances on win (never randomly eliminated)
- Uses DOTween, TextMesh Pro, Unity 6000.0.58f2
