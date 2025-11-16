# Stellar Chains - Game Overview

## Introduction

**Stellar Chains** is an addictive HTML5 puzzle game where players link chains of colorful stars to forge constellations. The game combines match-3 mechanics with chain-building gameplay, offering a vibrant cosmic adventure with progressive difficulty levels.

## Game Concept

Players must connect 3 or more stars of the same color by dragging across adjacent stars on an 8x8 grid. The game features:

- **Chain Building**: Connect adjacent stars of the same color
- **Loop Mechanics**: Create loops to clear all stars of that color for bonus points
- **Time Pressure**: Race against the clock to reach level goals
- **Progressive Difficulty**: 5 levels with increasing goals and complexity

## Core Gameplay

### Objective
Connect chains of 3 or more stars of the same color to score points and reach the level goal before time runs out.

### Controls
- **Mouse**: Click and drag to connect stars
- **Touch**: Tap and drag on mobile devices
- **Sound Toggle**: Mute/unmute button available in the UI

### Scoring System
- **Base Score**: 10 points per star × chain length
- **Combo Multiplier**: Additional 25% per star beyond minimum (3)
- **Loop Bonus**: 2× multiplier when creating a closed loop
- **Loop Effect**: Clears all stars of that color from the board

### Game States
1. **Start Screen**: Player name entry and game initialization
2. **Playing**: Active gameplay with timer and score tracking
3. **Level Complete**: Success screen with progression option
4. **Game Over**: Final score display with retry options

## Visual Design

### Color Palette
- **Background**: Dark cosmic gradient (#0c0a18 to #1e1b34)
- **Star Colors**: 6 vibrant colors
  - Red: #ff6b6b
  - Yellow: #f9d423
  - Blue: #54a0ff
  - Purple: #5f27cd
  - Green: #10ac84
  - Pink: #ff9ff3
- **UI Accents**: Purple and pink gradients

### Visual Effects
- **Particle System**: Explosion effects when stars vanish
- **Glow Effects**: Shadow blur on stars for cosmic feel
- **Smooth Animations**: Star dropping and vanishing animations
- **Chain Visualization**: Real-time line drawing during chain creation

## Level Progression

| Level | Goal Score | Time Limit | Colors Available |
|-------|-----------|------------|------------------|
| 1     | 1,000     | 60s        | 4                |
| 2     | 2,500     | 65s        | 4                |
| 3     | 4,500     | 70s        | 5                |
| 4     | 7,000     | 75s        | 5                |
| 5     | 10,000    | 80s        | 6                |

## Features

### Core Features
- ✅ 8×8 grid-based gameplay
- ✅ 6 distinct star colors
- ✅ Chain connection mechanics
- ✅ Loop detection and bonus scoring
- ✅ 5 progressive difficulty levels
- ✅ Time-based challenges
- ✅ High score persistence (localStorage)

### Audio System
- ✅ Web Audio API implementation
- ✅ Dynamic sound effects for:
  - Star connections
  - Chain completion
  - Loop completion
  - Level progression
  - Game over
  - UI interactions
- ✅ Mute/unmute toggle

### Responsive Design
- ✅ Mobile-friendly touch controls
- ✅ Responsive canvas sizing
- ✅ Adaptive UI layout
- ✅ Works on all screen sizes

### Performance Optimizations
- ✅ RequestAnimationFrame game loop
- ✅ Efficient particle system
- ✅ Optimized rendering
- ✅ Smooth 60 FPS gameplay

## Technical Highlights

- **Framework**: React 19.2.0 with hooks
- **Rendering**: HTML5 Canvas API
- **Audio**: Web Audio API
- **Styling**: Tailwind CSS
- **Build Tool**: Vite
- **Language**: TypeScript/JavaScript
- **Deployment**: Static HTML (embeddable)

## Target Audience

- Casual gamers looking for quick puzzle challenges
- Match-3 game enthusiasts
- Mobile and desktop players
- All ages (family-friendly content)

## Unique Selling Points

1. **Loop Mechanic**: Innovative closed-loop bonus system
2. **Smooth Performance**: Optimized canvas rendering
3. **Embeddable**: WordPress and website-ready
4. **No Dependencies**: Self-contained game code
5. **SEO Optimized**: Proper meta tags and descriptions
6. **Progressive Difficulty**: Balanced challenge curve

## Future Enhancement Possibilities

- Additional levels beyond level 5
- Power-ups and special stars
- Leaderboard system
- Daily challenges
- Achievement system
- Multiple game modes
- Social sharing features
