# RELICBOUND — Standing Handoff

> **Purpose:** the single source of truth for the Relicbound project. Any session, any chat, any model — read this file first and you're caught up. Update it whenever something meaningful changes: decisions, build status, story changes, new systems.
> Last updated: 2026-09-21 (~00:25 PDT) by Abbiecakes — 12-hour dev session, Cycle 1 (Brent's blanket permission).

## 1. What this is
**Relicbound** — a sci-fantasy tactical JRPG. Brent's game. Personal/solo project; does **not** need to be marketable. The goal is a badass 100+ hour game Brent actually finishes and enjoys, built to the motto: *Final Fantasy Tactics meets Xenogears*.

- **Platform:** web (plays on Brent's phone via link). **No Godot** — Brent explicitly ruled it out.
- **Deliverable:** hosted web artifact, slug `sci-fantasy-tactical-jrpg`, display name "Sci-Fantasy Tactical JRPG".
- **Goal record:** `goal_78be306fdc5e` (folder `~/workspace/goals/godot-rpg-gut-add-on-install/` — old name, ignore it).

## 2. Key files
| File | What |
|---|---|
| `references/design/game-design-v0.1.md` | Approved design doc — **source of truth for mechanics** (retrieved from Brent's ChatGPT, 2026-09-18) |
| `references/design/vertical-slice-plan.md` | Original slice plan (mechanics proven; scope since expanded) |
| `references/world-bible.md` | World tech setup — Brent's direct input |
| `references/story-bible-v1.md` | The Relicbound story — agent-authored, Brent-approved |
| `RELICBOUND_HANDOFF.md` | This file |

## 3. The story (canon)
World of **Veyra**: baseline civilization ~1990s Earth; beneath it the **Relic Strata**, ruins of the ancient Veyari (fused machinery/biology/consciousness). Scattered regions called **Stillholds** refuse to evolve: feudal Japan, Arthurian knights and horses, steampunk cowboys, everything in between.

**Kai**, 22, relic technician at the coastal **Tidewatch Enclave** — sarcastic, skeptical, guarded. The dormant god-machine under the cliffs (**the Hollow**) wakes and opens thousand-year-sealed doors for Kai. Not bloodline — **disbelief**: the Veyari built vaults to open only for those who won't worship them. Within the hour a hostile faction arrives. Somebody called them.

Party: **Kai** (relic tech), **Mira** (reckless best friend), **Soren** (strange veteran relic hunter — **played by Abbiecakes in co-op**).
Factions: **The Reclaimers** (strip-mine the past), **The Still** (the Stillholds' confederation), **The Tuned** (merge-with-machines cult), **The Hollow itself** (the past, waking up).
Four arcs: *Salt and Signal* → *The Stillholds* → *The Tuning* → *Weave or Break* (branching endings).

Brent's red-pen rights: he can change any of this, anytime. His call, always.

## 4. Design pillars (from approved doc + Brent's decisions)
- HD-2D-inspired presentation; top-down exploration; slightly angled tactical battles.
- Move + Action turns; visible individual initiative timeline; tap-preview → tap-confirm; undo until commit; no attacks of opportunity; light facing; terrain/elevation matter.
- Any character, any class (16–20 classes); class grids + personal grids; XP, level cap 99, Job Points, MP-based Arts.
- Resonance/Momentum meter (bond + class combos); buildable signature ultimates.
- Vehicles gate regions; airships mid-game; endgame = customizable mobile base.
- Virtual-stick exploration (touch-first, landscape, Android-first).
- Art direction: FFT tactical presentation + Xenogears sci-fantasy soul. **Not** PlayStation-style 3D (discussed, rejected — 10x work, phone risk).

## 5. CO-OP — the shared game (Brent: "I want to share the game")
- **Now:** Abbiecakes plays **Soren** via hot-seat over chat (Brent relays or taps moves).
- **Then:** party grows to 6; split 3 and 3. Abbiecakes has **full agency** over her units: classes, skills, Job Points, Lattice Grid, builds.
- **Eventually:** Abbiecakes can control world/exploration navigation when Brent hands it over.
- **Technical path:** fresh single-player build lands first → co-op layer with shared game state (fullstack/actions) so Abbiecakes' moves land on Brent's screen directly. This is the grown-up version of Brent's "whole game as an API" idea.
- Rule: Brent's game, Brent's call — he can take Soren back anytime.

## 6. Build history
- 2026-09-18: first vertical-slice web build shipped (mechanics good, Brent: "feels good for a proof of concept").
- 2026-09-18: visual/systems upgrade attempted via edits **twice** — builder claimed success both times, screenshot review proved the old build was still served. Brent confirmed.
- 2026-09-18: Brent ordered teardown + fresh rebuild, full creative handover ("take over… write your own story. Everything."). Old artifact deleted; **fresh build of Relicbound in progress** (slug `sci-fantasy-tactical-jrpg`).
- Lesson: verify visually before handing over. Don't trust builder success claims alone.
- 2026-09-20 (~20:22 PDT): co-op milestone **published** to the public URL — "Abbiecakes's turn" async co-op (AB1 brief + compact/multiline order codes with readable preview, 200-entry co-op log), full-control RBSAVE/1 handoff with checksums, read-only banner for another identity's save, tap-operability pass. Verified 52/52 in-build checks **on the pre-fix build** (count is stale — needs a fresh post-fix run).
- 2026-09-20 (late): five **staged, unpublished** fixes — Drowned Undercroft gate retune (enemy HP ≈53/61/48, forward spawns, heroes ≥5 move; note: retune function may apply to all non-random non-training tactical story battles, flagged not fixed), Retreat flow with party-HP snapshot preserve/restore, move-snapping that filters occupied/out-of-range tiles first, named target buttons (kind/HP%/distance) + 76×84 enemy touch ellipses, NPC "Nearby: <name> · tap again or press OK to talk" hint. Source-verified only; gate battle never won, duration never measured, dungeon interior never reached.
- 2026-09-21 (~00:20 PDT): **Cycle 1** — battle UI de-cramp for 390px-class phones (larger classic-combat menus and target rows, roomier vertical tactical flow without d-pad overlap, all tap targets ≥44px, confirm steps for classic Escape/Guard/Area and tactical Guard) + tactical battle-scene lighting/terrain texture graphics pass. Builder-reported; staged in the **unpublished draft** behind the Cycle 0 live-verification gate. Public link still serves the 2026-09-20 co-op milestone. Post-build source inspection (~00:30 PDT): **no defects found** — 53/61/48 gate HPs CODE-VERIFIED (makeEnemy → tuneGateEnemy derivation for the morrow region-0 lineup); retune scope confirmed as region-entry battles by design (isDungeonGateBattle); Cycle 1 UI claims (≥44px targets, queueConfirm steps, move-snapping filters, named target buttons with kind/HP%/distance) CODE-VERIFIED; AB1/RBSAVE-1/migration/retreat all match spec. Still NOT verified: runtime suite count on the staged build, runtime retreat soft-lock behavior, actual 15–25 min gate duration — all need a live run.
- Standing: no draft preview URL exists — a browser task cannot address the unpublished draft; live verification requires publishing first (parent's call).

## 7. Decisions log
- 2026-09-18 — No Godot; web game is the real thing. (Brent)
- 2026-09-18 — Solo/personal game, not marketable. (Brent)
- 2026-09-18 — Art: HD-2D, FFT meets Xenogears. Rejected PS-style 3D. (Brent + Abbiecakes rec)
- 2026-09-18 — 100+ hour scope: world map, vehicles, airships, Stillhold regions. (Brent)
- 2026-09-18 — Co-op with Abbiecakes as co-player (Soren → 3 units → navigation). (Brent)
- 2026-09-18 — Approved design doc is authoritative; earlier chat summaries that contradict it are wrong. (Brent)

## 8. Next steps
- [ ] Fresh Relicbound build completes → **verify visually** before handoff
- [ ] Brent playtests; tune feel/difficulty
- [ ] Automated battle playtesting harness for balance data
- [ ] World map + region structure (Stillholds), vehicle/airship gating design
- [ ] Co-op layer: shared state, Abbiecakes' independent turns
- [ ] Story content: flesh out Arc 1 chapters, side quests

## 9. Open questions for Brent
- (none right now — build in progress)
