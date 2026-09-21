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
- 2026-09-21 (~00:32 PDT): **Draft-URL extraction closed** (genuine attempt via read-only artifact inspection, id 3c22fa51): there is NO stable HTTP URL for the unpublished draft. The staged build was loaded only via builder-internal, per-session ephemeral audit routes (e.g. `https://<vm-uuid>.metaaivm.com:4431/spaces/v2/hatch-audit-<fresh-uuid>/`, one-time use, torn down after each audit; latest staged-build audit 2026-09-21T07:16:16Z had zero console errors) — not addressable from any live browser task. No draft token/preview route exists in space.json, build manifests, or the catalog. → **publish-then-verify fallback**: publish the staged build to the existing public URL (blanket permission covers it), then run the live-verification gate against the public URL; on fail, repair and re-share, or unshare if badly broken.
- Cycle 2 prep started (design-only, no artifact edits): starter quest chain + guided Tidewatch Enclave opening + real quest log with objectives/rewards, brief lands in `goals/godot-rpg-gut-add-on-install/hidden_files/cycle2-prep/`.

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

## 10. Cycle 2 progress (2026-09-21, 12-hour authorized session)
- LIVE GATE RESULT (public URL, ~00:33–01:05 PDT): Balance Lab 115/115 (new baseline; 52/52 retired). Gate battle WON in ~20 min (HPs observed 61/53/48 — retune confirmed live, all allies survived). Retreat clean ("Retreated safely · party condition preserved" → world map, no soft-lock, HP snapshot restored on re-entry). Controls/touch PARTIAL (desktop viewport only; legal-tile highlighting + named target buttons + enemy sprite taps all good). DUNGEON INTERIORS FOUND UNBUILT: no walkable Undercroft interior — only "Descend to the Relic Strata" (map layer) and "Training expedition" (arena); the "five seven-room Underworld dungeons" were never built. Classic-combat random encounter + Save/Export UNVERIFIED (no encounter fired; no Save/Export found in game menu/Settings). Decision: published build stays live; top priority = real Drowned Undercroft interior; Cycle 2 continues; no publish until interior live-verified in draft.
- Cycle 2 staged UNPUBLISHED (draft only): Edit A quest-log model + event bus + v19→v20 migration + Sparkline mirroring + guided-opening init; Edit B quest-log UI renderer (tabs/pin/claim via confirm modal, field-journal mirror, NPC !/? badges) + 5 procedural portrait speakers × 3 expressions + Beat 3 teal-pulse door lighting; Edit C beats 1–6 dialogue + objectives + env, landing-skirmish 4v4 tactical set-piece, coast classic-encounter gating (quiet until landing_skirmish_won for guided saves), coast-road guard NPC, Undercroft gate reachability on undercroft_gate_open (q_salt04 turn-in), "Open Water" epilogue (Contract Board tier-1, Kagehira Vale marker); Edit D Drowned Undercroft interior (LOCATION-PANEL fallback, not walkable tiles): 6 spaces (Vestibule/Tidal Gallery/Cistern/Sunken Reliquary/optional Inked-Out Chamber/Sanctum), entry gated on undercroft_gate_won, 2 tactical room fights (2× tide-sentinel ~36/39 HP; tide-sentinel ~42 + strata-wisp ~36), 3 one-shot chests (Tideglass Vial, Strata Coil, Æ60), tide→moon→root sluice puzzle (reset-safe), Severed Thread boss ~130 HP with 3 verbatim mystery lines, retreat/wipe/withdraw no-soft-lock. DROWNED_UNDERCROFT_ROOMS corrected 4→2 in quest-log.js. New enemy ids: tide-sentinel, strata-wisp (drowned Veyari caretaker automata, NOT Changed).
- Node tests: quest-log 25/25, quest-log-ui 10/10, portraits 6/6, interior-logic 24/24 (all node-verified, NOT live-verified).
- Pending: live re-verification of the draft (interior walkthrough room-by-room + Step E classic-combat/Save-Export items) before any publish request.
- Parked for Brent: Hollow "wakes up" wording, "Glasshaven 96", Severed Thread nature, Mira Tuned-rumor, Technician's Spanner id, skip-opening toggle.

## Cycle 3 Edit 2 — jobs integration (2026-09-21 ~03:50 PDT) — COMPLETE, STAGED, NOT PUBLISHED

Draft export: relicbound-cycle3-jobs-draft.html (1,147,537 bytes; +47.9 KB vs Edit 1).
Public URL still serves the previously approved build (current_build_published=false).

Source audit (decoded 7 base64 modules) — all markers PASS:
- RelicJobs API integrated (144 refs): migrateV20ToV21Jobs, normalizeJobs, getJobStatus.
- JP economy: JP_CLASSIC=8, JP_TACTICAL=12, reserves earn half (rounded down).
- Job UI present (Crew menu, Drill Yard teaching, 51 x 44px control refs).
- Real Soren owner lock: checkOwnerLock('soren') === (S.identity==='abbiecakes'); tested locked-as-Brent / open-as-Abbie.
- Haggler: jpCost 40, Tidewatch-only 10% discount via Math.floor(base*.9) (test: 101 -> 90).
- Battle-menu routing by menu/modes/battleReady (weave vs Arts labels).
- Migration: migrateV20ToV21 calls normalizeTidewatch + guarded RelicJobs.migrateV20ToV21Jobs, bumps v to 21, registered in saveMigrations chain 10->21.
- Prior systems preserved: severed-thread hp:130, clearBoss, DROWNED_UNDERCROFT_ROOMS=2, undercroft_gate_open via q_salt04 onTurnIn only, q_salt05 chain, undercroft_gate_won entry check.
- Canon constraints: no player-facing "100+ hours" (2 hits are comments documenting the rule), no "chapter", no Asterwake, no Hollow-awakening language, no Godot.

QA note: the 2026-09-21 "progression bug" report was diagnosed as navigation error —
"Locked · visit Breaker's Rest" is a fast-travel lock (unrelated to the undercroft);
"Descend to the Relic Strata" is the legacy underworld descent, not the undercroft;
"the second door" is Toma's flavor line; the only entry is q_salt04 turn-in -> gate battle.
No draft fix was needed. Re-test path written for the parent.

## 12-hour session — Cycle 4 (2026-09-21 ~04:00 PDT) — IN PROGRESS, DRAFT ONLY

Session: Brent authorized a 12-hour dev session ~23:30 PDT 2026-09-20 (runs to ~11:30 PDT 2026-09-21). Blanket permission covers build/edit/test/publish to the EXISTING public URL only. NOT covered: new share links, anything customer-facing sent as Brent, paused threads (assistance/aid, PocketMCP AI-client, I'm Not a Robot).

Draft inspection finding (CODE-VERIFIED, this cycle): the Tidewatch enterable-buildings UI shell is staged but its entire DATA LAYER is missing — travelList, districtDefById, districtUnlocked, buildingDefById, SHOP_STOCK, findShopItem/buyItem/sellItem, SELL_RATE, UNDERDOCKS, underDocksCanEnter, openUdChest, inspectUdLore, guildBoardSummary, guildHasTurnInReady, acceptContract, turnInContract, getTrainerStock, normalizeTidewatch, TIDEWATCH_NAME_DEFAULTS, tidewatchName, setTidewatchName, resetTidewatchNames are all referenced but never defined. Clicking "Explore Tidewatch" throws ReferenceError; shops, guild contracts, and under-docks are dead. Fix dispatched as artifact.edit (Cycle 4): full data-layer implementation (4 districts, 6 buildings incl. inn Rest, 3 guild delivery/skirmish contracts, under-docks 3-space sub-area with skirmish/chest/lore, resident renames, Haggler discount preserved) + main-story beat-card fix (renderChronicle mirrors the quest log — "Main story · Journey I · Salt and Signal · step N of 6" — when q_salt quests exist; legacy mainStoryBeats retained for pre-v20 saves). Awaiting builder handoff.

Queued: Cycle 5 jobs deepening brief (3 tier-3 jobs: hollow-diver, bellkeeper, signal-thief + mastery attunement 100 JP sink) and Cycle 6 geography foundations brief (continents/seas data model, world→region→local zoom, era indicator) written to goals/godot-rpg-gut-add-on-install/hidden_files/.

Boss-flow live re-verification: parent is running a live browser task against the draft (corrected path: quest log → q_salt04 → gate → interior → Sunken Reliquary → Sanctum → Severed Thread → q_salt05 + NPC !/? badges). Publish request only on PASS.

Parked for Brent (wrap-up): Hollow "wakes up" wording; "Glasshaven 96" typo?; Severed Thread boss Changed-entity?; Mira Tuned-rumor line; Technician's Spanner starting gear; skip-opening toggle; Balance Lab QA debug panel (flag setters + teleport).
