# Stellar Chains - Technical Architecture

## Architecture Overview

Stellar Chains is built as a single-page React application with all game logic contained within a self-contained HTML file. The architecture follows a component-based design with clear separation of concerns.

## Technology Stack

### Core Technologies
- **React 19.2.0**: UI framework with hooks
- **HTML5 Canvas**: Game rendering
- **Web Audio API**: Sound system
- **Tailwind CSS**: Styling framework
- **Vite**: Build tool and dev server
- **TypeScript**: Type safety (config only)

### External Dependencies
```json
{
  "react": "^19.2.0",
  "@vitejs/plugin-react": "^5.0.0",
  "vite": "^6.2.0",
  "typescript": "~5.8.2"
}
```

## Project Structure

```
stellar-chains/
├── index.html              # Main game file (self-contained)
├── package.json            # Dependencies
├── vite.config.ts          # Vite configuration
├── tsconfig.json           # TypeScript configuration
├── metadata.json           # Game metadata
├── .env.local              # Environment variables
├── components/             # Empty (components in index.html)
│   ├── GameCanvas.tsx
│   └── Modal.tsx
└── docs/                   # Documentation
    ├── GAME-OVERVIEW.md
    ├── TECHNICAL-ARCHITECTURE.md
    ├── GAME-MECHANICS.md
    ├── DEPLOYMENT-GUIDE.md
    └── API-REFERENCE.md
```

## Component Architecture

### Main Components

#### 1. StellarChains_App (Root Component)
**Purpose**: Main application container and state management

**State Management**:
```javascript
- gameState: Current game state (StartScreen/Playing/LevelComplete/GameOver)
- score: Current player score
- scoreAtLevelStart: Score checkpoint for retry functionality
- timeLeft: Remaining time in seconds
- level: Current level number (1-5)
- finalScore: Score at game end
- playerName: Player's entered name
- highScore: Persistent high score from localStorage
- isNewHighScore: Flag for new high score achievement
- isMuted: Audio mute state
```

**Key Methods**:
- `stellarChains_handleGameStart()`: Initialize new game
- `stellarChains_handleGameOver()`: Handle game completion
- `stellarChains_handleLevelComplete()`: Progress to next level
- `stellarChains_handleNextLevel()`: Load next level
- `stellarChains_handleTryAgain()`: Retry current level
- `stellarChains_handleBackToMenu()`: Return to start screen
- `stellarChains_handleToggleMute()`: Toggle audio

#### 2. StellarChains_GameCanvas
**Purpose**: Game rendering and interaction handling

**Refs**:
```javascript
- canvasRef: Canvas DOM element reference
- animationFrameId: RequestAnimationFrame ID
- lastTimeRef: Delta time calculation
- gridRef: 8×8 star grid data
- activeChainRef: Currently selected chain
- particlesRef: Particle effects array
- isDraggingRef: Drag state tracking
- scoreRef: Score reference for game loop
- timeLeftRef: Time reference for game loop
```

**Key Methods**:
- `stellarChains_createStar()`: Generate new star object
- `stellarChains_initializeGrid()`: Initialize 8×8 grid
- `stellarChains_draw()`: Canvas rendering
- `stellarChains_update()`: Game state updates
- `stellarChains_gameLoop()`: Main game loop
- `stellarChains_createParticles()`: Particle effect generation
- `handleInteractionStart()`: Mouse/touch start
- `handleInteractionMove()`: Mouse/touch move
- `handleInteractionEnd()`: Mouse/touch end

#### 3. StellarChains_Modal
**Purpose**: Reusable modal overlay component

**Features**:
- Backdrop blur effect
- Centered content
- Fade-in animation
- Responsive design

#### 4. StellarChains_MuteButton
**Purpose**: Audio control button

**Features**:
- Visual mute indicator
- SVG icon with strike-through
- Accessible ARIA labels

### Utility Systems

#### StellarChains_SoundManager
**Purpose**: Centralized audio management

**Properties**:
```javascript
- audioContext: Web Audio API context
- isMuted: Mute state
- mainGainNode: Master volume control
```

**Methods**:
```javascript
- init(): Initialize audio context
- toggleMute(): Toggle mute state
- playSound(config): Generic sound generator
- playConnect(index): Star connection sound
- playComplete(): Chain completion sound
- playLoopComplete(): Loop bonus sound
- playLevelUp(): Level progression sound
- playGameOver(): Game over sound
- playClick(): UI click sound
```

## Data Structures

### Star Object
```javascript
{
  id: number,              // Unique identifier
  x: number,               // Current X position
  y: number,               // Current Y position
  targetY: number,         // Target Y for animation
  color: string,           // Hex color code
  radius: number,          // Star radius
  isVanishing: boolean,    // Vanish animation flag
  vanishProgress: number   // Vanish animation progress (0-1)
}
```

### Particle Object
```javascript
{
  x: number,      // X position
  y: number,      // Y position
  vx: number,     // X velocity
  vy: number,     // Y velocity
  radius: number, // Particle size
  color: string,  // Particle color
  alpha: number   // Opacity (0-1)
}
```

### Level Configuration
```javascript
{
  level: number,    // Level number
  goal: number,     // Target score
  time: number,     // Time limit in seconds
  colors: number    // Number of colors (4-6)
}
```

## Game Loop Architecture

### RequestAnimationFrame Loop
```
stellarChains_gameLoop(timestamp)
  ├── Calculate deltaTime
  ├── stellarChains_update(deltaTime)
  │   ├── Update timer
  │   ├── Check win/lose conditions
  │   ├── Animate stars (falling, vanishing)
  │   ├── Handle grid refill
  │   └── Update particles
  ├── stellarChains_draw(ctx)
  │   ├── Clear canvas
  │   ├── Draw background gradient
  │   ├── Draw active chain line
  │   ├── Draw all stars
  │   └── Draw particles
  └── requestAnimationFrame(stellarChains_gameLoop)
```

## State Management Flow

### Game State Transitions
```
StartScreen
    ↓ (Start Game)
Playing
    ↓ (Score >= Goal)
LevelComplete
    ↓ (Next Level)
Playing
    ↓ (Time = 0 OR All Levels Complete)
GameOver
    ↓ (Try Again / Start Over)
Playing / StartScreen
```

## Event Handling

### Mouse Events
- `onMouseDown`: Start chain selection
- `onMouseMove`: Extend chain
- `onMouseUp`: Complete chain
- `onMouseLeave`: Cancel chain

### Touch Events
- `onTouchStart`: Start chain selection (with preventDefault)
- `onTouchMove`: Extend chain (with preventDefault)
- `onTouchEnd`: Complete chain (with preventDefault)

### Window Events
- `resize`: Update canvas dimensions

## Rendering Pipeline

### Canvas Rendering Order
1. **Background**: Radial gradient
2. **Chain Line**: Active selection visualization
3. **Stars**: All grid stars with glow effects
4. **Particles**: Explosion effects
5. **UI Overlay**: React components (modals, HUD)

### Animation Systems
- **Star Falling**: Smooth interpolation to target position
- **Star Vanishing**: Scale and opacity fade
- **Particle Effects**: Velocity-based movement with alpha decay
- **Modal Transitions**: CSS fade-in animation

## Performance Optimizations

### Rendering Optimizations
- Single canvas element
- Efficient particle filtering
- Minimal DOM manipulation
- RequestAnimationFrame for smooth 60 FPS

### Memory Management
- Particle cleanup (alpha threshold)
- Grid reuse (no recreation)
- Event listener cleanup in useEffect

### State Optimizations
- Refs for game loop data (avoid re-renders)
- Memoized callbacks with useCallback
- Minimal state updates

## Browser Compatibility

### Supported APIs
- ✅ HTML5 Canvas
- ✅ Web Audio API (with fallback)
- ✅ RequestAnimationFrame
- ✅ LocalStorage
- ✅ Touch Events
- ✅ ES6+ JavaScript

### Fallback Handling
- Audio context creation wrapped in try-catch
- Graceful degradation if Web Audio unavailable
- Touch and mouse event support

## Security Considerations

### Namespace Isolation
All game code uses `stellarChains_` prefix to prevent conflicts when embedded in other websites (e.g., WordPress).

### Scoped Styles
Container ID `#stellar-chains-app-container` ensures CSS isolation.

### No External Dependencies
Game runs entirely client-side with no external API calls (except CDN for React).

## Build Configuration

### Vite Configuration
```typescript
- Server: Port 3000, host 0.0.0.0
- React plugin enabled
- Environment variable injection
- Path alias: @ → root directory
```

### Development Mode
```bash
npm run dev    # Start dev server on port 3000
```

### Production Build
```bash
npm run build  # Build optimized bundle
npm run preview # Preview production build
```

## Deployment Architecture

The game is designed for multiple deployment scenarios:

1. **Standalone**: Single HTML file deployment
2. **WordPress Plugin**: Embeddable via shortcode
3. **Static Hosting**: Deploy to any web server
4. **CDN**: Serve from content delivery network

See [DEPLOYMENT-GUIDE.md](./DEPLOYMENT-GUIDE.md) for detailed instructions.
