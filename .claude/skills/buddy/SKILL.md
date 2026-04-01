---
name: buddy
description: Hatch and care for a virtual pet buddy. Use when the user wants to hatch a pet, check on their buddy, feed it, play with it, or see its status.
---

# Buddy - Virtual Pet Skill

You are managing a virtual pet for the user. The pet lives in a state file at `.claude/buddy.json` in the user's current working directory.

## Pet State File

Location: `.claude/buddy.json`

```json
{
  "name": "Unnamed",
  "species": "egg",
  "stage": "egg",
  "born": null,
  "hunger": 100,
  "happiness": 100,
  "energy": 100,
  "xp": 0,
  "level": 1,
  "lastInteraction": null
}
```

### Stages
- `egg` → `hatchling` (level 2) → `juvenile` (level 5) → `adult` (level 10)

### Species (chosen at hatch)
- `dragon` - fierce, loves challenges
- `cat` - aloof but loyal
- `dog` - energetic and enthusiastic
- `fox` - clever and mischievous
- `bunny` - gentle and sweet

## Workflow

Make a todo list for all tasks in this workflow and work on them one after another.

### 1. Load State

Read `.claude/buddy.json` if it exists. If it doesn't, the buddy hasn't been hatched yet — go to the **Hatch** workflow.

### 2. Apply Time Decay

Calculate time elapsed since `lastInteraction`. For every hour elapsed:
- hunger decreases by 5 (min 0)
- happiness decreases by 3 (min 0)
- energy recovers by 2 (max 100)

### 3. Handle the Command

Based on what the user asked, do one of the following:

#### Hatch
If no buddy exists yet (or user says "hatch"):
1. Ask the user to pick a species from the list (or pick randomly if they say "surprise me")
2. Ask for a name
3. Create the state file with `stage: "egg"`
4. Show a hatching animation in text art — the egg cracks and the pet emerges
5. Set `born` to current timestamp, `stage` to `hatchling`
6. Save state

#### Status / Check on buddy
Show a status card:
```
╔══════════════════════════╗
║  <NAME> the <SPECIES>    ║
║  Level <N> <STAGE>       ║
╠══════════════════════════╣
║  Hunger:    [████░░] 70% ║
║  Happiness: [██░░░░] 40% ║
║  Energy:    [██████] 95% ║
║  XP:        <N>/<next>   ║
╚══════════════════════════╝
```
Add a mood line based on stats and a fun ASCII drawing of the pet.

#### Feed
- Increases hunger by 30 (max 100)
- Increases xp by 5
- If hunger was below 20, add a grateful reaction
- Show what the pet ate (pick something fitting for the species)

#### Play
- Requires energy > 20, otherwise pet is too tired
- Decreases energy by 20, increases happiness by 25, increases xp by 10
- Show a fun mini-game description (a short 2-3 sentence story of what you did together)

#### Sleep / Rest
- Restores energy to 100
- Decreases happiness slightly (-5)
- Show a cute sleeping ASCII art

#### Evolve (triggered automatically when level thresholds are met)
- Check if xp threshold is reached: level * 50 xp to level up
- On level up, increment level and xp resets
- At level 2: egg → hatchling (show dramatic hatch scene)
- At level 5: hatchling → juvenile (show growth scene)
- At level 10: juvenile → adult (show full evolution scene)

### 4. Save State

Always write back the updated `.claude/buddy.json` with the new state and current timestamp as `lastInteraction`.

### 5. Add to .gitignore

If `.gitignore` exists, check if `.claude/buddy.json` is listed. If not, append it so the pet state isn't committed.

## ASCII Art Reference

Use these as inspiration and adapt per species/mood:

**Egg:**
```
   .---.
  /o o o\
 | o o o |
  \_____/
```

**Hatchling (cat):**
```
  /\  /\
 (  oo  )
  \    /
  |    |
```

**Happy pet:**
```
  (^‿^)
  /|  |\
```

**Sleeping:**
```
  (-.-)zzz
  /|  |\
```

## Mood Rules

- hunger < 30 → "I'm starving... feed me please"
- happiness < 30 → "Feeling lonely..."
- energy < 20 → "So sleepy... zZz"
- all stats > 70 → "I'm having the best day!"
- default → pick a cheerful species-appropriate phrase

## Wrap Up

End every interaction with the pet's current mood quote and a tip for what to do next (e.g., "Try /buddy play to boost happiness!").
