# Stellar Chains - Game Mechanics

## Core Mechanics

### Chain Building System

#### Basic Chain Rules
1. **Minimum Length**: 3 stars required for valid chain
2. **Color Matching**: All stars in chain must be same color
3. **Adjacency**: Stars must be adjacent (horizontally, vertically, or diagonally)
4. **Continuous Path**: Chain must form continuous path without gaps

#### Chain Creation Process
```
1. Player clicks/taps on a star
   ↓
2. Star becomes first link in active chain
   ↓
3. Player drags to adjacent star of same color
   ↓
4. Star is added to chain (if valid)
   ↓
5. Repeat step 3-4 for additional stars
   ↓
6. Player releases mouse/touch
   ↓
7. Chain is evaluated and scored
```

#### Adjacency Detection
Stars are considered adjacent if:
```javascript
const dx = Math.abs(star1.x - star2.x);
const dy = Math.abs(star1.y - star2.y);
const cellSize = canvasWidth / 8;

// Adjacent if within 1.5 cell distance
if (dx < cellSize * 1.5 && dy < cellSize * 1.5) {
  // Stars are adjacent
}
```

#### Backtracking
Players can undo the last star in a chain by dragging back to the second-to-last star:
```javascript
if (existingIndex === activeChain.length - 2) {
  activeChain.pop(); // Remove last star
}
```

### Loop Mechanic

#### Loop Detection
A loop is formed when:
1. Chain has more than 2 stars
2. Last star in chain is the same as first star
3. Chain forms closed path

```javascript
const isLoop = chain.length > 2 && chain[0].id === chain[chain.length - 1].id;
```

#### Loop Bonus Effects
When loop is created:
1. **Score Multiplier**: 2× points for the chain
2. **Color Clear**: ALL stars of that color vanish from entire grid
3. **Special Sound**: Unique audio feedback
4. **Massive Particle Effect**: Particles from all cleared stars

#### Loop Strategy
- High risk, high reward mechanic
- Requires planning and spatial awareness
- Can clear 10-20+ stars in single move
- Best used when one color dominates the board

### Scoring System

#### Base Score Calculation
```javascript
const basePoints = chainLength * 10;
```

#### Combo Multiplier
```javascript
const comboBonus = (chainLength - MIN_CHAIN_LENGTH) * 0.25;
const multiplier = 1 + comboBonus;
const points = basePoints * multiplier;
```

#### Score Examples

| Chain Length | Base | Multiplier | Total | Loop Total |
|--------------|------|------------|-------|------------|
| 3 stars      | 30   | 1.0×       | 30    | 60         |
| 4 stars      | 40   | 1.25×      | 50    | 100        |
| 5 stars      | 50   | 1.5×       | 75    | 150        |
| 6 stars      | 60   | 1.75×      | 105   | 210        |
| 7 stars      | 70   | 2.0×       | 140   | 280        |
| 8 stars      | 80   | 2.25×      | 180   | 360        |

#### Loop Scoring Example
If loop clears 15 stars of same color:
```
Chain: 5 stars (loop)
Base: 5 × 10 = 50 points
Combo: 50 × 1.5 = 75 points
Loop: 75 × 2 = 150 points
Additional: 10 more stars cleared = 10 × 10 = 100 points
Total: 250 points
```

### Grid Mechanics

#### Grid Initialization
- 8×8 grid (64 cells)
- Each cell contains one star
- Random color assignment based on level
- No initial matches guaranteed

#### Star Colors by Level
```javascript
Level 1-2: 4 colors (easier matching)
Level 3-4: 5 colors (moderate difficulty)
Level 5:   6 colors (maximum difficulty)
```

#### Gravity System
When stars are removed:
1. Stars above fall down to fill gaps
2. Smooth animation interpolation
3. New stars spawn from top
4. Grid always remains full

```javascript
// Falling animation
if (star.y < star.targetY) {
  star.y += (star.targetY - star.y) * 0.15;
}
```

#### Refill Algorithm
```javascript
1. Mark vanishing stars as null
2. For each column (bottom to top):
   - Move existing stars down to fill gaps
   - Update target positions
3. Create new stars for empty cells at top
4. Animate new stars falling into position
```

### Time Mechanics

#### Timer System
- Countdown timer in seconds
- Updates every frame (60 FPS)
- Displayed as rounded integer
- Game ends when timer reaches 0

```javascript
timeLeft -= deltaTime;
if (timeLeft <= 0) {
  gameOver();
}
```

#### Time Allocation by Level
```
Level 1: 60 seconds (1:00)
Level 2: 65 seconds (1:05)
Level 3: 70 seconds (1:10)
Level 4: 75 seconds (1:15)
Level 5: 80 seconds (1:20)
```

#### Time Strategy
- Early levels: Focus on learning mechanics
- Later levels: Balance speed vs. combo size
- Loop opportunities: Worth taking time to set up
- Final seconds: Quick small chains better than waiting

### Level Progression

#### Level Goals
```javascript
const LEVEL_CONFIG = [
  { level: 1, goal: 1000,  time: 60, colors: 4 },
  { level: 2, goal: 2500,  time: 65, colors: 4 },
  { level: 3, goal: 4500,  time: 70, colors: 5 },
  { level: 4, goal: 7000,  time: 75, colors: 5 },
  { level: 5, goal: 10000, time: 80, colors: 6 }
];
```

#### Difficulty Curve
- **Level 1**: Tutorial difficulty, easy to complete
- **Level 2**: 2.5× score requirement, slight increase
- **Level 3**: Introduces 5th color, significant jump
- **Level 4**: High score requirement, 5 colors
- **Level 5**: Maximum difficulty, all 6 colors

#### Win Condition
```javascript
if (score >= levelConfig.goal) {
  levelComplete();
}
```

#### Loss Condition
```javascript
if (timeLeft <= 0 && score < levelConfig.goal) {
  gameOver();
}
```

### Animation System

#### Star Vanishing Animation
```javascript
1. Set star.isVanishing = true
2. Increment vanishProgress (0 to 1)
3. Apply scale: 1 - vanishProgress
4. Apply alpha: 1 - vanishProgress
5. When progress >= 1, remove star
```

#### Particle Effects
```javascript
// 20 particles per vanishing star
for (let i = 0; i < 20; i++) {
  particles.push({
    x: star.x,
    y: star.y,
    vx: (Math.random() - 0.5) * 5,  // Random velocity
    vy: (Math.random() - 0.5) * 5,
    radius: Math.random() * 3 + 1,
    color: star.color,
    alpha: 1
  });
}

// Update particles each frame
particles.forEach(p => {
  p.x += p.vx;
  p.y += p.vy;
  p.alpha -= 0.02;  // Fade out
});
```

#### Chain Visualization
- Real-time line drawing during drag
- Line color matches star color
- 70% opacity for visibility
- 4px line width

### Audio Feedback

#### Sound Events
1. **Star Connect**: Pitch increases with chain length
   ```javascript
   frequency = 300 + index * 30
   ```

2. **Chain Complete**: Two-tone success sound
   ```javascript
   tone1: 600 Hz, 0.2s
   tone2: 800 Hz, 0.2s (80ms delay)
   ```

3. **Loop Complete**: Deep bass to high tone
   ```javascript
   tone1: 200 Hz, 0.4s
   tone2: 400 Hz, 0.3s (100ms delay)
   ```

4. **Level Up**: Ascending arpeggio
   ```javascript
   [440, 554.37, 659.25, 880] Hz
   100ms intervals
   ```

5. **Game Over**: Descending tones
   ```javascript
   [440, 349.23, 261.63] Hz
   120ms intervals
   ```

### Strategy Tips

#### Optimal Play Patterns
1. **Look for Loops**: Always scan for loop opportunities
2. **Long Chains**: 5+ star chains have best point efficiency
3. **Color Management**: Clear dominant colors to create opportunities
4. **Corner Strategy**: Corners are easier to create loops
5. **Time Management**: Don't waste time on 3-star chains late game

#### Advanced Techniques
1. **Loop Setup**: Intentionally leave stars to create loop patterns
2. **Cascade Planning**: Predict how stars will fall after clearing
3. **Color Balancing**: Avoid letting one color dominate
4. **Combo Chaining**: Clear stars to set up next move
5. **Endgame Rush**: Quick small chains when time is critical

#### Common Mistakes
1. Taking too long to decide on moves
2. Ignoring loop opportunities
3. Making 3-star chains when better options exist
4. Not planning for star refill patterns
5. Panicking in final seconds

### Difficulty Balancing

#### Easy Elements
- Generous time limits
- Clear visual feedback
- Forgiving adjacency detection
- Backtracking allowed

#### Challenging Elements
- Increasing color count
- Rising score requirements
- Time pressure
- Random grid generation

#### Skill Progression
- Level 1-2: Learn mechanics
- Level 3: Master chain building
- Level 4: Optimize scoring
- Level 5: Perfect execution required
