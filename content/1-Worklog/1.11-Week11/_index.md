---
title: "Week 11 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Develop frontend for Type Rush (FCJ Final Project)
* Implement Wave Rush multiplayer mode UI and WebSocket integration
* Build real-time communication with game-service microservice

### Tasks to be carried out this week:
| Day | Task                                                                             | Start Date | Completion Date | Reference Material                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Design Wave Rush frontend architecture<br/>- Plan WebSocket event structure with game-service | 10/11/2025 | 10/11/2025 | <https://socket.io/docs/v4/client-api/> |
| 3   | - Implement Wave Rush UI components<br/>- Build game state management | 11/11/2025 | 11/11/2025 | |
| 4   | - Develop Wave Rush WebSocket integration<br/>- Test real-time game synchronization with game-service | 12/11/2025 | 12/11/2025 | |
| 5   | - Design Type Race frontend architecture<br/>- Plan single-race UI flow | 13/11/2025 | 13/11/2025 | |
| 6   | - Implement Type Race UI components<br/>- Build WebSocket integration for Type Race | 14/11/2025 | 14/11/2025 | |


### Week 11 Achievements:

**FCJ Final Project: Type Rush - Frontend Development Overview**
* Type Rush multiplayer typing game frontend:
    * Designed comprehensive real-time multiplayer typing game user interface.
    * Planned two distinct game modes: Wave Rush and Type Race.
    * Architected frontend WebSocket integration with game-service microservice.
    * Created scalable frontend game state management with React hooks.
* Technology stack for frontend:
    * Frontend framework: React with TypeScript for type safety.
    * Real-time communication: Socket.IO client for WebSocket connection.
    * State management: React Context API and custom hooks.
    * UI framework: Tailwind CSS for responsive styling.
    * Game-service: Separate microservice handling all WebSocket events and multiplayer game logic.

**Wave Rush Game Mode - Frontend Architecture (Day 1)**
* Designed Wave Rush frontend structure:
    * Multi-round typing race UI with progressive difficulty display.
    * Wave-based gameplay interface where players compete across multiple rounds.
    * Real-time player progress visualization with animated progress bars.
    * Responsive layout for multiplayer game room (2-8 players).
* Planned frontend game states and screens:
    * Lobby screen: Room code display, player list, ready-up buttons.
    * Countdown screen: Pre-game countdown animation (3-2-1-GO).
    * Wave active screen: Typing interface with text display and input field.
    * Wave results screen: Round results, player rankings, next wave preview.
    * Game complete screen: Final leaderboard and statistics.
* Designed WebSocket event structure with game-service:
    * Client → Server events: `join_room`, `player_ready`, `typing_progress`, `wave_complete`.
    * Server → Client events: `room_updated`, `game_starting`, `wave_started`, `player_progress_update`, `wave_ended`, `game_finished`.
* Created component hierarchy:
    * `WaveRushPage` (parent container)
    * `WaveRushLobby` (lobby and room management)
    * `WaveRushGame` (active gameplay)
    * `PlayerList` (real-time player status)
    * `TypingArea` (text display and input)
    * `ProgressBar` (player progress visualization)
    * `WaveResults` (inter-wave results)
    * `FinalResults` (game complete screen)

**Wave Rush Frontend Implementation (Day 2-3)**
* Built Wave Rush React components:
    * Created `WaveRushLobby` component:
        * Room code display with copy-to-clipboard functionality.
        * Dynamic player list showing join status and ready state.
        * Ready/Unready button with state management.
        * Start game button (only visible to room host).
    * Developed `WaveRushGame` component:
        * Wave number and timer display.
        * Text display with character-by-character highlighting.
        * Input field with character validation and error highlighting.
        * Live WPM and accuracy calculation display.
    * Implemented `PlayerList` component:
        * Real-time player status cards with avatars.
        * Progress bars showing completion percentage.
        * WPM and accuracy display for each player.
        * Current ranking display during active wave.
    * Built `TypingArea` component:
        * Monospace text display with correct/incorrect character styling.
        * Cursor position indicator.
        * Input field with auto-focus and prevented copy-paste.
        * Character-by-character validation with visual feedback.
    * Created `WaveResults` component:
        * Animated ranking table with position changes.
        * Individual wave statistics (WPM, accuracy, time).
        * "Next wave in..." countdown timer.
        * Points/score accumulation display.

**Wave Rush WebSocket Integration (Day 3)**
* Set up Socket.IO client connection:
    * Created custom React hook `useGameSocket` for WebSocket management.
    * Implemented connection state handling (connecting, connected, disconnected).
    * Added automatic reconnection logic with exponential backoff.
    * Managed socket lifecycle with proper cleanup in useEffect.
* Implemented client-side event emitters:
    * `join_room(roomCode, playerData)`: Send room code and player info when joining.
    * `player_ready(isReady)`: Emit ready/unready status in lobby.
    * `typing_progress(progress)`: Send real-time typing progress with throttling.
        * Progress data: characters typed, current position, WPM, accuracy, completion percentage.
        * Throttled to every 100ms to reduce network load.
    * `leave_room()`: Handle player leaving gracefully.
* Implemented client-side event listeners:
    * `room_updated(roomData)`: Update UI with current room state.
        * Update player list with join/leave events.
        * Update ready statuses for all players.
        * Enable/disable start button based on ready count.
    * `game_starting(countdown)`: Trigger countdown animation.
        * Hide lobby UI, show countdown overlay.
        * Play countdown sound effects.
    * `wave_started(waveData)`: Initialize wave.
        * Display wave text and wave number.
        * Start wave timer countdown.
        * Enable typing input field.
        * Reset player progress bars.
    * `player_progress_update(playerId, progress)`: Update competitor progress.
        * Update progress bar for specific player.
        * Update WPM and accuracy displays.
        * Recalculate and update live rankings.
    * `wave_ended(results)`: Display wave results.
        * Show wave results component with animations.
        * Update cumulative scores/rankings.
        * Start countdown to next wave.
    * `game_finished(finalResults)`: Display final leaderboard.
        * Show final rankings with podium animation.
        * Display game statistics and achievements.
        * Enable rematch or leave room buttons.
    * `error(errorData)`: Handle and display error messages.
        * Show error notifications (room full, invalid room code).
        * Handle disconnection errors gracefully.

**Type Race Game Mode - Frontend Architecture (Day 4)**
* Designed Type Race frontend structure:
    * Single-round quick typing race interface.
    * Simplified matchmaking lobby with auto-start.
    * Real-time competitor progress with race track visualization.
    * Quick results screen with winner celebration animation.
* Planned Type Race game flow:
    * Queue screen: Players join queue and are auto-matched.
    * Found match screen: Show matched players (2-4 players).
    * Countdown: Short countdown before race starts (3 seconds).
    * Race: Single typing challenge with live rankings and progress.
    * Results: Winner announcement, performance stats, rematch option.
* Designed Type Race WebSocket events:
    * Client → Server: `join_queue`, `leave_queue`, `typing_progress`, `race_complete`.
    * Server → Client: `queue_joined`, `match_found`, `race_starting`, `race_started`, `player_update`, `race_finished`.
* Created Type Race component structure:
    * `TypeRacePage` (parent container)
    * `TypeRaceQueue` (matchmaking queue)
    * `TypeRaceCountdown` (pre-race countdown)
    * `TypeRaceGame` (active race)
    * `RaceTrack` (visual race track with player cars/positions)
    * `TypeRaceResults` (race results)

**Type Race Frontend Implementation (Day 5-6)**
* Built Type Race React components:
    * Created `TypeRaceQueue` component:
        * Queue animation with "searching for players" indicator.
        * Estimated wait time display.
        * Cancel queue button.
        * Player count in queue display (optional).
    * Developed `TypeRaceGame` component:
        * Race track visualization with player avatars/icons.
        * Horizontal progress bars or race track positions.
        * Text display with character highlighting.
        * Input field with validation.
        * Live rankings sidebar.
    * Implemented `RaceTrack` component:
        * Animated player positions based on progress.
        * Smooth transitions as players advance.
        * Position indicators (1st, 2nd, 3rd).
        * Finish line animation when players complete.
    * Built `TypeRaceResults` component:
        * Winner announcement with celebration animation.
        * Final rankings with player stats.
        * Performance comparison (your WPM vs others).
        * Rematch button and return to queue button.

**Type Race WebSocket Integration (Day 6)**
* Implemented Type Race Socket.IO client integration:
    * Reused `useGameSocket` hook with Type Race events.
    * Implemented client-side event emitters:
        * `join_queue(playerData)`: Join matchmaking queue.
        * `leave_queue()`: Exit queue before match found.
        * `typing_progress(progress)`: Send race progress (same as Wave Rush).
        * `race_complete(finalTime)`: Notify race completion.
    * Implemented client-side event listeners:
        * `queue_joined()`: Confirm queue entry, show queue screen.
        * `match_found(matchData)`: Show matched players, prepare for race.
        * `race_starting(countdown)`: Show countdown animation.
        * `race_started(raceData)`: Initialize race with text and timer.
        * `player_update(playerId, progress)`: Update competitor positions in real-time.
        * `player_finished(playerId, finalTime)`: Mark player as finished, show completion.
        * `race_finished(results)`: Display final results and rankings.
        * `queue_cancelled()`: Handle queue cancellation or timeout.

**Frontend State Management and Optimization**
* Implemented efficient state management:
    * Used React Context for global game state (current room, player list, game status).
    * Created custom hooks for game logic separation:
        * `useWaveRush()`: Manage Wave Rush game state and logic.
        * `useTypeRace()`: Manage Type Race game state and logic.
        * `useTypingInput()`: Handle typing validation and progress calculation.
    * Implemented local component state for UI-only concerns.
* Performance optimizations:
    * Memoized expensive calculations (WPM, accuracy) with useMemo.
    * Optimized re-renders with React.memo for player cards.
    * Throttled typing progress updates to game-service (100ms intervals).
    * Debounced input validation for smooth user experience.
    * Used CSS transitions for smooth animations instead of JavaScript.

**UI/UX Enhancements**
* Added visual feedback and animations:
    * Smooth transitions between game states.
    * Typing animation with character highlighting.
    * Progress bar animations with color transitions.
    * Winner celebration animations in results screen.
* Implemented responsive design:
    * Mobile-friendly layout for all game screens.
    * Touch-optimized input for mobile devices.
    * Responsive player list (grid layout adapting to screen size).
    * Portrait and landscape mode support.

**Testing and Debugging**
* Frontend testing performed:
    * Tested WebSocket connection and reconnection scenarios.
    * Verified real-time synchronization with multiple browser tabs.
    * Tested typing input validation and edge cases.
    * Verified progress calculation accuracy (WPM, accuracy).
    * Tested UI responsiveness across different screen sizes.
* Handled edge cases:
    * Network disconnection during active game.
    * Player leaving mid-game handling.
    * Invalid room code error handling.
    * Game service unavailable scenarios.
    * Race condition handling in state updates.

**Challenges and Solutions**
* Key challenges addressed:
    * WebSocket reconnection: Implemented automatic reconnection with state recovery.
    * Real-time sync delays: Added optimistic UI updates for smoother experience.
    * Input lag: Optimized typing validation to run synchronously.
    * State management complexity: Separated concerns with custom hooks.
    * Mobile keyboard issues: Used proper input types and handled focus carefully.