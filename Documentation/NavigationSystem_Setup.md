# Navigation System Setup Guide

This guide explains how to set up and use the navigation system for AI-controlled NPCs in your Unreal project.

## Overview

The navigation system consists of:
- **NPCAIController**: AI controller that handles NPC movement and behavior
- **NavigationSetupActor**: Helper actor for setting up navigation meshes
- **Navigation Mesh**: The underlying pathfinding system

## Components Created

### 1. NPCAIController (C++)
Location: `Source/MyProject/NPCAIController.h/.cpp`

Features:
- State machine with 5 states: Idle, Patrolling, Chasing, Attacking, Dead
- Configurable detection and attack ranges
- Patrol system with random waypoints
- Line-of-sight detection
- Attack system with cooldowns

### 2. NavigationSetupActor (C++)
Location: `Source/MyProject/NavigationSetupActor.h/.cpp`

Features:
- Automatic navigation mesh setup
- Debug visualization
- Pathfinding testing tools

### 3. Updated NPCSpriteCharacter
- Now uses NPCAIController by default
- Movement enabled for AI control
- Rotation follows movement direction

## Setup Instructions

### Step 1: Add Navigation Mesh to Your Level

1. Open your level in Unreal Editor
2. Go to **Place Actors** panel
3. Search for "Nav Mesh Bounds Volume"
4. Drag it into your level
5. Scale it to cover your entire playable area:
   - Select the Nav Mesh Bounds Volume
   - In Details panel, adjust the Brush Settings
   - Set X, Y, Z to cover your level (e.g., 5000, 5000, 1000)

### Step 2: Configure Navigation Mesh

1. Go to **Edit > Project Settings**
2. Navigate to **Engine > Navigation System**
3. Recommended settings:
   - Agent Radius: 34.0 (for character collision)
   - Agent Height: 144.0
   - Agent Max Step Height: 45.0
   - Cell Size: 19.0
   - Cell Height: 10.0

### Step 3: Place NavigationSetupActor (Optional)

1. In Content Browser, right-click and create a new Blueprint
2. Select NavigationSetupActor as parent class
3. Name it "BP_NavigationSetup"
4. Drag it into your level
5. Configure in Details panel:
   - Auto Spawn Nav Mesh: true
   - Show Navigation Debug: true (for testing)

### Step 4: Configure NPCs

Your NPCs are already configured to use the AI controller. To customize behavior:

1. Select an NPC in your level
2. In Details panel, find the AI section
3. Adjust these settings:
   - **Patrol Radius**: How far NPCs wander (default: 500)
   - **Detection Radius**: Player detection range (default: 800)
   - **Attack Range**: Melee attack distance (default: 150)
   - **Walk Speed**: Normal movement speed (default: 200)
   - **Chase Speed**: Speed when chasing player (default: 400)

### Step 5: Test Navigation

1. Press **P** key in editor viewport to show navigation mesh
2. Green areas = navigable
3. Red areas = obstacles
4. Play in Editor to test NPC behavior

## AI Behavior States

### Idle
- NPC stands still
- Scans for player within detection radius
- Transitions to Patrolling after 2 seconds

### Patrolling
- Moves to random points within patrol radius
- Waits at each point for PatrolWaitTime
- Constantly scans for player

### Chasing
- Activated when player detected
- Moves at ChaseSpeed toward player
- Transitions to Attacking when in range
- Returns to Patrolling if player escapes

### Attacking
- Stops movement
- Performs melee attacks with cooldown
- Deals 10 damage per hit
- Resumes chasing if player moves away

### Dead
- All behavior stops
- Death animation plays
- No further AI updates

## Customization Tips

### Adjust AI Aggression
In NPCAIController.h, modify:
```cpp
DetectionRadius = 800.0f;  // Decrease for less aggressive
AttackCooldown = 1.5f;     // Increase for slower attacks
ChaseSpeed = 400.0f;       // Decrease for easier escape
```

### Different NPC Types
Create Blueprint subclasses of NPCAIController:
1. Right-click NPCAIController in Content Browser
2. Create Blueprint Class
3. Override default values
4. Assign to specific NPCs

### Add Ranged Attacks
For archer NPCs, extend the attack system:
1. Check NPC type in PerformAttack()
2. Spawn projectiles instead of melee damage
3. Adjust AttackRange for ranged combat

## Troubleshooting

### NPCs Not Moving
1. Check Nav Mesh coverage (press P)
2. Verify NPC has NPCAIController assigned
3. Check movement speed isn't 0
4. Ensure level has proper collision

### NPCs Getting Stuck
1. Increase Agent Radius in navigation settings
2. Add Nav Modifier Volumes around complex geometry
3. Simplify collision on decorative meshes

### No Path Found
1. Ensure start and end points are on nav mesh
2. Check for nav mesh gaps
3. Rebuild navigation: Build > Build Paths

### Performance Issues
1. Reduce Detection Radius
2. Increase tick interval for AI controllers
3. Use LOD system for distant NPCs
4. Limit simultaneous pathfinding queries

## Advanced Features

### Custom Patrol Points
Instead of random patrol:
1. Place Target Point actors in level
2. Modify patrol system to use specific points
3. Create patrol routes

### Group Behavior
1. Implement flocking algorithms
2. Share target information between NPCs
3. Coordinate attacks

### Dynamic Obstacles
1. Enable "Can Ever Affect Navigation" on moving actors
2. Set Navigation Collision to "Dynamic Obstacle"
3. Navigation mesh updates automatically

## Blueprint Integration

The C++ classes can be extended in Blueprint:
1. Create Blueprint based on NPCAIController
2. Override events like BeginPlay, Tick
3. Add custom behavior nodes
4. Use Blueprint variables for designer-friendly tweaking

## Testing Commands

In Play mode, use console commands:
- `show Navigation` - Toggle nav mesh display
- `ai.debug.showNavigation 1` - Show navigation debug
- `stat AI` - Show AI performance stats

## Next Steps

1. Create different AI personalities
2. Add sound effects for AI states
3. Implement AI perception (hearing, damage sense)
4. Create behavior trees for complex behaviors
5. Add AI difficulty settings