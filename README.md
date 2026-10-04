# IronZone Quest NPC Lazy

**Version:** 0.1.0  
**Author:** IronZone (Banditas)  
**Type:** DayZ server-side script mod (Enforce)  
**Depends on:** Community Framework, DayZ Expansion Core, Expansion AI, Expansion Quests

**Steam Workshop:** https://steamcommunity.com/sharedfiles/filedetails/?id=3813060850  
**Quest JSON pack:** [Custom-Quests](https://github.com/Banditas231/Custom-Quests)

Field quest **givers** do **not** stay on the map from server start. They appear only while at least one player has a quest mapped to that NPC. When nobody has those quests anymore, the giver despawns after a short delay.

Companion mod for IronZone Expansion quests — or any server that wants the same “lazy field giver” pattern.

---

## What this mod is for

Expansion Quests spawn every NPC with `"Active": 1` when the mission loads. That is correct for a **hub** (safe zone: Peter, Steve, quest board, extraction pilots). It is a problem for **field** givers: they stand at the objective location 24/7 even when nobody has the quest.

This mod:

1. Lets Expansion spawn NPCs as usual.
2. Immediately **removes** every NPC listed in `LazyNPCs`.
3. **Spawns** that giver again when a mapped quest becomes active for any player.
4. **Despawns** the giver after `DespawnDelayMs` when no mapped quest is active anymore.

Escort copies (Expansion **AIVIP**) are **not** handled here. Expansion still spawns escorts. Do not put AIVIP objective IDs into this config.

Combat / world AI vision is **not** modified — Expansion only.

---

## What this mod does **not** do

- Does **not** create quests, objectives, or NPC files (`$profile:ExpansionMod/Quests/`).
- Does **not** replace Expansion Quests.
- Does **not** need to run on the **client** (`-servermod=`).
- Does **not** lazy-spawn hub NPCs unless you add them to `Settings.json` (do not).

---

## Requirements

| Requirement | Why |
|-------------|-----|
| DayZ dedicated server | Server-side spawn / despawn |
| DayZ-Expansion-Licensed (Core + Quests + AI) | Quest NPCs |
| Community Framework | Expansion dependency |
| Your Expansion quest JSON | Givers, quests, objectives |

---

## Install

1. Subscribe on Steam Workshop.
2. Server mod folder, e.g.` @IronZone_QuestNPCLazy.
3. Add to `-servermod=` @IronZone_QuestNPCLazy.
4. Restart once → `$profile:IronZone_QuestNPCLazy\Settings.json`

If the file already exists, it is used. If you **delete** it, the next restart recreates defaults from the **PBO**.

---

## Expansion quest rules (important)

1. Field giver NPC JSON must stay `"Active": 1`. Active `0` = Expansion never loads data → this mod cannot spawn them.
2. Do **not** use Active `0` to “hide” field givers — use this mod instead.
3. Keep hub NPCs Active `1` and **out of** `LazyNPCs`.
4. Shared objectives (e.g. one AIVIP used by several escorts) must stay shared — do not delete shared files.
5. `FollowUpQuest` / `PreQuestIDs` must match the chain you actually copied.

### Default IronZone field mapping

| NPC ID | Giver | Quest IDs |
|--------|--------|-----------|
| 4000 | Marina Sidorova | 131, 137, 402, 805, 815 |
| 4001 | Elena Petrova | 132, 403 |
| 4004 | Survivor (PilotCrash) | 134, 407 |
| 4005 | Survivor (PilotCrash_1) | 135, 408 |
| 4006 | Survivor (PilotCrash_2) | 136, 409 |
| 4007 | Captain (Lost Convoy) | 138, 410 |
| 4008 | Captain (Convoy Pursuit) | 139, 411 |
| 4009 | Captain (Operation Complete) | 140, 412 |

### Leave always spawned (not lazy)

| NPC ID | Role |
|--------|------|
| 1 | Peter (safe zone) |
| 2 | Steve (safe zone) |
| 3 | Quest board |
| 9 | Safe-zone heli pilot |
| 10, 12–19 | Extraction pilots |

---

## `Settings.json`

| Field | Meaning |
|-------|---------|
| `Enabled` | `1` on / `0` off |
| `DebugLogs` | `1` extra `[IronZone_QuestNPCLazy]` prints |
| `DespawnDelayMs` | Keep giver after last mapped quest ends (default **180000** = 180 s) |
| `LazyNPCs` | `NPCID`, `NPCName`, `QuestIDs` |

See [Settings.example.json](Settings.example.json).

### Add a new field quest

1. Add Expansion quest + objectives + NPC (`Active: 1`).
2. Add quest ID to that giver’s `QuestIDs` (or a new `LazyNPCs` entry).
3. Restart.

Skip step 2 → that giver stays world-spawned from mission start.

---

## How it works (short)

1. Expansion `SpawnQuestNPCs` → mod despawns every `LazyNPCs` ID.
2. `AddActiveQuest` → spawn mapped giver (no duplicate if already present).
3. `RemoveActiveQuest` → if no mapped quest left → wait `DespawnDelayMs` → despawn.

Useful log lines: `Init`, `Spawned giver`, `Despawned giver`, `No Expansion NPC data for ID=…`.

---

## Load order (typical)

```
-mod=@CF;@DabsFramework;@DayZ-Expansion-Licensed;…
-servermod=@IronZone_QuestNPCLazy
```

---

## License / reuse

For server admins running IronZone quests or the same pattern. Do not sell this as a paid “quest pack” wrapper. Quest JSON and map data remain your Expansion mission content — see [Custom-Quests](https://github.com/Banditas231/Custom-Quests).
