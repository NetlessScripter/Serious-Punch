--[[
================================================================================
  EXTRACTION ANALYSIS — WHAT THIS FILE IS, WHERE IT CAME FROM, WHAT'S MISSING
================================================================================

  SOURCE
  ------
  This file was stripped out of tsb_interpreter.txt — the full TSB (The
  Strongest Battlegrounds) LocalScript, deobfuscated from bytecode.

  The full interpreter is ~149,235 lines. This extracted file is ~6,470 lines
  (~4.3% of the original). Everything in here maps directly back to that source
  with variable names preserved exactly as the deobfuscator produced them.

  The full script is a single monolithic LocalScript that handles:
    - All client-side VFX for every move in the game (via the v12 dispatcher)
    - Character selection UI and character-swap logic
    - Spectator camera for RankedOnes mode with FPS tracking
    - WindShake grass/tree animation (WindShake module, PC only)
    - VoxelDestruction terrain fracturing
    - Map leaderboard tween (vote rating display)
    - Chat VIP prefix injection (OnIncomingMessage hook)
    - Snow particle follower (tracks camera position each frame)
    - Cloud cover tween (fades clouds in/out on ChildAdded/Removed signals)
    - Reset button override and teleport handler (BindableEvent/Function)
    - CharacterHandler watchdog with anti-tamper kill switch
    - Ambient FPS measurement loop (shared.AverageFPS)
    - Part recycling pool (75 pre-seeded "starterdeb" parts in workspace.Thrown)

  Only three effect blocks were kept for this extraction:
    - "Last Breath New"  — the target effect
    - "Ground Crater"    — called internally by v471 (debris spawner)
    - "Camshake"         — called internally by various sub-effects

================================================================================
  WHAT WAS STRIPPED AND WHY IT MATTERS
================================================================================

  v6  (interpreter lines 1-9)
      Vararg array-copy helper used by the original bytecode VM shim.
      Safe to strip — no code in this file calls it.

  script.Parent = nil + StarterPlayer self-cleanup  (interpreter lines 10-14)
      Hides the script from the explorer and destroys any duplicate of itself
      in StarterPlayer at startup. Irrelevant for standalone use.

  v15=require(Info) / v16=require(Util) / v17=require(MeshFlipbooks)
  l_Cutscenes_0 = ReplicatedStorage.Cutscenes  (interpreter lines 22-25)
      Module dependencies used by other effects in v12. Stripped because Last
      Breath New does not reference them directly. However v16 (Util) IS listed
      in the original v12 upvalue comment — if other stripped effects were ever
      restored here, v16 would need to come back too.

  v19  (interpreter lines 26-83)  +  v25()  (interpreter lines 84-94)
      Runtime string obfuscation system. v19 is a ~57-entry table of fruit and
      word strings. v25() concatenates selected entries to reconstruct workspace
      folder names like "FallenParts", "InfiniteHeight", "Baseplate" at runtime
      without spelling them plainly in source. Completely stripped — this file
      refers to workspace folders by direct name instead.

  v29 = require(VoxelDestruction)  (interpreter line 98)
      Terrain voxel fracturing module used by effects that shatter the map.
      Not needed for Last Breath New.

  l_LegacyReplication_0 = Resources.LegacyReplication  (interpreter line 99)
      Folder of legacy replication scripts (e.g. the Death screen LocalScript).
      Used by the "Death Screen" effect in v12. Stripped.
      NOTE: Still appears in the v12 upvalue comment in the original (line 6848),
      meaning if v12 were ever expanded with other effects here, this would need
      to be declared.

  v34 = nil  (interpreter line 107)
      Character selection state variable. Used by the reset BindableFunction to
      tell the server which character the player wants after dying. Stripped.

  v35  (interpreter lines 108-154)
      Movement-direction animation table (Idle/Go/Back/Left/Right etc. with
      Vector3 direction + animation ID). Used by locomotion effects. Stripped.

  v36 = WindShake  (interpreter lines 155-164)
      WindShake module that animates grass and trees on the map. Only loads when
      S_FastMode is off and the device is not touch-enabled (PC only). Stripped.

  Ranked spectator camera  (interpreter lines 165-284)
  FPS tracker / shared.AverageFPS  (interpreter lines 286-321)
  Map leaderboard tween  (interpreter lines 322-356)
      All three are pure UI/camera systems unrelated to VFX. Stripped.

  v106 = {}  (interpreter line 386)  →  became  local _ = {}  in extracted
      Terrain attachment cleanup dictionary. Keys are Attachment or SmokeBack
      instances; values are tick() timestamps. Any entry older than 15 seconds
      is destroyed on the next v112 ChildAdded fire.
      The extracted script reduced this to anonymous _ and discarded it, which
      is why v112's callback body was also stripped — it had no v106 to work
      with. See v112 note below.

  v132 = workspace.Thrown.ChildAdded:Connect(...)  (interpreter lines 405-584)
      A substantial listener handling special-named parts dropped into
      workspace.Thrown by the server. Cases handled:
        "QuickWind"       — tweens a wind-ring part outward then fades it
        "QuickSlashMesh"  — scales a slash mesh SpecialMesh via tween
        "AdjustStabby1-4" — repositions a stab Model to the target's HRP
                            using four separate hardcoded offset CFrames
        "CleaveBruh"      — orients a cleave Model to the target's primary part
      Also tags every incoming BasePart with a "Spawn" tick() attribute (used
      by v301 for age-based cleanup) and registers Attachments/Welds into v106
      for TTL cleanup. Stripped entirely — irrelevant to Last Breath New.

  v294 = tick()  (interpreter line 1813)
      Cooldown timestamp for v301's expensive full-sweep mode. v301 checks
      `tick() - v294 > 300` to decide whether to scan all of workspace.Thrown.
      *** LIVE BUG: v294 was stripped but v301 still references it. Any call
      to v301 will crash with "attempt to perform arithmetic on a nil value".
      This fires every time v471 runs its debris loop via the pattern
      `v301() or Instance.new("Part")`.
      FIX: add  local v294 = tick()  near the top globals section.

  75 pre-seeded "starterdeb" parts  (interpreter lines 1857-1865)
      At startup the full script creates 75 anchored Parts at CFrame(1e8,1e8,1e8)
      and inserts them into v107, pre-warming the recycling pool so early effect
      calls never pay the Instance.new("Part") cost. Stripped. Without them v301
      always falls through to Instance.new("Part") on every debris call.

  v472 = { workspace.Live, workspace.Thrown }  (interpreter line 4226)
      Default RaycastParams exclude-list used by v717 when no Whitelist or
      Ignore is provided in the opts table. Grows further as more character
      folders are added (table.insert calls at lines 4311, 4314, 4914, 4917).
      *** LIVE BUG: v472 was stripped but v717 still references it as an
      upvalue. When called without opts.Whitelist or opts.Ignore,
      FilterDescendantsInstances is set to nil — the raycast hits everything.
      FIX: add  local v472 = { workspace.Live, workspace.Thrown }  near the
      RaycastParams/v31 declaration at the top of the globals section.

  v1089 = 0  (interpreter line 6846)
      A call counter incremented on every v12 invocation in the original.
      Stripped — listed as a v12 upvalue but unused in the three kept effects.

  v306  (interpreter line 1866)
      Large per-character VFX data table (color, flipbook frame folder, scale,
      size). Used by character-specific visual effects. Stripped.

  All other v12 effect blocks
      The full interpreter v12 handles dozens of named effects beyond the three
      kept here (Death Screen, Barrage Finisher Done, and many others). All
      stripped. The if/elseif chain in v12 simply falls through to the
      Camshake block for any unrecognised Effect string.

  shared.repfire = v12  (interpreter line 148122)
      Set near the very end of the full script (line 148122), long after v12 is
      fully defined. Never added to this extracted file, so shared.repfire is nil
      here unless something external sets it. Any internal call to
      shared.repfire() from within v471 or sub-effects will silently do nothing
      since there is no stub for it (unlike SetCore/addshake/CutsceneEvent).

  Cloud cover tween / Reset BindableEvent+Function / CharacterHandler watchdog
  Snow particle follower  (interpreter lines 149078-149234)
      End-of-script lifecycle systems. None affect Last Breath New.
      The CharacterHandler watchdog is notable: it monitors each character for a
      CharacterHandler.Client script, destroys the character if Client is
      disabled, and contains a deliberate `safasfasfasf()` crash call if
      RunContext changes to "Server" — an anti-exploit kill switch. Inside v12
      the full interpreter pre-defines safasfasfasf as an empty function
      (lines 6851-6854) to neutralize this in the effects context. That stub is
      NOT present in the extracted script because the whole v12 body opens
      differently here.

================================================================================
  OBSERVATIONS ABOUT WHAT REMAINS IN THIS FILE
================================================================================

  DUPLICATE v10 DECLARATION  (extracted lines 220-221)
      `local v10 = game:service("TweenService")` appears twice back-to-back.
      The second silently shadows the first. Deobfuscation artifact — two
      SETGLOBAL bytecode ops landed on the same name. No runtime harm.

  v112 CALLBACK BODY IS EMPTY  (extracted lines 226-227)
      The original body tracked Terrain Attachments and "SmokeBack" parts in
      v106 with a 15-second TTL, destroying stale ones on each ChildAdded fire.
      Because v106 was stripped (became _), the body could not be preserved
      and was emptied. The connection itself exists but does nothing. v112 is
      never referenced again and is never disconnected — a minor connection
      leak, but harmless at runtime.

  OnIncomingMessage VIP CHAT HOOK  (extracted lines 249-258)
      Completely unrelated to VFX. Injects a gold "[VIP]" rich-text prefix into
      chat messages for players with the RealVIP attribute set but not
      S_HiddenVIP. It was bundled into the same LocalScript in TSB's architecture
      and came along during extraction. Safe to leave in or remove.

  v718 = math.random(1, 100)  (extracted line 1253)
      A random integer generated immediately after v717 is defined. Never read
      or used anywhere else in this file. Likely a stripped feature trigger
      (e.g. a rare visual variant chance gate) that lost its conditional body
      during deobfuscation.

  BoatTween MODULE  (extracted line 1258)
      v955 uses require(game.ReplicatedStorage.BoatTween) rather than raw
      TweenService. BoatTween is a well-known open-source Roblox tween library
      with spring physics and extra easing styles beyond vanilla TweenService.
      The v955 wrapper plays the tween, fires an optional callback on Completed,
      then destroys it — with a task.delay failsafe to guarantee cleanup even if
      Completed never fires.

  QuickWeldNew IS A BARE GLOBAL  (extracted line 1673)
      Unlike every other function in this file, QuickWeldNew has no `local`.
      It goes into the Lua global environment (_G), accessible from any other
      LocalScript in the same VM. This is intentional — the full TSB codebase
      calls QuickWeldNew cross-script without require().

  shared.* STUBS  (extracted lines 1707-1709)
      shared.SetCore, shared.addshake, and shared.CutsceneEvent are guarded with
      `or function() end`. In the full game these are set by other LocalScripts
      that run first. The stubs prevent nil-call crashes when this file runs
      standalone. shared.repfire is NOT stubbed — see the stripped section above.

  v12 USES rawget FOR EFFECT KEY  (extracted line 1715)
      `rawget(v1090, "Effect")` bypasses any __index metamethod on the payload
      table. Defensive coding — prevents a crafted table from spoofing the effect
      name via a metatable.

  v7292 = nil  (extracted line 1724)
      Declared at the Last Breath New block's outer scope and never assigned.
      Appears as an upvalue in several inner closures. Likely a state flag from
      the original (e.g. a running AnimationTrack reference) that was stripped
      when the outer character-loop context was removed during extraction.

  v7303 = 4  (extracted line 1734)
      Base wave/phase count for the shockwave ring sequence. Controls how many
      expanding ring iterations the Last Breath New effect produces. Changing
      this value directly scales the ring count.

  SOUND ASSET POOLS  (extracted lines 1736-1859)
      Last Breath New carries several large tables of audio asset IDs:
        unnamed pool  — 25 general hit/impact sounds
        v7306.Dash    — 17 dash whoosh sounds
        v7306.RunWind — 16 running wind sounds
        v7306.Wind    — 16 wind ambient sounds
        v7306.Ember1  — 21 ember/crackle sounds
        v7306.Ember2  — 16 entries (mirrors the Dash pool)
        v7307         — 25 additional sounds
        v7308         — 25 additional sounds
      A random entry is selected per play via v95 or v7302. This prevents the
      same sound from repeating identically across invocations.

  IDs FOLDER IS DATA-DRIVEN  (extracted line 1735)
      l_IDs_0 = l_LastBreath_0.IDs — animation and sound IDs live in a Folder
      inside ReplicatedStorage.Resources.LastBreath.IDs rather than hardcoded
      inline. The effect indexes into this folder by attribute name at runtime,
      so IDs can be updated in the asset folder without touching script logic.

  TWO SEPARATE RANDOM STRATEGIES  (extracted lines 1733 vs ~414)
      v7302 = Random.new() — fresh unseeded instance created per Last Breath New
      invocation. Visual jitter (particle offsets, timing variance) differs on
      every play. Intentional aesthetic variation.
      v366 = Random.new(v363.Seed) inside v471 — seeded with v1090.Seed, making
      crater tile placement identical across all clients for the same impact.
      These two strategies serve different goals: v7302 for variety, v366 for
      cross-client determinism.

  v471 STADIUM SPECIAL-CASE  (extracted lines 425-430)
      If the ground part is named "Stadium" and lacks a "FakeStadium" attribute,
      v471 makes a recursive call: v12({Effect="Break Model", Stadium=...}).
      "Break Model" is not one of the three kept effects — this call will fall
      through the if/elseif chain and silently do nothing in this file.

  v301 PART RECYCLING POOL  (extracted lines 1826-1851)
      The pattern `v301() or Instance.new("Part")` is used throughout v471.
      v301 has two modes depending on time elapsed:
        Full sweep (if > 300s since last — needs v294 which is missing/bugged):
          Scans workspace.Thrown and destroys all BaseParts with a "Spawn"
          attribute older than 60 seconds.
        Normal mode (otherwise):
          Reclaims the first entry in v107, resets its CollisionGroup and Spawn
          attribute, and returns it for reuse as a debris Part.
      Without the 75 pre-seeded parts (stripped) the pool starts empty and every
      debris call falls back to Instance.new() until parts start returning.

  if v1090.hit then — DUAL-PURPOSE GATE  (extracted line 2174)
      This single field controls two separate things:
        1. Gates the entire Last Breath New visual block. Without it, the effect
           fires nothing at all. This was the root cause of Fix 2.
        2. On line 2176 it is compared against LocalPlayer.Character. If hit IS
           the local player, camera/UI side effects fire (SetCore, sound
           parenting). If it is someone else's character, only the visual VFX
           runs without touching the local camera or UI.
      Passing hit = character satisfies both: it unlocks the block AND correctly
      routes camera effects to the local player.

================================================================================
  REUSE GUIDE — EXTRACTING OTHER EFFECTS FROM tsb_interpreter
================================================================================

  This file is the canonical reference for helper functions shared across
  all effects in the interpreter. When you extract a different effect block
  from tsb_interpreter.txt and it depends on support systems, DO NOT re-derive
  or rewrite those systems from the interpreter. Come back here and copy them
  directly from this file instead.

  The following helpers are already clean, fixed, and ready to paste:

    Ground Crater   — the full "Ground Crater" elseif block inside v12
                      (near the bottom of this file). Handles raycast-based
                      ground detection, smoke/dust particles, colour matching,
                      and calls v471 for tile/debris scatter.

    Camshake        — the full "Camshake" elseif block inside v12
                      (at the very bottom of this file). Handles single-shot
                      and decayed-loop camera shake via shared.addshake.

    v471            — ground impact and debris scatter spawner. Called by many
                      effects that hit the ground. Includes tile ring logic,
                      small debris physics, sound pools, and Stadium detection.

    v313 / shared.sfx — sound factory. Creates, configures, plays, and
                        auto-destroys a Sound instance. Used by nearly every
                        effect.

    v327            — CFrame rotation helper (vecA onto vecB via cross-product).
                      Used to orient VFX correctly against surface normals.

    v338            — GetTouchingParts workaround. Needed anywhere tile/debris
                      overlap detection is used inside v471.

    v717 / shared.ray — raycast helper. Supports both Raycast and Blockcast,
                        Whitelist and Ignore modes.

    v955            — BoatTween wrapper. Use anywhere a tween with a completion
                      callback is needed.

    v983            — ZOffset adjuster for layering ParticleEmitters.

    v989            — Lifetime scaler for speeding up or slowing down particles.

    v1000           — FX cloner. Clones an asset, pivots it to an Anchor CFrame,
                      parents to workspace.Thrown, and schedules Debris cleanup.

    v1014           — ParticleEmitter activator. Respects EmitCount/EmitDelay/
                      EmitDuration attributes and handles Beam on/off tweens.

    v1021           — Particle toggle (bulk enable/disable all emitters in an FX).

    v1034           — Mesh flipbook animator. Cycles an ImageValue folder through
                      a Decal at a given FPS.

    v1058           — Asset loader and Maid factory. Use this for any effect that
                      needs tracked clones and automatic cleanup after N seconds.

    QuickWeldNew    — Welds an FX Model to a part with Maid registration.
                      Available globally (no require needed).

  PROCEDURE
  ---------
  1. Open tsb_interpreter.txt and find your target effect block (search for
     its Effect string, e.g. "Barrage Finisher Done").
  2. Copy the elseif block and paste it into the v12 if/elseif chain in this
     file, before the Ground Crater block.
  3. Check the new block's upvalue comment at the top of the elseif. Any
     upvalue already present in this file can be used as-is. Any upvalue NOT
     present here must be located in the interpreter and added to the globals
     section of this file.
  4. Do NOT copy helper functions from the interpreter if they already exist
     here — the versions in this file have fixes applied (e.g. the v112 body
     fix) that the raw interpreter does not.
  5. Remember to add the two missing globals that affect any effect using
     ground or raycast logic:
       local v294 = tick()                              -- v301 sweep timer
       local v472 = { workspace.Live, workspace.Thrown } -- v717 default filter

================================================================================
]]

--[[
================================================================================
  LAST BREATH NEW — FULL EXTRACTED BLOCK + SUPPORT SYSTEMS
  Source: TSB interpreter (v12 effects dispatcher)
================================================================================

  DISPATCHER CONTEXT
  ------------------
  v12(v1090)       The main effects dispatch function. v1090 is the payload
                   table passed in by the server/replication layer.
  v1091            = rawget(v1090, "Effect")  — the effect name string.
                   This block fires when v1091 == "Last Breath New".

  v1090 PAYLOAD FIELDS (Last Breath New)
  ----------------------------------------
  v1090.char       → The target player's Character (Model)
  v1090.id         → Numeric move/effect ID used to index animation/sound IDs
  v1090.second     → Boolean; true = second phase of the move
  v1090.Effect     → "Last Breath New" (consumed as v1091 above)

  v1090 PAYLOAD FIELDS (Camshake — support system)
  --------------------------------------------------
  v1090.Intensity      → Number; strength of the camera shake passed to
                         shared.addshake(). Decays by ×0.9 per tick when
                         v1090.Last is set.
  v1090.Last           → Number (seconds); if set, runs the shake in a loop
                         for (Last / 0.03) ticks at 0.03s intervals, decaying
                         intensity each tick. Must be a number — if it's any
                         other type the block nils it out and falls back to a
                         single-shot shake.
  v1090.IgnoreReduced  → Boolean; passed straight to shared.addshake as the
                         second arg. When true, skips any "reduced shake"
                         quality setting on the client.
  v1090.OnScreenOnly   → Boolean; when true, the Camshake block checks whether
                         v1090.RealPart is on screen before doing anything.
                         If the part is off-screen it returns early (no shake).
  v1090.RealPart       → Model with a PrimaryPart; the world-space anchor used
                         by the OnScreenOnly check. Its PrimaryPart.Position is
                         fed to WorldToScreenPoint.

  v1090 PAYLOAD FIELDS (Ground Crater — support system)
  -------------------------------------------------------
  v1090.start      → Vector3 or Instance (Attachment/BasePart); origin of the
                     downward raycast. If an Instance, the block resolves it to
                     WorldPosition / Position automatically.
  v1090["end"]     → Vector3; direction vector for the ground-detection raycast
                     (typically pointing downward).
  v1090.Seed       → Number; seeds the local Random so craters are deterministic
                     across clients.
  v1090.Sound      → Sound opts table; if present, plays a positional sound at
                     the crater hit position via shared.sfx().
  v1090.nosmoke    → Boolean; suppresses the smoke/dust particle burst.
  v1090.smoked     → Boolean; forces the Attachment to parent to
                     workspace.Terrain regardless of whether the hit part is
                     anchored.

  GLOBAL SERVICES (defined at script top-level)
  -----------------------------------------------
  v10              game:service("TweenService")
  v9               game:service("CollectionService")
  v138             game:service("UserInputService")
  l_LocalPlayer_0  game.Players.LocalPlayer
  l_Debris_0       game:GetService("Debris")
  v31              RaycastParams (filtered to workspace.Thrown + workspace.Live)
  v107             {} — active/tracked BasePart cleanup list (workspace.Thrown)
  v95              Random.new() — shared global Random instance

  SHARED GLOBALS (set on the Roblox `shared` table by other LocalScripts)
  -------------------------------------------------------------------------
  shared.OnScreen(position: Vector3) → boolean
      Defined in a SEPARATE LocalScript (not present in this file).
      Internally calls:
          local _, onScreen = workspace.CurrentCamera:WorldToScreenPoint(pos)
          return onScreen
      Returns true if [position] is within the camera's viewport.
      Used as a culling gate — effects skip expensive VFX when the action
      is off-screen.

  shared.addshake(intensity: number, ignoreReduced?: boolean)
      Defined in a SEPARATE LocalScript (not present in this file).
      Applies a camera shake of the given intensity to the local client.
      The Camshake effect block calls this directly (single-shot) or in a
      decay loop when v1090.Last is set.

  shared.sfx(opts)          Alias for v313 — see v313 below.
  shared.ray(opts)          Alias for v717 — see v717 below.
  shared.loop(fn, fps)      Alias for v780 — see v780 below.
  shared.repfire            Alias for v12 (the dispatch function itself).
  shared.resetbe            BindableFunction for the Reset button callback.

  UPVALUE UTILITY FUNCTIONS
  --------------------------
  v102(min, max, isInt?, rng?)
      Random number helper. Wraps v95 (or a passed-in Random).
      Returns NextNumber or NextInteger depending on isInt flag.

  v301()
      Cleanup sweep. Destroys old stale parts in workspace.Thrown
      (those older than 60 s) and trims v107 if it exceeds 100 entries.

  v313(opts)
      Sound factory. Creates a new Sound, assigns SoundGroup to
      SoundService.Sounds, tags it via CollectionService "NewSound",
      sets all opts fields on it, plays it, and auto-destroys on Ended.
      Exposed as shared.sfx.

  v327(vecA, vecB, fallbackAxis)
      CFrame rotation helper. Returns a CFrame that rotates vecA onto vecB
      (cross-product quaternion method). Used for orienting VFX.

  v338(part)
      GetTouchingParts workaround. Briefly connects a no-op Touched event
      so Roblox populates the touching list, then disconnects and returns
      the filtered list (excluding "InvisibleBorder" / "InfBall").

  v471(opts)
      Ground impact / debris scatter spawner. Reads opts.cframe, opts.ground,
      opts.sizemult, etc. Spawns tile/debris chunks and calls v12 internally
      for sub-effects (e.g. "Ground Crater").

  v717(opts)
      Raycast helper. Accepts opts.orig, opts.dir, opts.Whitelist/Ignore,
      opts.Blockcast. Returns (instance, position, material, normal).
      Exposed as shared.ray.

  v741(part, axis)
      Splits a BasePart in half along the given Enum.Axis.
      Returns (original, half1, half2).

  v755(part, times)
      Recursively fractures a part [times] times using v741.
      Destroys the original and re-parents all fragments to original parent.

  v780(fn, fps, sync)
      Fixed-rate loop runner. Calls fn(stopFn) at [fps] Hz via RenderStepped.
      Returns a stop function. sync=true runs synchronously instead of spawned.
      Exposed as shared.loop.

  v880(partsOrModel, relativeCF?)
      Bounding box calculator. Returns min/max extents of all BaseParts
      relative to an optional CFrame. Used for sizing impact effects.

  v955(obj, tweenInfoTable, callback?)
      Lightweight TweenService wrapper. Creates and plays a tween from a
      plain table (tweenInfoTable.Time, .EasingStyle, .Goal, etc.),
      fires optional callback on complete, then auto-destroys the tween.

  v983(opts)
      Adjusts ZOffset on every ParticleEmitter inside opts.FX by opts.Count.
      Used to layer VFX on top of each other correctly.

  v989(opts)
      Scales Lifetime (Min and Max) on every ParticleEmitter in opts.FX
      by opts.Scale. Used to speed up or slow down particle effects.

  v1000(opts)
      Clones opts.FX and pivots/positions it to opts.Anchor (a CFrame).
      Parents to workspace.Thrown and calls Debris:AddItem(clone, 30).
      Returns the cloned instance.

  v1014(model, debrisTime?, opts?)
      Activates all ParticleEmitters in [model] (respects EmitCount,
      EmitDelay, EmitDuration attributes). Also tweens Beams on/off using
      their Width0/Width1 attributes. Optionally calls Debris:AddItem.

  v1021(opts)
      Toggles all ParticleEmitters in opts.FX. opts.On=true enables them,
      false/nil disables them.

  v1034(opts)
      Mesh flipbook animator. Cycles textures stored in a Folder of
      ImageValues through a Decal on opts.Mesh at opts.FPS.
      opts.Repeat controls loop count. Auto-destroys mesh when done
      unless opts.DWC == false.

  v1058(assetsFolder, maidTable, debrisTime?)
      Asset loader + Maid factory — SEE FULL FUNCTION BELOW.
      Returns: assetProxy (v7297), maidObject (v7298), cleanupFn.
      • assetProxy  — metatable proxy; index by asset name to get a table
                      with :Clone(), :GetChildren(), :GetAttribute().
                      :Clone() auto-registers the clone in maidTable.
      • maidObject  — { _maid = { give=fn, giveTask=fn } }
                      _maid.give(instance)     registers an Instance or
                      RBXScriptConnection for cleanup.
                      _maid.giveTask(task)     same but for task handles.
      • cleanupFn   — called automatically after debrisTime seconds.
                      Disconnects all connections, destroys all instances,
                      and clears all internal tables.
      In Last Breath New: v1058(l_LastBreath_0, v7294, 25)
          l_LastBreath_0 = ReplicatedStorage.Resources.LastBreath (asset folder)
          v7294          = {} (the maid tracking table)
          25             = auto-cleanup after 25 seconds
          → v7297 = asset proxy  |  v7298 = maid object

  LOCAL VARS (Last Breath New block entry)
  -----------------------------------------
  l_char_26        = v1090.char           → target Character Model
  l_id_8           = v1090.id             → effect/animation ID index
  l_PrimaryPart_49 = l_char_26.PrimaryPart → character HumanoidRootPart
  l_CurrentCamera_8= workspace.CurrentCamera
  l_LastBreath_0   = game.ReplicatedStorage.Resources.LastBreath  (assets folder)
  v7294            = {}  maid tracking table (passed into v1058)
  v7297, v7298     = v1058(l_LastBreath_0, v7294, 25)
                     v7297 = asset proxy table  |  v7298 = maid object
  l_TweenService_26= game:GetService("TweenService")
  l_Thrown_23      = workspace.Thrown
  l_wait_19        = task.wait  (local alias)
  v7302            = Random.new()  (local random for this effect)
  v7303            = 4  (base wave/phase count)
  l_IDs_0          = l_LastBreath_0.IDs  (animation/sound ID folder)

================================================================================
]]


-- ============================================================
-- REQUIRED SERVICE GLOBALS
-- ============================================================
local v9 = game:service("CollectionService");
local v10 = game:service("TweenService");
local v10 = game:service("TweenService");
local _ = game:GetService("PhysicsService");
local v95 = Random.new();
local _ = {};
local v107 = {};
local v112 = workspace.Terrain.ChildAdded:Connect(function(v108) --[[ Line: 358 ]]
end)

-- ============================================================
-- v102  Random number helper
-- ============================================================
local function v102(v97, v98, v99, v100) --[[ Line: 322 ]]
    -- upvalues: v95 (copy)
    local v101 = v100 or v95;
    if not v98 and v97 then
        v98 = v97;
        v97 = 1;
    end;
    if not v98 and not v97 then
        v97 = 0;
        v98 = 1;
    end;
    if not v99 then
        return v101:NextNumber(v97, v98);
    else
        return v101:NextInteger(v97, v98);
    end;
end;
game:GetService("TextChatService").OnIncomingMessage = function(v103) --[[ Line: 341 ]]
    local l_TextChatMessageProperties_0 = Instance.new("TextChatMessageProperties");
    if v103.TextSource then
        local l_PlayerByUserId_1 = game.Players:GetPlayerByUserId(v103.TextSource.UserId);
        if not l_PlayerByUserId_1:GetAttribute("S_HiddenVIP") and l_PlayerByUserId_1:GetAttribute("RealVIP") then
            l_TextChatMessageProperties_0.PrefixText = "<font color=\"rgb(255, 216, 19)\">[VIP]</font> " .. v103.PrefixText;
        end;
    end;
    return l_TextChatMessageProperties_0;
end;

-- ============================================================
-- v313 / shared.sfx  Sound factory
-- ============================================================
local function v313(v307) --[[ Line: 2142 ]]
    -- upvalues: v9 (copy)
    local l_Sound_0 = Instance.new("Sound");
    l_Sound_0.SoundGroup = game:GetService("SoundService").Sounds;
    v9:AddTag(l_Sound_0, "NewSound");
    if v307.AddTo then
        table.insert(v307.AddTo, l_Sound_0);
        v307.AddTo = nil;
    end;
    local v309 = nil;
    local v310 = nil;
    v310 = l_Sound_0.Ended:connect(function() --[[ Line: 2153 ]]
        -- upvalues: l_Sound_0 (copy), v309 (ref), v310 (ref)
        if l_Sound_0 then
            l_Sound_0:Destroy();
        end;
        if v309 then
            v309:Destroy();
        end;
        return v310:Disconnect();
    end);
    if not v307.RollOffMaxDistance then
        v307.RollOffMaxDistance = 300;
    end;
    if v307.CFrame then
        v309 = Instance.new("Attachment");
        v309.Parent = workspace.Terrain;
        v309.WorldCFrame = v307.CFrame;
        v307.Parent = v309;
    end;
    v307.CFrame = nil;
    for v311, v312 in pairs(v307) do
        l_Sound_0[v311] = v312;
    end;
    return l_Sound_0, v309;
end;
shared.sfx = v313;

-- ============================================================
-- v327  CFrame rotation from two vectors
-- ============================================================
local function v327(v322, v323, v324) --[[ Line: 2202 ]]
    local v325 = v322:Dot(v323);
    local v326 = v322:Cross(v323);
    if v325 < -0.99999 then
        return CFrame.fromAxisAngle(v324, 3.141592653589793);
    else
        return CFrame.new(0, 0, 0, v326.x, v326.y, v326.z, 1 + v325);
    end;
end;
local _ = function(v328) --[[ Line: 2208 ]]
    local _, v330 = workspace:FindFirstChildOfClass("Camera"):WorldToScreenPoint(v328);
    return v330;
end;

-- ============================================================
-- v338  GetTouchingParts workaround
-- ============================================================
local function v338(v332) --[[ Line: 2213 ]]
    if not v332 then
        return;
    elseif v332.ClassName == "Model" then
        return;
    else
        local v333 = v332.Touched:Connect(function() --[[ Line: 2216 ]]

        end);
        local l_v332_TouchingParts_0 = v332:GetTouchingParts();
        v333:Disconnect();
        local v335 = {};
        for _, v337 in pairs(l_v332_TouchingParts_0) do
            if v337.Name ~= "InvisibleBorder" and v337.Name ~= "InfBall" then
                table.insert(v335, v337);
            end;
        end;
        return v335;
    end;
end;

-- ============================================================
-- v362  Tween helper (used by v471)
-- ============================================================
local function v362(v351) --[[ Line: 2259 ]]
    -- upvalues: v10 (copy), v95 (copy), v102 (copy)
    local l_v10_0 = v10;
    local l_v351_0 = v351;
    local l_new_0 = TweenInfo.new;
    local v355 = 0.7;
    local v356 = 1;
    local l_v95_1 = v95;
    if not v356 and v355 then
        v356 = v355;
        v355 = 1;
    end;
    if not v356 and not v355 then
        v355 = 0;
        v356 = 1;
    end;
    l_v10_0:Create(l_v351_0, l_new_0(l_v95_1:NextNumber(v355, v356), Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Size = Vector3.new()
    }):Play();
    v351.Anchored = false;
    v351.CanCollide = false;
    v351.CanTouch = false;
    l_v10_0 = v351:FindFirstChildOfClass("BodyVelocity");
    if l_v10_0 then
        game:GetService("Debris"):AddItem(l_v10_0, 0);
    end;
    local l_BodyVelocity_0 = Instance.new("BodyVelocity");
    l_BodyVelocity_0.MaxForce = Vector3.new(400000, 400000, 400000, 0);
    v355 = -10;
    v356 = 10;
    l_v95_1 = v95;
    if not v356 and v355 then
        v356 = v355;
        v355 = 1;
    end;
    if not v356 and not v355 then
        v355 = 0;
        v356 = 1;
    end;
    local v359 = l_v95_1:NextNumber(v355, v356);
    l_v95_1 = 10;
    local v360 = 17.5;
    local l_v95_2 = v95;
    if not v360 and l_v95_1 then
        v360 = l_v95_1;
        l_v95_1 = 1;
    end;
    if not v360 and not l_v95_1 then
        l_v95_1 = 0;
        v360 = 1;
    end;
    l_BodyVelocity_0.Velocity = Vector3.new(v359, l_v95_2:NextNumber(l_v95_1, v360) * 2, v102(-10, 10)) * 1.5;
    l_BodyVelocity_0.Parent = v351;
    game:GetService("Debris"):AddItem(l_BodyVelocity_0, 0.15);
end;

-- ============================================================
-- v471  Ground impact / debris scatter spawner
-- ============================================================
local function v471(v363) --[[ Line: 2277 ]]
    -- upvalues: v102 (copy), v12 (ref), v95 (copy), l_LocalPlayer_0 (copy), v301 (copy), v10 (copy), v107 (copy), v327 (copy), v338 (copy), v362 (copy), v313 (copy)
    if not v363.add then
        v363.add = {};
    end;
    local v364 = v363.sizemult or 1;
    local l_add_0 = v363.add;
    if v363.dispersefast and not shared.OnScreen(v363.cframe.Position) then
        return;
    else
        local v366 = Random.new(v363.Seed or tick());
        local l_v102_0 = v102;
        local function v370(v368, v369) --[[ Line: 2288 ]]
            -- upvalues: l_v102_0 (copy), v366 (copy)
            return l_v102_0(v368, v369, nil, v366);
        end;
        if v363.amount then
            v363.amount = math.floor(v363.amount);
        end;
        local v371 = l_add_0.less and 2.5 or 1;
        local v372 = nil;
        if v363.ground.Name == "Stadium" and not v363.ground:GetAttribute("FakeStadium") and not v363.notiles then
            v372 = v12({
                Effect = "Break Model", 
                Stadium = v363.ground
            });
        end;
        local v373 = 3;
        local v374 = 5;
        local v375 = v366 or v95;
        if not v374 and v373 then
            v374 = v373;
            v373 = 1;
        end;
        if not v374 and not v373 then
            v373 = 0;
            v374 = 1;
        end;
        local v376 = v375:NextNumber(v373, v374);
        if l_LocalPlayer_0:GetAttribute("S_FastMode") then
            v376 = v376 / 2;
        end;
        if v363.nodebris then
            v376 = 0;
        end;
        v373 = false;
        v374 = false;
        if v363.ground.Material == Enum.Material.Water or v363.ground == workspace.Terrain then
            v375 = {
                Material = Enum.Material.Sand, 
                Color = Color3.fromRGB(227, 206, 157), 
                Transparency = 0
            };
            v363.ground = v375;
            v375.GetAttribute = function(_) --[[ Line: 2323 ]] --[[ Name: GetAttribute ]]

            end;
            v373 = true;
            v374 = true;
        end;
        for _ = 1, v376 do
            if not l_add_0.less and not v374 then
                local v379 = 1;
                local v380 = 3;
                local v381 = v366 or v95;
                if not v380 and v379 then
                    v380 = v379;
                    v379 = 1;
                end;
                if not v380 and not v379 then
                    v379 = 0;
                    v380 = 1;
                end;
                local v382 = v381:NextNumber(v379, v380);
                v379 = v301() or Instance.new("Part");
                v379.CollisionGroup = "nocol";
                v381 = v363.cframe;
                local l_Angles_0 = CFrame.Angles;
                local v384 = -360;
                local v385 = 360;
                local v386 = v366 or v95;
                if not v385 and v384 then
                    v385 = v384;
                    v384 = 1;
                end;
                if not v385 and not v384 then
                    v384 = 0;
                    v385 = 1;
                end;
                local v387 = math.rad((v386:NextNumber(v384, v385)));
                v385 = -360;
                v386 = 360;
                local v388 = v366 or v95;
                if not v386 and v385 then
                    v386 = v385;
                    v385 = 1;
                end;
                if not v386 and not v385 then
                    v385 = 0;
                    v386 = 1;
                end;
                local v389 = math.rad((v388:NextNumber(v385, v386)));
                v386 = -360;
                v388 = 360;
                local v390 = v366 or v95;
                if not v388 and v386 then
                    v388 = v386;
                    v386 = 1;
                end;
                if not v388 and not v386 then
                    v386 = 0;
                    v388 = 1;
                end;
                v379.CFrame = v381 * l_Angles_0(v387, v389, (math.rad((v390:NextNumber(v386, v388)))));
                v379.Size = Vector3.new(v382, v382, v382);
                v379.Color = v363.ground.Color;
                v379.CanCollide = not v374;
                v379.Anchored = false;
                v379.Material = v363.ground.Material;
                v379.Transparency = v363.ground.Transparency;
                v379.Parent = workspace.Thrown;
                if v379.Material == Enum.Material.Neon then
                    v379.Material = Enum.Material.Concrete;
                end;
                v380 = Instance.new("BodyVelocity");
                v380.MaxForce = Vector3.new(2000000000, 2000000000, 2000000000, 0);
                v389 = -25;
                v384 = 25;
                v385 = v366 or v95;
                if not v384 and v389 then
                    v384 = v389;
                    v389 = 1;
                end;
                if not v384 and not v389 then
                    v389 = 0;
                    v384 = 1;
                end;
                v387 = v385:NextNumber(v389, v384);
                v384 = 5;
                v385 = 25;
                v386 = v366 or v95;
                if not v385 and v384 then
                    v385 = v384;
                    v384 = 1;
                end;
                if not v385 and not v384 then
                    v384 = 0;
                    v385 = 1;
                end;
                v380.Velocity = Vector3.new(v387, v386:NextNumber(v384, v385), v370(-25, 25)) * 2;
                v380.Parent = v379;
                game:GetService("Debris"):AddItem(v380, 0.15);
                v381 = delay;
                if v374 then
                    l_Angles_0 = 0;
                else
                    v387 = 3;
                    v384 = 0;
                    v385 = 1;
                    v386 = v366 or v95;
                    if not v385 and v384 then
                        v385 = v384;
                        v384 = 1;
                    end;
                    if not v385 and not v384 then
                        v384 = 0;
                        v385 = 1;
                    end;
                    l_Angles_0 = v387 + v386:NextNumber(v384, v385);
                end;
                v381(l_Angles_0, function() --[[ Line: 2360 ]]
                    -- upvalues: v10 (ref), v379 (copy), v374 (ref), v107 (ref)
                    v10:Create(v379, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                        Transparency = 1, 
                        Size = if v374 then Vector3.new(0, 0, 0, 0) else v379.Size / 1.5
                    }):Play();
                    delay(0.3, function() --[[ Line: 2365 ]]
                        -- upvalues: v379 (ref), v107 (ref)
                        v379.Anchored = true;
                        local l_v379_0 = v379;
                        l_v379_0.AssemblyLinearVelocity = Vector3.new(0, 0, 0, 0);
                        l_v379_0.AssemblyAngularVelocity = Vector3.new(0, 0, 0, 0);
                        l_v379_0.Velocity = Vector3.new(0, 0, 0, 0);
                        v379.CanCollide = false;
                        v379.CFrame = CFrame.new(100000000, 100000000, 100000000);
                        table.insert(v107, v379);
                    end);
                end);
            else
                break;
            end;
        end;
        local v392 = 4;
        local v393 = 7;
        local v394 = v366 or v95;
        if not v393 and v392 then
            v393 = v392;
            v392 = 1;
        end;
        if not v393 and not v392 then
            v392 = 0;
            v393 = 1;
        end;
        v375 = v394:NextNumber(v392, v393) / v371;
        if l_LocalPlayer_0:GetAttribute("S_FastMode") then
            v375 = v375 / 2;
        end;
        if v363.nodebris then
            v375 = 0;
        end;
        for _ = 1, v375 do
            if not v374 then
                local v396 = 0.6;
                local v397 = 0.8;
                local v398 = v366 or v95;
                if not v397 and v396 then
                    v397 = v396;
                    v396 = 1;
                end;
                if not v397 and not v396 then
                    v396 = 0;
                    v397 = 1;
                end;
                v394 = v398:NextNumber(v396, v397);
                v396 = v301() or Instance.new("Part");
                v396.CFrame = v363.cframe;
                v397 = v327(v396.CFrame.UpVector, v363.normal, (Vector3.new(0, 1, 0, 0)));
                v396.CFrame = v396.CFrame * (v397 * CFrame.Angles(1.5707963267948966, 0, 0));
                v396.CollisionGroup = "nocol";
                v396.Size = Vector3.new(v394, v394, v394);
                v396.Color = v363.ground.Color;
                v396.CanCollide = not v374;
                v396.Name = "SmallDebris";
                v396.Anchored = false;
                v396.Material = v363.ground.Material;
                v396.Transparency = v363.ground.Transparency;
                v396.Parent = workspace.Thrown;
                if v396.Material == Enum.Material.Neon then
                    v396.Material = Enum.Material.Concrete;
                end;
                v398 = Instance.new("BodyVelocity");
                v398.MaxForce = Vector3.new(2000000000, 2000000000, 2000000000, 0);
                local v399 = -25;
                local v400 = 25;
                local v401 = v366 or v95;
                if not v400 and v399 then
                    v400 = v399;
                    v399 = 1;
                end;
                if not v400 and not v399 then
                    v399 = 0;
                    v400 = 1;
                end;
                local v402 = v401:NextNumber(v399, v400);
                v400 = 5;
                v401 = 25;
                local v403 = v366 or v95;
                if not v401 and v400 then
                    v401 = v400;
                    v400 = 1;
                end;
                if not v401 and not v400 then
                    v400 = 0;
                    v401 = 1;
                end;
                v398.Velocity = Vector3.new(v402, v403:NextNumber(v400, v401), v370(-25, 25)) * 2;
                v398.Parent = v396;
                game:GetService("Debris"):AddItem(v398, 0.15);
                local l_BodyAngularVelocity_0 = Instance.new("BodyAngularVelocity");
                l_BodyAngularVelocity_0.Parent = v396;
                game:GetService("Debris"):AddItem(l_BodyAngularVelocity_0, 0.5);
                local l_delay_0 = delay;
                if v374 then
                    v402 = 0;
                else
                    v399 = 3;
                    v401 = 0;
                    v403 = 1;
                    local v406 = v366 or v95;
                    if not v403 and v401 then
                        v403 = v401;
                        v401 = 1;
                    end;
                    if not v403 and not v401 then
                        v401 = 0;
                        v403 = 1;
                    end;
                    v402 = v399 + v406:NextNumber(v401, v403);
                end;
                l_delay_0(v402, function() --[[ Line: 2418 ]]
                    -- upvalues: v10 (ref), v396 (copy), v374 (ref), l_BodyAngularVelocity_0 (copy), v107 (ref)
                    v10:Create(v396, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                        Transparency = 1, 
                        Size = if v374 then Vector3.new(0, 0, 0, 0) else v396.Size / 1.5
                    }):Play();
                    delay(0.3, function() --[[ Line: 2424 ]]
                        -- upvalues: l_BodyAngularVelocity_0 (ref), v396 (ref), v107 (ref)
                        l_BodyAngularVelocity_0:Destroy();
                        v396.Anchored = true;
                        local l_v396_0 = v396;
                        l_v396_0.AssemblyLinearVelocity = Vector3.new(0, 0, 0, 0);
                        l_v396_0.AssemblyAngularVelocity = Vector3.new(0, 0, 0, 0);
                        l_v396_0.Velocity = Vector3.new(0, 0, 0, 0);
                        v396.CanCollide = false;
                        v396.CFrame = CFrame.new(100000000, 100000000, 100000000);
                        table.insert(v107, v396);
                    end);
                end);
            else
                break;
            end;
        end;
        local v408 = {};
        v392 = 5;
        v393 = 7;
        if v363.new then
            v392 = v363.new[1];
            v393 = v363.new[2];
        end;
        v394 = CFrame.new(v363.cframe.p);
        local v409 = (v392 + v393) / 2;
        local v410 = v363.amount or 16;
        if l_LocalPlayer_0:GetAttribute("S_FastMode") then
            v410 = v410 / 2;
        end;
        v394 = v394 * v327(v394.UpVector, v363.normal, (Vector3.new(0, 1, 0, 0)));
        local l_Angles_1 = CFrame.Angles;
        local v412 = 0;
        local v413 = -360;
        local v414 = 360;
        local v415 = v366 or v95;
        if not v414 and v413 then
            v414 = v413;
            v413 = 1;
        end;
        if not v414 and not v413 then
            v413 = 0;
            v414 = 1;
        end;
        v394 = v394 * l_Angles_1(v412, math.rad((v415:NextNumber(v413, v414))), 0);
        v412 = CFrame.Angles;
        local v416 = 0;
        v414 = -16;
        v415 = 16;
        local v417 = v366 or v95;
        if not v415 and v414 then
            v415 = v414;
            v414 = 1;
        end;
        if not v415 and not v414 then
            v414 = 0;
            v415 = 1;
        end;
        l_Angles_1 = v394 * v412(v416, math.rad((v417:NextNumber(v414, v415))), 0);
        v412 = true;
        if v392 ~= 5 and v393 ~= 7 then
            v363.Double = 2;
        end;
        for v418 = 1, v410 do
            for v419 = 1, v363.Double or 1 do
                local l_v394_0 = v394;
                if v419 > 1 then
                    l_v394_0 = l_Angles_1;
                    v409 = v409 + 1;
                end;
                local v421 = math.sin((360 / v410 + 360 / v410 * v418) / 57.29577951308232);
                local v422 = math.cos((360 / v410 + 360 / v410 * v418) / 57.29577951308232);
                local v423 = l_v394_0 * CFrame.new(v409 * v421, 0, v409 * v422);
                local v424 = 0.2;
                if v363.Double then
                    v424 = 0.35;
                end;
                local l_lookVector_0 = v423.lookVector;
                local v426 = -v424;
                local l_v424_0 = v424;
                local l_v426_0 = v426;
                local l_l_v424_0_0 = l_v424_0;
                local v430 = v366 or v95;
                if not l_l_v424_0_0 and l_v426_0 then
                    l_l_v424_0_0 = l_v426_0;
                    l_v426_0 = 1;
                end;
                if not l_l_v424_0_0 and not l_v426_0 then
                    l_v426_0 = 0;
                    l_l_v424_0_0 = 1;
                end;
                v423 = v423 + l_lookVector_0 * v430:NextNumber(l_v426_0, l_l_v424_0_0);
                local v431 = v301() or Instance.new("Part");
                if v363.dontcollide == l_LocalPlayer_0 then
                    v431.CollisionGroup = "nocol";
                end;
                v431.Anchored = true;
                v431.CanCollide = false;
                v431.CanTouch = true;
                v431.Material = v363.ground.Material;
                v431.Color = v363.ground.Color;
                v431.CFrame = v423 * CFrame.fromEulerAnglesXYZ(0, (360 / v410 + 360 / v410 * v418) / 57.29577951308232, 0);
                l_lookVector_0 = v431.CFrame;
                local l_Angles_2 = CFrame.Angles;
                l_l_v424_0_0 = 6;
                v430 = 42;
                local v433 = v366 or v95;
                if not v430 and l_l_v424_0_0 then
                    v430 = l_l_v424_0_0;
                    l_l_v424_0_0 = 1;
                end;
                if not v430 and not l_l_v424_0_0 then
                    l_l_v424_0_0 = 0;
                    v430 = 1;
                end;
                v431.CFrame = l_lookVector_0 * l_Angles_2(math.rad(-v433:NextNumber(l_l_v424_0_0, v430)), 0, 0);
                l_v426_0 = 2;
                l_l_v424_0_0 = 5;
                v430 = v366 or v95;
                if not l_l_v424_0_0 and l_v426_0 then
                    l_l_v424_0_0 = l_v426_0;
                    l_v426_0 = 1;
                end;
                if not l_l_v424_0_0 and not l_v426_0 then
                    l_v426_0 = 0;
                    l_l_v424_0_0 = 1;
                end;
                v426 = v430:NextNumber(l_v426_0, l_l_v424_0_0) * (v419 > 1 and 1.8 or v363.Double and 1.35 or 1);
                l_v426_0 = 1.9;
                l_l_v424_0_0 = 2.4;
                v430 = v366 or v95;
                if not l_l_v424_0_0 and l_v426_0 then
                    l_l_v424_0_0 = l_v426_0;
                    l_v426_0 = 1;
                end;
                if not l_l_v424_0_0 and not l_v426_0 then
                    l_v426_0 = 0;
                    l_l_v424_0_0 = 1;
                end;
                v431.Size = Vector3.new(v426, v430:NextNumber(l_v426_0, l_l_v424_0_0), v370(2, 6)) * v364;
                v431.CFrame = v431.CFrame * CFrame.new(0, -(v431.Size.Y / 3 + v431.Size.Z / 3) / 3, 0);
                v431.Transparency = 1;
                v431.Parent = workspace.Thrown;
                l_lookVector_0 = v431.CFrame;
                l_Angles_2 = nil;
                v426 = v338(v431);
                l_v424_0 = {};
                l_v426_0 = nil;
                l_l_v424_0_0 = nil;
                for _, v435 in pairs(v426) do
                    if not v435:IsDescendantOf(workspace.Live) and v435.Name ~= "invis" and v435.Parent ~= workspace.Thrown then
                        if v435.Material ~= v363.ground.Material then
                            l_Angles_2 = v435;
                        end;
                        table.insert(l_v424_0, v435);
                    end;
                    if v435 == v363.ground then
                        l_v426_0 = v363.ground;
                    end;
                    if v435.Name == "Stadium" then
                        l_l_v424_0_0 = true;
                    end;
                end;
                v430 = v431.CFrame;
                local v436 = CFrame.new(0, -3, -1.5);
                local l_Angles_3 = CFrame.Angles;
                local v438 = -11;
                local v439 = 11;
                local v440 = v366 or v95;
                if not v439 and v438 then
                    v439 = v438;
                    v438 = 1;
                end;
                if not v439 and not v438 then
                    v438 = 0;
                    v439 = 1;
                end;
                local v441 = math.rad((v440:NextNumber(v438, v439)));
                v439 = -11;
                v440 = 11;
                local v442 = v366 or v95;
                if not v440 and v439 then
                    v440 = v439;
                    v439 = 1;
                end;
                if not v440 and not v439 then
                    v439 = 0;
                    v440 = 1;
                end;
                local v443 = math.rad((v442:NextNumber(v439, v440)));
                v440 = -11;
                v442 = 11;
                local v444 = v366 or v95;
                if not v442 and v440 then
                    v442 = v440;
                    v440 = 1;
                end;
                if not v442 and not v440 then
                    v440 = 0;
                    v442 = 1;
                end;
                v431.CFrame = v430 * (v436 * l_Angles_3(v441, v443, (math.rad((v444:NextNumber(v440, v442))))));
                v431.Transparency = 0;
                table.insert(v408, v431);
                v430 = true;
                if #l_v424_0 ~= 0 then
                    v430 = v363.ground:GetAttribute("Breakable") or not v363.ground.Anchored;
                end;
                v433 = v363.angle;
                if v433 then
                    l_Angles_3 = v363.anglecfr;
                    v441 = l_lookVector_0.Position;
                    v443 = (Vector3.new(v441.X, l_Angles_3.p.Y, v441.Z) - l_Angles_3.p).unit;
                    v433 = math.deg((math.acos((l_Angles_3.LookVector:Dot(v443))))) <= v363.angle;
                end;
                if v430 or v433 then
                    if not v373 then
                        v431.Transparency = 1;
                    end;
                else
                    if l_Angles_2 and (not l_v426_0 or l_v426_0.Name ~= "Stadium") and (not l_l_v424_0_0 or v363.ground.Name ~= "Stadium") then
                        if v363.ground.Material == Enum.Material.Grass and l_v426_0 then
                            l_Angles_2 = workspace.Preload.Dirt;
                        end;
                        v431.Color = l_Angles_2.Color;
                        v431.Material = l_Angles_2.Material;
                    end;
                    v431.Transparency = v363.ground.Transparency;
                end;
                if v431.Transparency >= 1 then
                    v431.CanCollide = false;
                    v431.CanTouch = false;
                    v431.CanQuery = false;
                else
                    v431.CanCollide = true;
                end;
                if v431.Velocity.magnitude > 0 then
                    v431.AssemblyLinearVelocity = Vector3.new(0, 0, 0, 0);
                    v431.AssemblyAngularVelocity = Vector3.new(0, 0, 0, 0);
                    v431.Velocity = Vector3.new(0, 0, 0, 0);
                end;
                if v374 or v373 then
                    v431.CFrame = l_lookVector_0;
                    v362(v431);
                end;
                do
                    local l_l_v394_0_0 = l_v394_0;
                    if v412 then
                        v412 = false;
                        v436 = {
                            "rbxassetid://3848076724", 
                            "rbxassetid://3848078820"
                        };
                        l_Angles_3 = {
                            "rbxassetid://4307208601", 
                            "rbxassetid://4307207425", 
                            "rbxassetid://4307207693", 
                            "rbxassetid://4307205188", 
                            "rbxassetid://3778609188", 
                            "rbxassetid://3778608737", 
                            "rbxassetid://3744401196", 
                            "rbxassetid://4307204962"
                        };
                        v441 = {
                            "rbxassetid://4307204696", 
                            "rbxassetid://4307204452"
                        };
                        if v374 then
                            v443 = 1;
                            if v372 then
                                v443 = 2;
                            end;
                            if not shared.recentdebris then
                                shared.recentdebris = {};
                            end;
                            v438 = function(v446) --[[ Line: 2583 ]] --[[ Name: adaptiveWait ]]
                                local v447 = 0.015;
                                if v446 < 0.8 then
                                    v447 = 0.015 + (0.8 - v446) * 0.02;
                                end;
                                return v447;
                            end;
                            v439 = (v392 + v393) / 12;
                            v440 = task.delay;
                            v444 = 0.015;
                            if v439 < 0.8 then
                                v444 = 0.015 + (0.8 - v439) * 0.02;
                            end;
                            local l_v439_0 = v439 --[[ copy: 46 -> 52 ]];
                            do
                                local l_v443_0 = v443;
                                v440(v444, function() --[[ Line: 2595 ]]
                                    -- upvalues: l_v439_0 (copy), l_l_v394_0_0 (ref), v313 (ref), l_v443_0 (ref), v373 (ref), v392 (ref), v393 (ref), v366 (copy), v95 (ref)
                                    local v450 = true;
                                    for v451, v452 in pairs(shared.recentdebris) do
                                        local v453 = v452[1];
                                        local _ = v452[2];
                                        if tick() - v452[2] > 0.5 then
                                            shared.recentdebris[v451] = nil;
                                        elseif (v453.Position - v453.Position).magnitude <= 5 then
                                            v450 = false;
                                            break;
                                        end;
                                    end;
                                    if v450 then
                                        local v455 = l_v439_0 >= 0.8;
                                        if v455 then
                                            table.insert(shared.recentdebris, {
                                                l_l_v394_0_0, 
                                                tick()
                                            });
                                            v313({
                                                SoundId = ({
                                                    "rbxassetid://18922680029", 
                                                    "rbxassetid://18922679331", 
                                                    "rbxassetid://18922678743", 
                                                    "rbxassetid://18922678349"
                                                })[math.random(1, 4)], 
                                                Volume = 1.35 / l_v443_0, 
                                                CFrame = l_l_v394_0_0
                                            }):Play();
                                        end;
                                        if v373 then
                                            local v456 = (v392 + v393) / 12;
                                            local v457 = {
                                                "rbxassetid://18922743710", 
                                                "rbxassetid://18922743936", 
                                                "rbxassetid://18922744361"
                                            };
                                            if not v455 then
                                                v457 = {
                                                    "rbxassetid://97900036122485", 
                                                    "rbxassetid://73075062841158", 
                                                    "rbxassetid://122164610127113", 
                                                    "rbxassetid://78182682399983", 
                                                    "rbxassetid://89999290123856", 
                                                    "rbxassetid://70534139031144"
                                                };
                                            end;
                                            local v458 = 0;
                                            if not v455 then
                                                v458 = v458 + 1;
                                            end;
                                            local l_v313_0 = v313;
                                            local v460 = {
                                                SoundId = v457[math.random(#v457)], 
                                                Volume = 1.75 / l_v443_0 + math.max(v456 - 1, 0) + v458
                                            };
                                            local v461 = 0.9;
                                            local v462 = 1.1;
                                            local v463 = v366 or v95;
                                            if not v462 and v461 then
                                                v462 = v461;
                                                v461 = 1;
                                            end;
                                            if not v462 and not v461 then
                                                v461 = 0;
                                                v462 = 1;
                                            end;
                                            v460.PlaybackSpeed = v463:NextNumber(v461, v462);
                                            v460.CFrame = l_l_v394_0_0;
                                            l_v313_0(v460):Play();
                                            l_v313_0 = game.ReplicatedStorage.Resources.Splash:Clone();
                                            game:GetService("Debris"):AddItem(l_v313_0, 5);
                                            l_v313_0:ScaleTo(v456 + 0.3);
                                            l_v313_0.PrimaryPart.CFrame = l_l_v394_0_0;
                                            l_v313_0.Parent = workspace.Thrown;
                                            for _, v465 in pairs(l_v313_0:GetDescendants()) do
                                                if v465:IsA("ParticleEmitter") then
                                                    v465:Emit(v465:GetAttribute("EmitCount"));
                                                end;
                                            end;
                                        end;
                                    end;
                                end);
                            end;
                        elseif l_add_0.sounds and not v363.nosound then
                            v443 = 1;
                            if v372 then
                                v443 = 2;
                            end;
                            v438 = v313;
                            v439 = {};
                            v444 = #v441;
                            local v466 = nil;
                            local l_v95_3 = v95;
                            if not v466 and v444 then
                                v466 = v444;
                                v444 = 1;
                            end;
                            if not v466 and not v444 then
                                v444 = 0;
                                v466 = 1;
                            end;
                            v439.SoundId = v441[l_v95_3:NextInteger(v444, v466)];
                            v439.Volume = 3.85 / v443;
                            v439.Parent = v431;
                            v438(v439):Play();
                            v438 = v313;
                            v439 = {};
                            v444 = #l_Angles_3;
                            v466 = nil;
                            l_v95_3 = v95;
                            if not v466 and v444 then
                                v466 = v444;
                                v444 = 1;
                            end;
                            if not v466 and not v444 then
                                v444 = 0;
                                v466 = 1;
                            end;
                            v439.SoundId = l_Angles_3[l_v95_3:NextInteger(v444, v466)];
                            v439.Volume = 4.3 / v443;
                            v439.Parent = v431;
                            v438(v439):Play();
                            v438 = v313;
                            v439 = {};
                            v444 = #v436;
                            v466 = nil;
                            l_v95_3 = v95;
                            if not v466 and v444 then
                                v466 = v444;
                                v444 = 1;
                            end;
                            if not v466 and not v444 then
                                v444 = 0;
                                v466 = 1;
                            end;
                            v439.SoundId = v436[l_v95_3:NextInteger(v444, v466)];
                            v439.Volume = 4.12 / v443;
                            v439.Parent = v431;
                            v438(v439):Play();
                        end;
                    end;
                    v431:SetAttribute("OGCframe", l_lookVector_0);
                    if not v374 then
                        v436 = v10;
                        v441 = v431;
                        v443 = TweenInfo.new;
                        v439 = 0.2;
                        v440 = 0.3;
                        v442 = v366 or v95;
                        if not v440 and v439 then
                            v440 = v439;
                            v439 = 1;
                        end;
                        if not v440 and not v439 then
                            v439 = 0;
                            v440 = 1;
                        end;
                        v436:Create(v441, v443(v442:NextNumber(v439, v440), Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                            CFrame = l_lookVector_0
                        }):Play();
                    end;
                    if v374 or v373 then
                        task.delay(1, function() --[[ Line: 2700 ]]
                            -- upvalues: v431 (copy), v107 (ref)
                            v431:SetAttribute("OGCframe", nil);
                            v431.Anchored = true;
                            local l_v431_0 = v431;
                            l_v431_0.AssemblyLinearVelocity = Vector3.new(0, 0, 0, 0);
                            l_v431_0.AssemblyAngularVelocity = Vector3.new(0, 0, 0, 0);
                            l_v431_0.Velocity = Vector3.new(0, 0, 0, 0);
                            v431.CFrame = CFrame.new(100000000, 100000000, 100000000);
                            table.insert(v107, v431);
                        end);
                    else
                        v436 = l_add_0.keep;
                        if not v436 then
                            l_Angles_3 = 5;
                            v441 = 7;
                            v443 = v366 or v95;
                            if not v441 and l_Angles_3 then
                                v441 = l_Angles_3;
                                l_Angles_3 = 1;
                            end;
                            if not v441 and not l_Angles_3 then
                                l_Angles_3 = 0;
                                v441 = 1;
                            end;
                            v436 = v443:NextNumber(l_Angles_3, v441);
                        end;
                        l_Angles_3 = TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.In);
                        if v363.dispersefast then
                            v436 = v363.dispersefast;
                        end;
                        if v363.customtween then
                            l_Angles_3 = v363.customtween;
                        end;
                        do
                            local l_l_Angles_3_0 = l_Angles_3;
                            delay(v436, function() --[[ Line: 2718 ]]
                                -- upvalues: l_add_0 (copy), v10 (ref), v431 (copy), l_lookVector_0 (copy), l_l_Angles_3_0 (ref), v107 (ref)
                                if l_add_0.keep then
                                    v10:Create(v431, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                                        CFrame = l_lookVector_0, 
                                        Size = v431.Size
                                    }):Play();
                                    wait(2);
                                end;
                                v10:Create(v431, l_l_Angles_3_0, {
                                    CFrame = v431.CFrame * CFrame.new(0, -7, 0), 
                                    Size = v431.Size / 1.5
                                }):Play();
                                wait(2);
                                v431:SetAttribute("OGCframe", nil);
                                v431.Anchored = true;
                                local l_v431_1 = v431;
                                l_v431_1.AssemblyLinearVelocity = Vector3.new(0, 0, 0, 0);
                                l_v431_1.AssemblyAngularVelocity = Vector3.new(0, 0, 0, 0);
                                l_v431_1.Velocity = Vector3.new(0, 0, 0, 0);
                                v431.CFrame = CFrame.new(100000000, 100000000, 100000000);
                                table.insert(v107, v431);
                            end);
                        end;
                    end;
                end;
            end;
        end;
        return v408;
    end;
end;

-- ============================================================
-- v717 / shared.ray  Raycast helper
-- ============================================================
local function v717(v711) --[[ Line: 3797 ]]
    -- upvalues: v472 (copy)
    local l_orig_0 = v711.orig;
    local l_dir_0 = v711.dir;
    local v714 = RaycastParams.new();
    v714.FilterDescendantsInstances = v711.Whitelist or v711.Ignore or v472;
    if v711.Whitelist then
        v714.FilterType = Enum.RaycastFilterType.Include;
    else
        v714.FilterType = Enum.RaycastFilterType.Exclude;
    end;
    if v711.Blockcast then
        local v715 = workspace:Blockcast(l_orig_0, v711.Blockcast, l_dir_0, v714);
        if v715 then
            return v715.Instance, v715.Position, v715.Material, v715.Normal;
        else
            return;
        end;
    else
        local v716 = workspace:Raycast(l_orig_0, l_dir_0, v714);
        if v716 then
            return v716.Instance, v716.Position, v716.Material, v716.Normal;
        else
            return;
        end;
    end;
end;
shared.ray = v717;
local v718 = math.random(1, 100);

-- ============================================================
-- v949  BoatTween  |  v955  TweenService wrapper
-- ============================================================
local v949 = require(game.ReplicatedStorage.BoatTween);
local function v955(v950, v951, v952) --[[ Line: 4458 ]]
    -- upvalues: v949 (copy)
    local v953 = v949:Create(v950, v951);
    v953:Play();
    local v954 = v953.Completed:Once(function() --[[ Line: 4462 ]]
        -- upvalues: v952 (copy), v953 (copy)
        if v952 then
            v952();
        end;
        v953:Destroy();
    end);
    task.delay(v951.Time, function() --[[ Line: 4468 ]]
        -- upvalues: v954 (copy), v953 (copy)
        v954:Disconnect();
        v953:Destroy();
    end);
end;
local _ = function(v956) --[[ Line: 4474 ]]
    task.spawn(function() --[[ Line: 4475 ]]
        -- upvalues: v956 (copy)
        local l_Model_0 = v956.Model;
        local v958 = v956.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
        local l_Start_0 = l_Model_0:FindFirstChild("Start");
        local l_End_0 = l_Model_0:FindFirstChild("End");
        local l_Stay_0 = v956.Stay;
        local l_Anchor_0 = v956.Anchor;
        local v963 = v956.EndT or 1;
        local l_Del_0 = v956.Del;
        local l_Skip_0 = v956.Skip;
        if l_Start_0 and l_End_0 then
            l_Model_0.PrimaryPart = l_Start_0;
            if not l_Skip_0 then
                for _, v967 in pairs(l_Model_0:GetChildren()) do
                    if v967:IsA("BasePart") then
                        v967.CanCollide = false;
                        v967.Anchored = true;
                    end;
                end;
            end;
            if l_Anchor_0 then
                l_Model_0:SetPrimaryPartCFrame(l_Anchor_0);
            end;
            if v956.T then
                l_Start_0.Transparency = v956.T;
            end;
            l_End_0.Transparency = 1;
            l_Model_0.Parent = workspace.Thrown;
            local l_Decal_0 = l_Start_0:FindFirstChildOfClass("Decal");
            local l_SpecialMesh_0 = l_Start_0:FindFirstChildOfClass("SpecialMesh");
            local l_SpecialMesh_1 = l_End_0:FindFirstChildOfClass("SpecialMesh");
            local l_Decal_1 = l_End_0:FindFirstChildOfClass("Decal");
            if l_Decal_1 and not l_Skip_0 then
                l_Decal_1.Transparency = 1;
            end;
            local v972 = nil;
            if l_Del_0 then
                game:GetService("TweenService"):Create(l_Start_0, v958, {
                    Size = l_End_0.Size, 
                    CFrame = l_End_0.CFrame
                }):Play();
                task.delay(l_Del_0, function() --[[ Line: 4519 ]]
                    -- upvalues: v972 (ref), l_Start_0 (copy), v958 (copy), v963 (copy), l_Decal_0 (copy), l_SpecialMesh_0 (copy), l_SpecialMesh_1 (copy)
                    v972 = game:GetService("TweenService"):Create(l_Start_0, v958, {
                        Transparency = v963
                    });
                    v972:Play();
                    if l_Decal_0 then
                        for _, v974 in pairs(l_Start_0:GetChildren()) do
                            if v974:IsA("Decal") then
                                game:GetService("TweenService"):Create(v974, v958, {
                                    Transparency = v963
                                }):Play();
                            end;
                        end;
                    end;
                    if l_SpecialMesh_0 then
                        v972 = game:GetService("TweenService"):Create(l_SpecialMesh_0, v958, {
                            Scale = l_SpecialMesh_1.Scale
                        }):Play();
                    end;
                end);
            else
                if l_SpecialMesh_0 then
                    game:GetService("TweenService"):Create(l_SpecialMesh_0, v958, {
                        Scale = l_SpecialMesh_1.Scale
                    }):Play();
                end;
                if l_Decal_0 then
                    for _, v976 in pairs(l_Start_0:GetChildren()) do
                        if v976:IsA("Decal") then
                            game:GetService("TweenService"):Create(v976, v958, {
                                Transparency = v963
                            }):Play();
                        end;
                    end;
                    v972 = game:GetService("TweenService"):Create(l_Start_0, v958, {
                        Size = l_End_0.Size, 
                        CFrame = l_End_0.CFrame
                    });
                    v972:Play();
                else
                    v972 = game:GetService("TweenService"):Create(l_Start_0, v958, {
                        Size = l_End_0.Size, 
                        Transparency = v963, 
                        CFrame = l_End_0.CFrame
                    });
                    v972:Play();
                end;
            end;
            if not l_Stay_0 then
                if l_Del_0 then
                    task.wait(l_Del_0 + 0.1);
                end;
                v972.Completed:Connect(function() --[[ Line: 4562 ]]
                    -- upvalues: l_Model_0 (copy)
                    l_Model_0:Destroy();
                end);
            end;
            return;
        else
            warn("NO START OR END");
            return;
        end;
    end);
end;

-- ============================================================
-- v983  ZOffset  |  v989  Lifetime scale
-- ============================================================
local function v983(v978) --[[ Line: 4572 ]]
    local l_FX_0 = v978.FX;
    local l_Count_0 = v978.Count;
    for _, v982 in pairs(l_FX_0:GetDescendants()) do
        if v982:IsA("ParticleEmitter") then
            v982.ZOffset = v982.ZOffset + l_Count_0;
        end;
    end;
end;
local function v989(v984) --[[ Line: 4583 ]]
    local l_FX_1 = v984.FX;
    local l_Scale_0 = v984.Scale;
    for _, v988 in pairs(l_FX_1:GetDescendants()) do
        if v988:IsA("ParticleEmitter") then
            v988.Lifetime = NumberRange.new(v988.Lifetime.Min * l_Scale_0, v988.Lifetime.Max * l_Scale_0);
        end;
    end;
end;

-- ============================================================
-- Emit  Emit all ParticleEmitters
-- ============================================================
Emit = function(v990, v991) --[[ Line: 4595 ]] --[[ Name: Emit ]]
    local l_v990_0 = v990;
    if v990:IsA("ParticleEmitter") then
        l_v990_0 = {
            v990
        };
    elseif typeof(v990) ~= "table" then
        l_v990_0 = v990:GetDescendants();
    end;
    local v993 = 0;
    for _, v995 in l_v990_0 do
        if v995:IsA("ParticleEmitter") and v995:GetAttribute("EmitCount") then
            local v996 = (typeof(v995.Lifetime) == "NumberRange" and v995.Lifetime.Max or v995.Lifetime) / v995.TimeScale;
            if v995:GetAttribute("EmitDelay") and v995:GetAttribute("EmitDelay") > 0 then
                v996 = v996 + v995:GetAttribute("EmitDelay");
                task.delay(v995:GetAttribute("EmitDelay"), function() --[[ Line: 4620 ]]
                    -- upvalues: v995 (copy)
                    v995:Emit(v995:GetAttribute("EmitCount"));
                end);
            else
                v995:Emit(v995:GetAttribute("EmitCount"));
            end;
            if v991 then
                task.delay(v996, v995.Destroy, v995);
                if v993 < v996 then
                    v993 = v996;
                end;
            end;
        end;
    end;
    if v991 and typeof(v990) == "Instance" then
        task.delay(v993, function() --[[ Line: 4637 ]]
            -- upvalues: v990 (copy)
            v990:Destroy();
        end);
    end;
end;

-- ============================================================
-- v1000  Clone FX at anchor  |  v1014  Activate model
-- ============================================================
local function v1000(v997) --[[ Line: 4643 ]]
    local l_FX_2 = v997.FX;
    local l_Anchor_1 = v997.Anchor;
    l_FX_2 = l_FX_2:Clone();
    if l_FX_2:IsA("Model") then
        l_FX_2:PivotTo(l_Anchor_1);
    elseif l_FX_2:IsA("BasePart") then
        l_FX_2.CFrame = l_Anchor_1;
    end;
    if l_FX_2:IsA("Attachment") then
        l_FX_2.Parent = l_Anchor_1;
    else
        l_FX_2.Parent = workspace.Thrown;
    end;
    game.Debris:AddItem(l_FX_2, 30);
    return l_FX_2;
end;
local function v1014(v1001, v1002, v1003) --[[ Line: 4666 ]]
    for _, v1005 in pairs(v1001:GetDescendants()) do
        if v1005:IsA("ParticleEmitter") then
            local l_v1005_Attributes_0 = v1005:GetAttributes();
            local l_EmitDelay_0 = l_v1005_Attributes_0.EmitDelay;
            if l_EmitDelay_0 then
                local l_l_v1005_Attributes_0_0 = l_v1005_Attributes_0 --[[ copy: 8 -> 12 ]];
                task.delay(l_EmitDelay_0, function() --[[ Line: 4672 ]]
                    -- upvalues: v1005 (copy), l_l_v1005_Attributes_0_0 (copy)
                    v1005:Emit(l_l_v1005_Attributes_0_0.EmitCount);
                end);
            else
                v1005:Emit(l_v1005_Attributes_0.EmitCount);
            end;
            if l_v1005_Attributes_0.EmitDuration then
                v1005.Enabled = true;
                task.delay(l_v1005_Attributes_0.EmitDuration, function() --[[ Line: 4682 ]]
                    -- upvalues: v1005 (copy)
                    v1005.Enabled = false;
                end);
            end;
        end;
        if v1005:IsA("Beam") then
            local l_v1005_Attributes_1 = v1005:GetAttributes();
            local l_Duration_0 = l_v1005_Attributes_1.Duration;
            local v1011 = 0.5;
            if v1003 and v1003.TweenTime then
                v1011 = v1003.TweenTime;
            end;
            do
                local l_v1011_0 = v1011;
                local function v1013() --[[ Line: 4699 ]] --[[ Name: Shut_OFF ]]
                    -- upvalues: v1005 (copy), l_v1011_0 (ref)
                    game:GetService("TweenService"):Create(v1005, TweenInfo.new(l_v1011_0, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {
                        Width1 = 0, 
                        Width0 = 0
                    }):Play();
                end;
                (function() --[[ Line: 4703 ]] --[[ Name: Turn_ON ]]
                    -- upvalues: v1005 (copy), l_v1011_0 (ref), l_v1005_Attributes_1 (copy)
                    game:GetService("TweenService"):Create(v1005, TweenInfo.new(l_v1011_0, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {
                        Width1 = l_v1005_Attributes_1.Width1, 
                        Width0 = l_v1005_Attributes_1.Width0
                    }):Play();
                end)();
                if l_Duration_0 then
                    task.delay(l_Duration_0, function() --[[ Line: 4711 ]]
                        -- upvalues: v1013 (copy)
                        v1013();
                    end);
                end;
            end;
        end;
    end;
    if v1002 then
        game.Debris:AddItem(v1001, v1002);
    end;
end;

-- ============================================================
-- v1021  Toggle particles  |  v1034  Flipbook animator
-- ============================================================
local function v1021(v1015) --[[ Line: 4724 ]]
    local l_FX_3 = v1015.FX;
    if v1015.On then
        for _, v1018 in pairs(l_FX_3:GetDescendants()) do
            if v1018:IsA("ParticleEmitter") then
                v1018.Enabled = true;
            end;
        end;
        return;
    else
        for _, v1020 in pairs(l_FX_3:GetDescendants()) do
            if v1020:IsA("ParticleEmitter") then
                v1020.Enabled = false;
            end;
        end;
        return;
    end;
end;
local function v1034(v1022) --[[ Line: 4743 ]]
    local l_Mesh_0 = v1022.Mesh;
    local v1024 = v1022.Delta or 0.02;
    local l_DWC_0 = v1022.DWC;
    local v1026 = v1022.Repeat or 1;
    local v1027 = v1022.FPS or 1;
    local _ = v1022.Loop;
    local l_Folder_2 = l_Mesh_0:FindFirstChild("Folder");
    local l_Decal_2 = l_Mesh_0:FindFirstChild("Decal");
    if l_DWC_0 == nil then
        l_DWC_0 = true;
    end;
    if l_Folder_2 and l_Decal_2 then
        task.spawn(function() --[[ Line: 4759 ]]
            -- upvalues: v1026 (copy), l_Mesh_0 (copy), l_Folder_2 (copy), v1027 (copy), l_Decal_2 (copy), v1024 (copy), l_DWC_0 (ref)
            for _ = 1, v1026 do
                if not l_Mesh_0.Parent then
                    return;
                else
                    for v1032 = 1, #l_Folder_2:GetChildren(), v1027 do
                        local v1033 = l_Folder_2:FindFirstChild((tostring(v1032))) or l_Folder_2:GetChildren()[#l_Folder_2:GetChildren()];
                        if v1033 then
                            l_Decal_2.Texture = v1033.Texture;
                        end;
                        if v1024 == "Step" then
                            game:GetService("RunService").RenderStepped:Wait();
                        else
                            task.wait(v1024);
                        end;
                    end;
                end;
            end;
            if l_DWC_0 and l_DWC_0 ~= false then
                l_Mesh_0:Destroy();
            end;
        end);
    end;
end;

-- ============================================================
-- v1058  Asset loader + Maid factory
-- ============================================================
local function v1058(v1035, v1036, v1037) --[[ Line: 4786 ]]
    local v1038 = {};
    for _, v1040 in pairs(v1035:GetChildren()) do
        v1038[v1040.Name] = v1040;
    end;
    local v1048 = setmetatable({}, {
        __index = function(_, v1042) --[[ Line: 4793 ]] --[[ Name: __index ]]
            -- upvalues: v1038 (ref), v1036 (ref)
            return {
                Clone = function(_) --[[ Line: 4795 ]] --[[ Name: Clone ]]
                    -- upvalues: v1038 (ref), v1042 (copy), v1036 (ref)
                    local v1044 = rawget(v1038, v1042):Clone();
                    if not table.find(v1036, v1044) then
                        table.insert(v1036, v1044);
                    end;
                    return v1044;
                end, 
                GetChildren = function(_) --[[ Line: 4804 ]] --[[ Name: GetChildren ]]
                    -- upvalues: v1038 (ref), v1042 (copy)
                    return rawget(v1038, v1042):GetChildren();
                end, 
                GetAttribute = function(_, v1047) --[[ Line: 4809 ]] --[[ Name: GetAttribute ]]
                    -- upvalues: v1038 (ref), v1042 (copy)
                    return rawget(v1038, v1042):GetAttribute(v1047);
                end
            };
        end
    });
    local v1049 = {
        _maid = {}
    };
    local function v1052(_, v1051) --[[ Line: 4820 ]] --[[ Name: give ]]
        -- upvalues: v1036 (ref)
        if not table.find(v1036, v1051) then
            table.insert(v1036, v1051);
        end;
        return v1051;
    end;
    v1049._maid.give = v1052;
    v1052 = function(_, v1054) --[[ Line: 4827 ]] --[[ Name: giveTask ]]
        -- upvalues: v1036 (ref)
        if not table.find(v1036, v1054) then
            table.insert(v1036, v1054);
        end;
        return v1054;
    end;
    v1049._maid.giveTask = v1052;
    v1052 = function() --[[ Line: 4834 ]]
        -- upvalues: v1048 (ref), v1049 (ref), v1038 (ref), v1036 (ref)
        if v1048 and v1049 then
            table.clear(v1048);
            table.clear(v1049);
        end;
        if v1038 then
            table.clear(v1038);
            v1038 = nil;
        end;
        v1049 = nil;
        v1048 = nil;
        if v1036 then
            for v1055, v1056 in pairs(v1036) do
                if typeof(v1056) == "RBXScriptConnection" then
                    v1056:Disconnect();
                elseif typeof(v1056) == "Instance" then
                    v1056:Destroy();
                end;
                v1036[v1055] = nil;
            end;
            table.clear(v1036);
        end;
        v1036 = nil;
    end;
    local v1057 = 10;
    if v1037 ~= nil and typeof(v1037) == "number" then
        v1057 = v1037;
    end;
    task.delay(v1057, v1052);
    return v1048, v1049, v1052;
end;

-- ============================================================
-- QuickWeldNew  Weld FX to part with Maid tracking
-- ============================================================
QuickWeldNew = function(v1082) --[[ Line: 4965 ]] --[[ Name: QuickWeldNew ]]
    local l_FX_4 = v1082.FX;
    local v1084 = v1082.C0 or CFrame.new(0, 0, 0);
    local l_P_0 = v1082.P;
    local l_Maid_0 = v1082.Maid;
    local l_l_FX_4_0 = l_FX_4;
    if typeof(l_l_FX_4_0) == "Instance" and l_l_FX_4_0:IsA("Model") then
        l_l_FX_4_0 = l_l_FX_4_0.PrimaryPart;
    end;
    pcall(function() --[[ Line: 4973 ]]
        -- upvalues: l_l_FX_4_0 (ref)
        l_l_FX_4_0.Anchored = false;
        l_l_FX_4_0.CanCollide = false;
        l_l_FX_4_0.Massless = true;
    end);
    l_FX_4 = l_FX_4:Clone();
    local l_Weld_0 = Instance.new("Weld");
    if l_FX_4:IsA("Model") then
        l_Weld_0.Part0 = l_FX_4.PrimaryPart;
    else
        l_Weld_0.Part0 = l_FX_4;
    end;
    l_Weld_0.Part1 = l_P_0;
    l_Weld_0.C0 = v1084;
    l_Weld_0.Parent = l_FX_4;
    l_FX_4.Parent = workspace.Thrown;
    l_Maid_0:give(l_FX_4);
    return l_FX_4, l_Weld_0;
end;

-- ============================================================
-- v12  Effects dispatcher (wraps all three effect blocks)
-- ============================================================
local l_LocalPlayer_0 = game.Players.LocalPlayer
shared.SetCore    = shared.SetCore    or function() end
shared.addshake   = shared.addshake   or function() end
shared.CutsceneEvent = shared.CutsceneEvent or function() end
shared.sfx        = v313
shared.ray        = v717

local v12
v12 = function(v1090)
    local v1091 = rawget(v1090, "Effect");
    local l_Character_5 = l_LocalPlayer_0.Character;

    -- ==========================================
    -- LAST BREATH NEW
    -- ==========================================
    if v1091 == "Last Breath New" then
        local l_char_26 = v1090.char;
        local l_id_8 = v1090.id;
        local v7292 = nil;
        local l_PrimaryPart_49 = l_char_26.PrimaryPart;
        local v7294 = {};
        local l_CurrentCamera_8 = workspace.CurrentCamera;
        local l_LastBreath_0 = game.ReplicatedStorage.Resources.LastBreath;
        local v7297, v7298 = v1058(l_LastBreath_0, v7294, 25);
        local l_TweenService_26 = game:GetService("TweenService");
        local l_Thrown_23 = workspace.Thrown;
        local l_wait_19 = task.wait;
        local v7302 = Random.new();
        local v7303 = 4;
        local l_IDs_0 = l_LastBreath_0.IDs;
        local _ = {
            "rbxassetid://134874154282500", 
            "rbxassetid://95103199653377", 
            "rbxassetid://130506351473507", 
            "rbxassetid://101498403340552", 
            "rbxassetid://82957099537027", 
            "rbxassetid://91203818794269", 
            "rbxassetid://114974510966309", 
            "rbxassetid://122037235239864", 
            "rbxassetid://109902527005867", 
            "rbxassetid://74499736615967", 
            "rbxassetid://94348460790225", 
            "rbxassetid://120623043752262", 
            "rbxassetid://93003436623091", 
            "rbxassetid://124065460762994", 
            "rbxassetid://71139146261999", 
            "rbxassetid://78919809546628", 
            "rbxassetid://124728711065179", 
            "rbxassetid://82247420844843", 
            "rbxassetid://134637540233590", 
            "rbxassetid://125189547220630", 
            "rbxassetid://102750081492379", 
            "rbxassetid://71436360183851", 
            "rbxassetid://103159845151922", 
            "rbxassetid://80526523992135", 
            "rbxassetid://121540864888457"
        };
        local v7306 = {
            Dash = {
                "rbxassetid://17832942931", 
                "rbxassetid://17832951215", 
                "rbxassetid://17832958272", 
                "rbxassetid://17832970162", 
                "rbxassetid://17832975503", 
                "rbxassetid://17832979515", 
                "rbxassetid://17832984539", 
                "rbxassetid://17832988245", 
                "rbxassetid://17832993160", 
                "rbxassetid://17832997682", 
                "rbxassetid://17833003979", 
                "rbxassetid://17833007948", 
                "rbxassetid://17833011261", 
                "rbxassetid://17833013866", 
                "rbxassetid://17833017399", 
                "rbxassetid://17833020479", 
                "rbxassetid://140322785840145"
            }, 
            RunWind = {
                "rbxassetid://80715070561044", 
                "rbxassetid://86413958219693", 
                "rbxassetid://87511479094596", 
                "rbxassetid://100175789922973", 
                "rbxassetid://138814790858650", 
                "rbxassetid://128708934211892", 
                "rbxassetid://134455851016041", 
                "rbxassetid://89265930309230", 
                "rbxassetid://119816638740586", 
                "rbxassetid://93843424969472", 
                "rbxassetid://75847205115591", 
                "rbxassetid://86585503946580", 
                "rbxassetid://135117132926514", 
                "rbxassetid://81097940937769", 
                "rbxassetid://80524231392226", 
                "rbxassetid://132298577561623"
            }, 
            Wind = {
                "rbxassetid://94350061671584", 
                "rbxassetid://99288342496275", 
                "rbxassetid://113443746637817", 
                "rbxassetid://88191546545486", 
                "rbxassetid://91500255169303", 
                "rbxassetid://94063697201181", 
                "rbxassetid://98797609297769", 
                "rbxassetid://90130831849603", 
                "rbxassetid://122441670784352", 
                "rbxassetid://123735871701026", 
                "rbxassetid://133669475761822", 
                "rbxassetid://95651369904424", 
                "rbxassetid://73166444392658", 
                "rbxassetid://105632004044193", 
                "rbxassetid://100217961188472", 
                "rbxassetid://74459261126285"
            }, 
            Ember1 = {
                "rbxassetid://99108538838697", 
                "rbxassetid://80297925665371", 
                "rbxassetid://70489071590295", 
                "rbxassetid://71646336805895", 
                "rbxassetid://119966461341306", 
                "rbxassetid://110060674460396", 
                "rbxassetid://135180452945012", 
                "rbxassetid://91485112370521", 
                "rbxassetid://113128275280587", 
                "rbxassetid://73255822954602", 
                "rbxassetid://91714702299821", 
                "rbxassetid://91267742619360", 
                "rbxassetid://138024090646986", 
                "rbxassetid://77455092251910", 
                "rbxassetid://121784411356513", 
                "rbxassetid://134369258682665", 
                "rbxassetid://122867951919434", 
                "rbxassetid://86620130669727", 
                "rbxassetid://132365446438090", 
                "rbxassetid://109619508535474", 
                "rbxassetid://18799643112"
            }, 
            Ember2 = {
                "rbxassetid://17832942931", 
                "rbxassetid://17832951215", 
                "rbxassetid://17832958272", 
                "rbxassetid://17832970162", 
                "rbxassetid://17832975503", 
                "rbxassetid://17832979515", 
                "rbxassetid://17832984539", 
                "rbxassetid://17832988245", 
                "rbxassetid://17832993160", 
                "rbxassetid://17832997682", 
                "rbxassetid://17833003979", 
                "rbxassetid://17833007948", 
                "rbxassetid://17833011261", 
                "rbxassetid://17833013866", 
                "rbxassetid://17833017399", 
                "rbxassetid://17833020479"
            }
        };
        local v7307 = {
            "rbxassetid://140601110529995", 
            "rbxassetid://128950661243692", 
            "rbxassetid://86031602130843", 
            "rbxassetid://133632657886797", 
            "rbxassetid://79600287279170", 
            "rbxassetid://83398565884657", 
            "rbxassetid://85476779127610", 
            "rbxassetid://106557904321348", 
            "rbxassetid://73314997368401", 
            "rbxassetid://136617238760755", 
            "rbxassetid://138553658244738", 
            "rbxassetid://107320391250081", 
            "rbxassetid://128480144277728", 
            "rbxassetid://129288217715329", 
            "rbxassetid://94314687234427", 
            "rbxassetid://76244259107160", 
            "rbxassetid://116648104194659", 
            "rbxassetid://120061982086838", 
            "rbxassetid://127854653825186", 
            "rbxassetid://117033695973321", 
            "rbxassetid://132361061749715", 
            "rbxassetid://111589160167564", 
            "rbxassetid://119529439934188", 
            "rbxassetid://108166488545265", 
            "rbxassetid://81389753853817"
        };
        local v7308 = {
            "rbxassetid://86757610778006", 
            "rbxassetid://77160927852658", 
            "rbxassetid://73035588796758", 
            "rbxassetid://93213184749376", 
            "rbxassetid://119077567496605", 
            "rbxassetid://127754313111885", 
            "rbxassetid://85133298359199", 
            "rbxassetid://103783554881287", 
            "rbxassetid://87576482923298", 
            "rbxassetid://72250651672458", 
            "rbxassetid://124275728806912", 
            "rbxassetid://97358188358302", 
            "rbxassetid://78447328872572", 
            "rbxassetid://89339567151645", 
            "rbxassetid://115094041228004", 
            "rbxassetid://83506218648581", 
            "rbxassetid://117792342648454", 
            "rbxassetid://18799643112"
        };
        local v7309 = {
            "rbxassetid://124442681342835", 
            "rbxassetid://113158373470512", 
            "rbxassetid://82139065611087", 
            "rbxassetid://139222368327812", 
            "rbxassetid://139289084508904", 
            "rbxassetid://109458003089853", 
            "rbxassetid://73283906326161", 
            "rbxassetid://92305907449078", 
            "rbxassetid://101718516876502", 
            "rbxassetid://122165089596150", 
            "rbxassetid://112530459419992", 
            "rbxassetid://98792828606875", 
            "rbxassetid://80534326100017", 
            "rbxassetid://18799643112"
        };
        local v7310 = {
            [1] = {
                "rbxassetid://126345619062862", 
                "rbxassetid://99303886332474", 
                "rbxassetid://121637092604416", 
                "rbxassetid://113029456950748", 
                "rbxassetid://138572874693768", 
                "rbxassetid://92607920533530", 
                "rbxassetid://138142224621278", 
                "rbxassetid://120065192728444", 
                "rbxassetid://70411827948913", 
                "rbxassetid://71491448109970", 
                "rbxassetid://85524876848679", 
                "rbxassetid://84060367679631", 
                "rbxassetid://98004646393019", 
                "rbxassetid://139881862115187", 
                "rbxassetid://75646293133592", 
                "rbxassetid://82260360973815", 
                "rbxassetid://80749449158947", 
                "rbxassetid://75562819739007", 
                "rbxassetid://78560402198235", 
                "rbxassetid://134122785594190", 
                "rbxassetid://122238912891286", 
                "rbxassetid://83757815885857"
            }, 
            [2] = {
                "rbxassetid://79659437605905", 
                "rbxassetid://118290359008121", 
                "rbxassetid://93612183410156", 
                "rbxassetid://79503345167950", 
                "rbxassetid://75259404133000", 
                "rbxassetid://123404852590285", 
                "rbxassetid://89121436722950", 
                "rbxassetid://122757847245395", 
                "rbxassetid://110337433498526", 
                "rbxassetid://82000074938026", 
                "rbxassetid://96669627548575", 
                "rbxassetid://113917452449518", 
                "rbxassetid://133381373610534", 
                "rbxassetid://138651759212140", 
                "rbxassetid://120620024709807", 
                "rbxassetid://95006064285568", 
                "rbxassetid://135806306899349", 
                "rbxassetid://134049946195990", 
                "rbxassetid://77590421805120", 
                "rbxassetid://71393365951006", 
                "rbxassetid://94079431186519"
            }
        };
            local _ = v1090.victim;
            if l_LocalPlayer_0.Character == l_char_26 then
                shared.SetCore(false, nil, true);
            end;
            local function _(v7314) --[[ Line: 17451 ]] --[[ Name: createinfo ]]
                local l_v7314_Attributes_0 = v7314:GetAttributes();
                return TweenInfo.new(l_v7314_Attributes_0.time, Enum.EasingStyle[l_v7314_Attributes_0.style], Enum.EasingDirection[l_v7314_Attributes_0.direction], tonumber(l_v7314_Attributes_0.Repeat), l_v7314_Attributes_0.reverse, (tonumber(l_v7314_Attributes_0.delay)));
            end;
            local function _(v7317) --[[ Line: 17456 ]]
                local l_Highlight_1 = Instance.new("Highlight");
                l_Highlight_1.Parent = v7317;
                l_Highlight_1.OutlineTransparency = 1;
                l_Highlight_1.FillTransparency = 1;
                return l_Highlight_1;
            end;
            local l_v7294_0 = v7294 --[[ copy: 8 -> 1333 ]];
            local function v7339(v7321, v7322, v7323, v7324) --[[ Line: 17464 ]]
                -- upvalues: l_v7294_0 (copy)
                v7323 = v7323 or TweenInfo.new(0.5, Enum.EasingStyle.Linear, Enum.EasingDirection.Out);
                if v7321:IsA("ImageLabel") then

                end;
                local function _(v7325) --[[ Line: 17470 ]] --[[ Name: updateTexture ]]
                    -- upvalues: v7321 (copy), v7322 (copy)
                    local v7326 = nil;
                    v7326 = if not v7321:IsA("ImageLabel") then v7321:FindFirstChildOfClass("Decal") else v7321;
                    if v7326 then
                        local v7327 = v7322[v7325];
                        if v7327 then
                            if not v7321:IsA("ImageLabel") then
                                v7326.Texture = v7327;
                                return;
                            else
                                v7326.Image = v7327;
                            end;
                        end;
                    end;
                end;
                local v7329 = 1;
                local v7330 = #v7322;
                local l_NumberValue_4 = Instance.new("NumberValue");
                l_NumberValue_4.Value = v7329;
                local v7332 = game.TweenService:Create(l_NumberValue_4, v7323, {
                    Value = v7330
                });
                v7332:Play();
                local v7337 = l_NumberValue_4.Changed:Connect(function(v7333) --[[ Line: 17495 ]]
                    -- upvalues: v7321 (copy), v7322 (copy)
                    local v7334 = math.floor(v7333);
                    local v7335 = nil;
                    v7335 = if not v7321:IsA("ImageLabel") then v7321:FindFirstChildOfClass("Decal") else v7321;
                    if v7335 then
                        local v7336 = v7322[v7334];
                        if v7336 then
                            if not v7321:IsA("ImageLabel") then
                                v7335.Texture = v7336;
                                return;
                            else
                                v7335.Image = v7336;
                            end;
                        end;
                    end;
                end);
                table.insert(l_v7294_0, v7337);
                local v7338 = nil;
                v7338 = v7332.Completed:Once(function() --[[ Line: 17498 ]]
                    -- upvalues: l_NumberValue_4 (copy), v7324 (copy), v7337 (copy), v7338 (ref)
                    l_NumberValue_4:Destroy();
                    if v7324 then
                        task.spawn(v7324);
                    end;
                    v7337:Disconnect();
                    v7338:Disconnect();
                end);
                table.insert(l_v7294_0, v7338);
                return v7332;
            end;
            local function v7355(v7340, _) --[[ Line: 17509 ]]
                local l_info_0 = v7340:FindFirstChild("info");
                local l_goal_0 = v7340:FindFirstChild("goal");
                local v7344 = {};
                if not l_info_0 then
                    return;
                elseif not l_goal_0 then
                    return;
                else
                    local function v7347(v7345) --[[ Line: 17522 ]] --[[ Name: createinfo ]]
                        -- upvalues: l_info_0 (copy)
                        local v7346 = v7345:FindFirstChild("info") and v7345:FindFirstChild("info"):GetAttributes() or l_info_0:GetAttributes();
                        return TweenInfo.new(v7346.time, Enum.EasingStyle[v7346.style], Enum.EasingDirection[v7346.direction], tonumber(v7346.Repeat), v7346.reverse, (tonumber(v7346.delay)));
                    end;
                    for _, v7349 in l_goal_0:GetChildren() do
                        if not v7349:IsA("Folder") then
                            return;
                        else
                            local v7350 = v7347(v7349);
                            local v7351 = (not (v7349.Name ~= "Part") or v7349.Name == "MeshPart") and v7349:GetAttributes();
                            if v7351 then
                                v7351.CFrame = v7340.CFrame * v7351.CFrame;
                            end;
                            local v7352 = game.TweenService:Create(v7340:FindFirstChildOfClass(v7349.Name) or v7340, v7350, not v7340:FindFirstChildOfClass(v7349.Name) and v7351 or v7349:GetAttributes());
                            v7344[v7349.Name .. "Tween"] = v7352;
                        end;
                    end;
                    return {
                        tweens = v7344, 
                        play = function() --[[ Line: 17547 ]] --[[ Name: play ]]
                            -- upvalues: v7344 (copy)
                            for _, v7354 in v7344 do
                                v7354:Play();
                            end;
                        end
                    };
                end;
            end;
            local l_v7355_0 = v7355 --[[ copy: 29 -> 1334 ]];
            local l_v7306_0 = v7306 --[[ copy: 20 -> 1335 ]];
            local l_v7339_0 = v7339 --[[ copy: 28 -> 1336 ]];
            local function v7380(v7359, _, v7361) --[[ Line: 17555 ]]
                -- upvalues: l_v7355_0 (copy), l_v7306_0 (copy), l_v7339_0 (copy)
                local v7376 = not v7359:IsA("Model") and function(v7369) --[[ Line: 17560 ]] --[[ Name: handle ]]
                    -- upvalues: l_v7355_0 (ref), v7361 (copy), l_v7306_0 (ref), l_v7339_0 (ref)
                    -- upvalues: l_v7355_0 (ref), v7361 (copy), l_v7306_0 (ref), l_v7339_0 (ref)
                    if v7369:FindFirstChild("info") then
                        l_v7355_0(v7369).play();
                        if v7369:GetAttribute("Destroy") or v7361 then
                            task.delay(v7369.info:GetAttribute("time"), v7369.Destroy, v7369);
                        end;
                    end;
                    local l_v7369_Attribute_1 = v7369:GetAttribute("Flipbook");
                    if l_v7369_Attribute_1 and v7369:FindFirstChild("tweeninfo") and l_v7306_0[l_v7369_Attribute_1] then
                        local v7371 = l_v7306_0[l_v7369_Attribute_1];
                        local l_l_v7339_0_1 = l_v7339_0;
                        local l_v7369_1 = v7369;
                        local l_v7371_1 = v7371;
                        local l_Attributes_1 = v7369:FindFirstChild("tweeninfo"):GetAttributes();
                        l_l_v7339_0_1(l_v7369_1, l_v7371_1, TweenInfo.new(l_Attributes_1.time, Enum.EasingStyle[l_Attributes_1.style], Enum.EasingDirection[l_Attributes_1.direction], tonumber(l_Attributes_1.Repeat), l_Attributes_1.reverse, (tonumber(l_Attributes_1.delay))) or TweenInfo.new(0.5), function() --[[ Line: 17573 ]]
                            -- upvalues: v7369 (copy)
                            v7369:Destroy();
                        end);
                    end;
                end or function(v7369) --[[ Line: 17560 ]] --[[ Name: handle ]]
                    -- upvalues: l_v7355_0 (ref), v7361 (copy), l_v7306_0 (ref), l_v7339_0 (ref)
                    -- upvalues: l_v7355_0 (ref), v7361 (copy), l_v7306_0 (ref), l_v7339_0 (ref)
                    if v7369:FindFirstChild("info") then
                        l_v7355_0(v7369).play();
                        if v7369:GetAttribute("Destroy") or v7361 then
                            task.delay(v7369.info:GetAttribute("time"), v7369.Destroy, v7369);
                        end;
                    end;
                    local l_v7369_Attribute_1 = v7369:GetAttribute("Flipbook");
                    if l_v7369_Attribute_1 and v7369:FindFirstChild("tweeninfo") and l_v7306_0[l_v7369_Attribute_1] then
                        local v7371 = l_v7306_0[l_v7369_Attribute_1];
                        local l_l_v7339_0_1 = l_v7339_0;
                        local l_v7369_1 = v7369;
                        local l_v7371_1 = v7371;
                        local l_Attributes_1 = v7369:FindFirstChild("tweeninfo"):GetAttributes();
                        l_l_v7339_0_1(l_v7369_1, l_v7371_1, TweenInfo.new(l_Attributes_1.time, Enum.EasingStyle[l_Attributes_1.style], Enum.EasingDirection[l_Attributes_1.direction], tonumber(l_Attributes_1.Repeat), l_Attributes_1.reverse, (tonumber(l_Attributes_1.delay))) or TweenInfo.new(0.5), function() --[[ Line: 17573 ]]
                            -- upvalues: v7369 (copy)
                            v7369:Destroy();
                        end);
                    end;
                end;
                for _, v7378 in v7359:GetChildren() do
                    v7376(v7378);
                end;
                local l_Highlight_2 = Instance.new("Highlight");
                l_Highlight_2.Parent = v7359;
                l_Highlight_2.OutlineTransparency = 1;
                l_Highlight_2.FillTransparency = 1;
            end;
            local function v7388(v7381) --[[ Line: 17586 ]]
                -- upvalues: l_v7355_0 (copy), v10 (ref)
                for _, v7383 in v7381:GetDescendants() do
                    if v7383:IsA("Beam") then
                        if v7383:FindFirstChild("info") then
                            l_v7355_0(v7383).play();
                            if v7383:GetAttribute("Destroy") then
                                task.delay(v7383.info:GetAttribute("time"), v7383.Destroy, v7383);
                            end;
                        else
                            continue;
                        end;
                    end;
                    if v7383:IsA("CFrameValue") then
                        local l_Parent_8 = v7383.Parent;
                        local l_v10_13 = v10;
                        local l_l_Parent_8_0 = l_Parent_8;
                        local l_Attributes_2 = v7383:FindFirstChild("info"):GetAttributes();
                        l_v10_13:Create(l_l_Parent_8_0, TweenInfo.new(l_Attributes_2.time, Enum.EasingStyle[l_Attributes_2.style], Enum.EasingDirection[l_Attributes_2.direction], tonumber(l_Attributes_2.Repeat), l_Attributes_2.reverse, (tonumber(l_Attributes_2.delay))) or TweenInfo.new(0.5), {
                            CFrame = l_Parent_8:FindFirstChildOfClass("CFrameValue").Value
                        }):Play();
                    end;
                end;
            end;
            local l_l_char_26_0 = l_char_26 --[[ copy: 4 -> 1337 ]];
            local _ = function() --[[ Line: 17608 ]] --[[ Name: GetTorsoCF ]]
                -- upvalues: l_l_char_26_0 (copy)
                local _, v7391, _ = l_l_char_26_0.HumanoidRootPart.CFrame:ToOrientation();
                return CFrame.new(l_l_char_26_0.Torso.Position) * CFrame.Angles(0, v7391, 0);
            end;
            local l_QuickWeldNew_6 = QuickWeldNew;
            if v1090.hit then
                local v7395 = true;
                if v1090.hit ~= game.Players.LocalPlayer.Character then
                    v7395 = l_LocalPlayer_0.Character == l_char_26;
                end;
                local v7396 = nil;
                if v7395 then
                    shared.SetCore(false, nil, true);
                end;
                if v1090.sound and v7395 then
                    v1090.sound.Parent = workspace;
                end;
                local v7397 = v7297.RedDragon:Clone();
                v7397:PivotTo(l_char_26.PrimaryPart.CFrame * CFrame.new(3.66210938E-4, -2.99993134, -3.50952148E-4, -1, 0, 0, 0, 1, 0, 0, 0, -1));
                v7397.Parent = workspace.Thrown;
                local l_Animation_16 = Instance.new("Animation");
                l_Animation_16.AnimationId = "rbxassetid://93273199811416";
                v7397.AnimationController:LoadAnimation(l_Animation_16):Play();
                if not v7395 then
                    for _, v7400 in pairs(v7397:GetDescendants()) do
                        if v7400:IsA("MeshPart") or v7400:IsA("Part") then
                            v7400.Transparency = 1;
                        end;
                    end;
                end;
                if v7395 then
                    local v7401 = {
                        Char = l_char_26, 
                        Force = true, 
                        Anim = 84807030759844, 
                        Offset = CFrame.new(0, -3, 0), 
                        AnimSentBypass = true, 
                        SpecificRig = game.ReplicatedStorage.Emotes.VFX.Assets.cambreath, 
                        SlowAfter = 10.96, 
                        ActualPart = "CamPart", 
                        EnableShakeAfter = 10.96, 
                        SpoofedCFrame = l_char_26.PrimaryPart.CFrame, 
                        NoLerpAfter = true, 
                        LerpTime = 0.6, 
                        UseCFrameEventually = true, 
                        From = l_PrimaryPart_49
                    };
                    if l_char_26 ~= game.Players.LocalPlayer.Character then
                        v7401.From = l_char_26.PrimaryPart;
                        v7401.SpoofedCFrame = nil;
                    else
                        v7401.UseCFrameEventually = nil;
                        v7401.From = nil;
                    end;
                    v7396 = shared.CutsceneEvent(v7401);
                end;
                local l_hit_18 = v1090.hit;
                local v7403 = v7297.TrailRig:Clone();
                v7403.Parent = workspace.Thrown;
                local l_Humanoid_7 = v7403.Humanoid;
                local l_Animation_17 = Instance.new("Animation");
                l_Animation_17.AnimationId = "rbxassetid://122459294904623";
                l_Humanoid_7:LoadAnimation(l_Animation_17):Play();
                v7403:PivotTo(l_char_26.PrimaryPart.CFrame * CFrame.new(3.54766846E-4, -2.99993873, -3.48567963E-4, 1, 0, 0, 0, 1, 0, 0, 0, 1));
                local _ = function() --[[ Line: 17686 ]] --[[ Name: GetVictimTorsoCF ]]
                    -- upvalues: l_hit_18 (copy), l_char_26 (copy)
                    local _, v7407, _ = l_hit_18.HumanoidRootPart.CFrame:ToOrientation();
                    return CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7407, 0);
                end;
                task.delay(0.6839999999999993, function() --[[ Line: 17692 ]]
                    -- upvalues: v1000 (ref), v7297 (copy), v7298 (copy), l_hit_18 (copy), l_char_26 (copy), l_PrimaryPart_49 (copy), v989 (ref), v1014 (ref)
                    local l_v1000_7 = v1000;
                    local v7411 = {
                        FX = v7297.Bounce, 
                        Maid = v7298._maid
                    };
                    local _, v7413, _ = l_hit_18.HumanoidRootPart.CFrame:ToOrientation();
                    v7411.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7413, 0) * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 3);
                    l_v1000_7 = l_v1000_7(v7411);
                    l_v1000_7:ScaleTo(1.5);
                    v989({
                        FX = l_v1000_7, 
                        Scale = 1.5
                    });
                    v1014(l_v1000_7);
                end);
                local v7415 = {};
                task.delay(1.4499999999999993, function() --[[ Line: 17700 ]]
                    -- upvalues: l_char_26 (copy), v7415 (copy), v7297 (copy), v7403 (copy), l_Thrown_23 (copy), v7302 (copy), v7310 (copy), l_wait_19 (copy)
                    for _, v7417 in pairs(l_char_26:GetDescendants()) do
                        if (v7417:IsA("BasePart") or v7417:IsA("Decal")) and v7417.Transparency < 1 then
                            if not v7415[v7417] then
                                v7415[v7417] = v7417.Transparency;
                            end;
                            v7417.Transparency = 1;
                        end;
                    end;
                    local function v7424() --[[ Line: 17710 ]] --[[ Name: Swirls ]]
                        -- upvalues: v7297 (ref), v7403 (ref), l_Thrown_23 (ref), v7302 (ref), v7310 (ref), l_wait_19 (ref)
                        for _ = 1, 5 do
                            task.spawn(function() --[[ Line: 17721 ]]
                                -- upvalues: v7297 (ref), v7403 (ref), l_Thrown_23 (ref), v7302 (ref), v7310 (ref), l_wait_19 (ref)
                                local v7419 = v7297.Swirlnormalfaceorigin:Clone();
                                local l_Mesh_1 = v7419.Mesh;
                                l_Mesh_1.Scale = l_Mesh_1.Scale * 3;
                                v7419:PivotTo(v7403.Part.A0.WorldCFrame * CFrame.new(0, 0, -10) * CFrame.Angles(math.rad((math.random(-180, 180))), math.rad((math.random(-180, 180))), (math.rad((math.random(-180, 180))))));
                                v7419.Parent = l_Thrown_23;
                                game.Debris:AddItem(v7419, 10);
                                l_Mesh_1 = nil;
                                l_Mesh_1 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                                local v7421 = v7310[math.random(1, 2)];
                                task.spawn(function() --[[ Line: 17737 ]]
                                    -- upvalues: v7421 (copy), v7419 (copy), l_wait_19 (ref)
                                    for v7422 = 1, #v7421, 4 do
                                        local v7423 = v7421[v7422];
                                        if v7423 then
                                            v7419.Decal.Texture = v7423;
                                        end;
                                        l_wait_19(0.01);
                                    end;
                                    v7419:Destroy();
                                end);
                            end);
                            task.wait(0.05);
                        end;
                    end;
                    task.spawn(function() --[[ Line: 17756 ]]
                        -- upvalues: v7424 (copy), l_wait_19 (ref)
                        local v7425 = tick();
                        while tick() - v7425 < 1 do
                            task.spawn(v7424);
                            l_wait_19(0.1);
                        end;
                    end);
                end);
                task.delay(2.284, function() --[[ Line: 17765 ]]
                    -- upvalues: v7415 (copy), l_QuickWeldNew_6 (copy), l_LastBreath_0 (copy), v7298 (copy), l_char_26 (copy), v1021 (ref), v989 (ref), l_TweenService_26 (copy), v1000 (ref), v7297 (copy), v1014 (ref), l_PrimaryPart_49 (copy)
                    for v7426, v7427 in pairs(v7415) do
                        v7426.Transparency = v7427;
                    end;
                    local v7428 = l_QuickWeldNew_6({
                        FX = l_LastBreath_0.Arm, 
                        Maid = v7298._maid, 
                        P = l_char_26["Right Arm"]
                    });
                    v1021({
                        FX = v7428, 
                        On = true
                    });
                    v989({
                        FX = v7428, 
                        Scale = 0.5
                    });
                    task.delay(0.3, function() --[[ Line: 17773 ]]
                        -- upvalues: v7428 (copy), l_TweenService_26 (ref)
                        for _, v7430 in pairs(v7428:GetDescendants()) do
                            if v7430:IsA("PointLight") then
                                l_TweenService_26:Create(v7430, TweenInfo.new(0.3, Enum.EasingStyle.Sine), {
                                    Brightness = 0
                                }):Play();
                            elseif v7430:IsA("ParticleEmitter") then
                                v7430.Enabled = false;
                            end;
                        end;
                    end);
                    local l_v1000_8 = v1000;
                    local v7432 = {
                        FX = v7297.AirTP, 
                        Maid = v7298._maid
                    };
                    local _, v7434, v7435 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7432.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7434, 0) * CFrame.new(0, 0, 0);
                    l_v1000_8 = l_v1000_8(v7432);
                    v1014(l_v1000_8);
                    v7432 = v1000;
                    local v7436 = {
                        FX = v7297.GroundTP, 
                        Maid = v7298._maid
                    };
                    local v7437;
                    v7434, v7435, v7437 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7436.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7435, 0) * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 0);
                    v7432 = v7432(v7436);
                    v1014(v7432);
                end);
                task.delay(2.749999999999999, function() --[[ Line: 17790 ]]
                    -- upvalues: v7397 (copy), v7395 (copy), l_LastBreath_0 (copy), v7294 (copy), l_PrimaryPart_49 (copy), v1000 (ref), v7298 (copy), v989 (ref), v1014 (ref), l_wait_19 (copy), l_char_26 (copy), v7297 (copy), l_hit_18 (copy)
                    for _, v7439 in pairs(v7397:GetChildren()) do
                        if v7439.Name ~= "RootPart" and v7439:IsA("BasePart") and v7395 then
                            v7439.Transparency = 0;
                        end;
                    end;
                    local function v7508() --[[ Line: 17797 ]] --[[ Name: OldHitEvent ]]
                        -- upvalues: l_LastBreath_0 (ref), v7294 (ref), l_PrimaryPart_49 (ref), v1000 (ref), v7298 (ref), v989 (ref), v1014 (ref), l_wait_19 (ref)
                        local v7440 = l_LastBreath_0.PreCam.BIGGER:Clone();
                        table.insert(v7294, v7440);
                        v7440:ScaleTo(1.8);
                        local v7441 = {
                            Model = v7440, 
                            EndT = 0, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -140) * CFrame.new(0, 5.7, -10) * CFrame.Angles(-1.1344640137963142, 1.5707963267948966, 0) * CFrame.new(50, 0, 0), 
                            Info = TweenInfo.new(0.1, Enum.EasingStyle.Exponential)
                        };
                        local l_v7441_0 = v7441 --[[ copy: 1 -> 5 ]];
                        task.spawn(function() --[[ Line: 4475 ]]
                            -- upvalues: l_v7441_0 (copy)
                            local l_Model_147 = l_v7441_0.Model;
                            local v7444 = l_v7441_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                            local l_Start_153 = l_Model_147:FindFirstChild("Start");
                            local l_End_151 = l_Model_147:FindFirstChild("End");
                            local l_Stay_146 = l_v7441_0.Stay;
                            local l_Anchor_147 = l_v7441_0.Anchor;
                            local v7449 = l_v7441_0.EndT or 1;
                            local l_Del_146 = l_v7441_0.Del;
                            local l_Skip_146 = l_v7441_0.Skip;
                            if l_Start_153 and l_End_151 then
                                l_Model_147.PrimaryPart = l_Start_153;
                                if not l_Skip_146 then
                                    for _, v7453 in pairs(l_Model_147:GetChildren()) do
                                        if v7453:IsA("BasePart") then
                                            v7453.CanCollide = false;
                                            v7453.Anchored = true;
                                        end;
                                    end;
                                end;
                                if l_Anchor_147 then
                                    l_Model_147:SetPrimaryPartCFrame(l_Anchor_147);
                                end;
                                if l_v7441_0.T then
                                    l_Start_153.Transparency = l_v7441_0.T;
                                end;
                                l_End_151.Transparency = 1;
                                l_Model_147.Parent = workspace.Thrown;
                                local l_Decal_294 = l_Start_153:FindFirstChildOfClass("Decal");
                                local l_SpecialMesh_294 = l_Start_153:FindFirstChildOfClass("SpecialMesh");
                                local l_SpecialMesh_295 = l_End_151:FindFirstChildOfClass("SpecialMesh");
                                local l_Decal_295 = l_End_151:FindFirstChildOfClass("Decal");
                                if l_Decal_295 and not l_Skip_146 then
                                    l_Decal_295.Transparency = 1;
                                end;
                                local v7458 = nil;
                                if l_Del_146 then
                                    game:GetService("TweenService"):Create(l_Start_153, v7444, {
                                        Size = l_End_151.Size, 
                                        CFrame = l_End_151.CFrame
                                    }):Play();
                                    task.delay(l_Del_146, function() --[[ Line: 4519 ]]
                                        -- upvalues: v7458 (ref), l_Start_153 (copy), v7444 (copy), v7449 (copy), l_Decal_294 (copy), l_SpecialMesh_294 (copy), l_SpecialMesh_295 (copy)
                                        v7458 = game:GetService("TweenService"):Create(l_Start_153, v7444, {
                                            Transparency = v7449
                                        });
                                        v7458:Play();
                                        if l_Decal_294 then
                                            for _, v7460 in pairs(l_Start_153:GetChildren()) do
                                                if v7460:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7460, v7444, {
                                                        Transparency = v7449
                                                    }):Play();
                                                end;
                                            end;
                                        end;
                                        if l_SpecialMesh_294 then
                                            v7458 = game:GetService("TweenService"):Create(l_SpecialMesh_294, v7444, {
                                                Scale = l_SpecialMesh_295.Scale
                                            }):Play();
                                        end;
                                    end);
                                else
                                    if l_SpecialMesh_294 then
                                        game:GetService("TweenService"):Create(l_SpecialMesh_294, v7444, {
                                            Scale = l_SpecialMesh_295.Scale
                                        }):Play();
                                    end;
                                    if l_Decal_294 then
                                        for _, v7462 in pairs(l_Start_153:GetChildren()) do
                                            if v7462:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v7462, v7444, {
                                                    Transparency = v7449
                                                }):Play();
                                            end;
                                        end;
                                        v7458 = game:GetService("TweenService"):Create(l_Start_153, v7444, {
                                            Size = l_End_151.Size, 
                                            CFrame = l_End_151.CFrame
                                        });
                                        v7458:Play();
                                    else
                                        v7458 = game:GetService("TweenService"):Create(l_Start_153, v7444, {
                                            Size = l_End_151.Size, 
                                            Transparency = v7449, 
                                            CFrame = l_End_151.CFrame
                                        });
                                        v7458:Play();
                                    end;
                                end;
                                if not l_Stay_146 then
                                    if l_Del_146 then
                                        task.wait(l_Del_146 + 0.1);
                                    end;
                                    v7458.Completed:Connect(function() --[[ Line: 4562 ]]
                                        -- upvalues: l_Model_147 (copy)
                                        l_Model_147:Destroy();
                                    end);
                                end;
                                return;
                            else
                                warn("NO START OR END");
                                return;
                            end;
                        end);
                        v7441 = l_LastBreath_0.PreCam.AngleHit:Clone();
                        table.insert(v7294, v7441);
                        v7441:ScaleTo(0.5);
                        local v7463 = {
                            Model = v7441, 
                            T = 0.5, 
                            EndT = 1, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -160) * CFrame.new(0, 5.7, -10) * CFrame.Angles(-1.1344640137963142, 3.141592653589793, 0) * CFrame.new(0, 0, 0), 
                            Info = TweenInfo.new(1, Enum.EasingStyle.Exponential)
                        };
                        local l_v7463_0 = v7463 --[[ copy: 2 -> 6 ]];
                        task.spawn(function() --[[ Line: 4475 ]]
                            -- upvalues: l_v7463_0 (copy)
                            local l_Model_148 = l_v7463_0.Model;
                            local v7466 = l_v7463_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                            local l_Start_154 = l_Model_148:FindFirstChild("Start");
                            local l_End_152 = l_Model_148:FindFirstChild("End");
                            local l_Stay_147 = l_v7463_0.Stay;
                            local l_Anchor_148 = l_v7463_0.Anchor;
                            local v7471 = l_v7463_0.EndT or 1;
                            local l_Del_147 = l_v7463_0.Del;
                            local l_Skip_147 = l_v7463_0.Skip;
                            if l_Start_154 and l_End_152 then
                                l_Model_148.PrimaryPart = l_Start_154;
                                if not l_Skip_147 then
                                    for _, v7475 in pairs(l_Model_148:GetChildren()) do
                                        if v7475:IsA("BasePart") then
                                            v7475.CanCollide = false;
                                            v7475.Anchored = true;
                                        end;
                                    end;
                                end;
                                if l_Anchor_148 then
                                    l_Model_148:SetPrimaryPartCFrame(l_Anchor_148);
                                end;
                                if l_v7463_0.T then
                                    l_Start_154.Transparency = l_v7463_0.T;
                                end;
                                l_End_152.Transparency = 1;
                                l_Model_148.Parent = workspace.Thrown;
                                local l_Decal_296 = l_Start_154:FindFirstChildOfClass("Decal");
                                local l_SpecialMesh_296 = l_Start_154:FindFirstChildOfClass("SpecialMesh");
                                local l_SpecialMesh_297 = l_End_152:FindFirstChildOfClass("SpecialMesh");
                                local l_Decal_297 = l_End_152:FindFirstChildOfClass("Decal");
                                if l_Decal_297 and not l_Skip_147 then
                                    l_Decal_297.Transparency = 1;
                                end;
                                local v7480 = nil;
                                if l_Del_147 then
                                    game:GetService("TweenService"):Create(l_Start_154, v7466, {
                                        Size = l_End_152.Size, 
                                        CFrame = l_End_152.CFrame
                                    }):Play();
                                    task.delay(l_Del_147, function() --[[ Line: 4519 ]]
                                        -- upvalues: v7480 (ref), l_Start_154 (copy), v7466 (copy), v7471 (copy), l_Decal_296 (copy), l_SpecialMesh_296 (copy), l_SpecialMesh_297 (copy)
                                        v7480 = game:GetService("TweenService"):Create(l_Start_154, v7466, {
                                            Transparency = v7471
                                        });
                                        v7480:Play();
                                        if l_Decal_296 then
                                            for _, v7482 in pairs(l_Start_154:GetChildren()) do
                                                if v7482:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7482, v7466, {
                                                        Transparency = v7471
                                                    }):Play();
                                                end;
                                            end;
                                        end;
                                        if l_SpecialMesh_296 then
                                            v7480 = game:GetService("TweenService"):Create(l_SpecialMesh_296, v7466, {
                                                Scale = l_SpecialMesh_297.Scale
                                            }):Play();
                                        end;
                                    end);
                                else
                                    if l_SpecialMesh_296 then
                                        game:GetService("TweenService"):Create(l_SpecialMesh_296, v7466, {
                                            Scale = l_SpecialMesh_297.Scale
                                        }):Play();
                                    end;
                                    if l_Decal_296 then
                                        for _, v7484 in pairs(l_Start_154:GetChildren()) do
                                            if v7484:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v7484, v7466, {
                                                    Transparency = v7471
                                                }):Play();
                                            end;
                                        end;
                                        v7480 = game:GetService("TweenService"):Create(l_Start_154, v7466, {
                                            Size = l_End_152.Size, 
                                            CFrame = l_End_152.CFrame
                                        });
                                        v7480:Play();
                                    else
                                        v7480 = game:GetService("TweenService"):Create(l_Start_154, v7466, {
                                            Size = l_End_152.Size, 
                                            Transparency = v7471, 
                                            CFrame = l_End_152.CFrame
                                        });
                                        v7480:Play();
                                    end;
                                end;
                                if not l_Stay_147 then
                                    if l_Del_147 then
                                        task.wait(l_Del_147 + 0.1);
                                    end;
                                    v7480.Completed:Connect(function() --[[ Line: 4562 ]]
                                        -- upvalues: l_Model_148 (copy)
                                        l_Model_148:Destroy();
                                    end);
                                end;
                                return;
                            else
                                warn("NO START OR END");
                                return;
                            end;
                        end);
                        v7463 = l_LastBreath_0.PreCam.AngleHit:Clone();
                        table.insert(v7294, v7463);
                        v7463:ScaleTo(0.9);
                        local v7485 = {
                            Model = v7463, 
                            T = 0.5, 
                            EndT = 1, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -160) * CFrame.new(0, 5.7, -10) * CFrame.Angles(-1.1344640137963142, 3.141592653589793, 0) * CFrame.new(0, 0, 0), 
                            Info = TweenInfo.new(1.2, Enum.EasingStyle.Exponential)
                        };
                        local l_v7485_0 = v7485 --[[ copy: 3 -> 7 ]];
                        task.spawn(function() --[[ Line: 4475 ]]
                            -- upvalues: l_v7485_0 (copy)
                            local l_Model_149 = l_v7485_0.Model;
                            local v7488 = l_v7485_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                            local l_Start_155 = l_Model_149:FindFirstChild("Start");
                            local l_End_153 = l_Model_149:FindFirstChild("End");
                            local l_Stay_148 = l_v7485_0.Stay;
                            local l_Anchor_149 = l_v7485_0.Anchor;
                            local v7493 = l_v7485_0.EndT or 1;
                            local l_Del_148 = l_v7485_0.Del;
                            local l_Skip_148 = l_v7485_0.Skip;
                            if l_Start_155 and l_End_153 then
                                l_Model_149.PrimaryPart = l_Start_155;
                                if not l_Skip_148 then
                                    for _, v7497 in pairs(l_Model_149:GetChildren()) do
                                        if v7497:IsA("BasePart") then
                                            v7497.CanCollide = false;
                                            v7497.Anchored = true;
                                        end;
                                    end;
                                end;
                                if l_Anchor_149 then
                                    l_Model_149:SetPrimaryPartCFrame(l_Anchor_149);
                                end;
                                if l_v7485_0.T then
                                    l_Start_155.Transparency = l_v7485_0.T;
                                end;
                                l_End_153.Transparency = 1;
                                l_Model_149.Parent = workspace.Thrown;
                                local l_Decal_298 = l_Start_155:FindFirstChildOfClass("Decal");
                                local l_SpecialMesh_298 = l_Start_155:FindFirstChildOfClass("SpecialMesh");
                                local l_SpecialMesh_299 = l_End_153:FindFirstChildOfClass("SpecialMesh");
                                local l_Decal_299 = l_End_153:FindFirstChildOfClass("Decal");
                                if l_Decal_299 and not l_Skip_148 then
                                    l_Decal_299.Transparency = 1;
                                end;
                                local v7502 = nil;
                                if l_Del_148 then
                                    game:GetService("TweenService"):Create(l_Start_155, v7488, {
                                        Size = l_End_153.Size, 
                                        CFrame = l_End_153.CFrame
                                    }):Play();
                                    task.delay(l_Del_148, function() --[[ Line: 4519 ]]
                                        -- upvalues: v7502 (ref), l_Start_155 (copy), v7488 (copy), v7493 (copy), l_Decal_298 (copy), l_SpecialMesh_298 (copy), l_SpecialMesh_299 (copy)
                                        v7502 = game:GetService("TweenService"):Create(l_Start_155, v7488, {
                                            Transparency = v7493
                                        });
                                        v7502:Play();
                                        if l_Decal_298 then
                                            for _, v7504 in pairs(l_Start_155:GetChildren()) do
                                                if v7504:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7504, v7488, {
                                                        Transparency = v7493
                                                    }):Play();
                                                end;
                                            end;
                                        end;
                                        if l_SpecialMesh_298 then
                                            v7502 = game:GetService("TweenService"):Create(l_SpecialMesh_298, v7488, {
                                                Scale = l_SpecialMesh_299.Scale
                                            }):Play();
                                        end;
                                    end);
                                else
                                    if l_SpecialMesh_298 then
                                        game:GetService("TweenService"):Create(l_SpecialMesh_298, v7488, {
                                            Scale = l_SpecialMesh_299.Scale
                                        }):Play();
                                    end;
                                    if l_Decal_298 then
                                        for _, v7506 in pairs(l_Start_155:GetChildren()) do
                                            if v7506:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v7506, v7488, {
                                                    Transparency = v7493
                                                }):Play();
                                            end;
                                        end;
                                        v7502 = game:GetService("TweenService"):Create(l_Start_155, v7488, {
                                            Size = l_End_153.Size, 
                                            CFrame = l_End_153.CFrame
                                        });
                                        v7502:Play();
                                    else
                                        v7502 = game:GetService("TweenService"):Create(l_Start_155, v7488, {
                                            Size = l_End_153.Size, 
                                            Transparency = v7493, 
                                            CFrame = l_End_153.CFrame
                                        });
                                        v7502:Play();
                                    end;
                                end;
                                if not l_Stay_148 then
                                    if l_Del_148 then
                                        task.wait(l_Del_148 + 0.1);
                                    end;
                                    v7502.Completed:Connect(function() --[[ Line: 4562 ]]
                                        -- upvalues: l_Model_149 (copy)
                                        l_Model_149:Destroy();
                                    end);
                                end;
                                return;
                            else
                                warn("NO START OR END");
                                return;
                            end;
                        end);
                        v7485 = v1000({
                            FX = l_LastBreath_0.PreCam.ForwardBlast, 
                            Maid = v7298._maid, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -150) * CFrame.Angles(2.356194490192345, 0, 0)
                        });
                        v989({
                            FX = v7485, 
                            Scale = 0.5
                        });
                        v7485:ScaleTo(1.8);
                        table.insert(v7294, v7485);
                        v1014(v7485);
                        local v7507 = v1000({
                            FX = l_LastBreath_0.PreCam.Floor, 
                            Maid = v7298._maid, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, -160) * CFrame.Angles(0, 0, 0)
                        });
                        v1014(v7507);
                        table.insert(v7294, v7507);
                        l_wait_19(0.05);
                    end;
                    local _ = function() --[[ Line: 17834 ]] --[[ Name: UpEvent ]]
                        -- upvalues: l_char_26 (ref), v1000 (ref), v7297 (ref), l_PrimaryPart_49 (ref), v7298 (ref), v989 (ref), v1014 (ref), l_wait_19 (ref)
                        task.spawn(function() --[[ Line: 17838 ]]
                            -- upvalues: l_char_26 (ref), v1000 (ref), v7297 (ref), l_PrimaryPart_49 (ref), v7298 (ref), v989 (ref), v1014 (ref), l_wait_19 (ref)
                            local v7509, v7510, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            local v7512 = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7510, 0) * CFrame.Angles(0.4363323129985824, 0, 0);
                            local v7513 = v1000({
                                FX = v7297.GoodWind, 
                                Anchor = v7512 * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 0), 
                                Maid = v7298._maid
                            });
                            v7513:ScaleTo(0.3);
                            v989({
                                FX = v7513, 
                                Scale = 2
                            });
                            v1014(v7513);
                            v7509 = v7297.Hmm3:Clone();
                            v7509:ScaleTo(4);
                            v7510 = {
                                Model = v7509, 
                                T = 0.7, 
                                Anchor = v7512 * CFrame.new(0, 0, 0) * CFrame.Angles(0, 0, 0), 
                                Info = TweenInfo.new(5, Enum.EasingStyle.Exponential)
                            };
                            task.spawn(function() --[[ Line: 4475 ]]
                                -- upvalues: v7510 (copy)
                                local l_Model_150 = v7510.Model;
                                local v7515 = v7510.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                                local l_Start_156 = l_Model_150:FindFirstChild("Start");
                                local l_End_154 = l_Model_150:FindFirstChild("End");
                                local l_Stay_149 = v7510.Stay;
                                local l_Anchor_150 = v7510.Anchor;
                                local v7520 = v7510.EndT or 1;
                                local l_Del_149 = v7510.Del;
                                local l_Skip_149 = v7510.Skip;
                                if l_Start_156 and l_End_154 then
                                    l_Model_150.PrimaryPart = l_Start_156;
                                    if not l_Skip_149 then
                                        for _, v7524 in pairs(l_Model_150:GetChildren()) do
                                            if v7524:IsA("BasePart") then
                                                v7524.CanCollide = false;
                                                v7524.Anchored = true;
                                            end;
                                        end;
                                    end;
                                    if l_Anchor_150 then
                                        l_Model_150:SetPrimaryPartCFrame(l_Anchor_150);
                                    end;
                                    if v7510.T then
                                        l_Start_156.Transparency = v7510.T;
                                    end;
                                    l_End_154.Transparency = 1;
                                    l_Model_150.Parent = workspace.Thrown;
                                    local l_Decal_300 = l_Start_156:FindFirstChildOfClass("Decal");
                                    local l_SpecialMesh_300 = l_Start_156:FindFirstChildOfClass("SpecialMesh");
                                    local l_SpecialMesh_301 = l_End_154:FindFirstChildOfClass("SpecialMesh");
                                    local l_Decal_301 = l_End_154:FindFirstChildOfClass("Decal");
                                    if l_Decal_301 and not l_Skip_149 then
                                        l_Decal_301.Transparency = 1;
                                    end;
                                    local v7529 = nil;
                                    if l_Del_149 then
                                        game:GetService("TweenService"):Create(l_Start_156, v7515, {
                                            Size = l_End_154.Size, 
                                            CFrame = l_End_154.CFrame
                                        }):Play();
                                        task.delay(l_Del_149, function() --[[ Line: 4519 ]]
                                            -- upvalues: v7529 (ref), l_Start_156 (copy), v7515 (copy), v7520 (copy), l_Decal_300 (copy), l_SpecialMesh_300 (copy), l_SpecialMesh_301 (copy)
                                            v7529 = game:GetService("TweenService"):Create(l_Start_156, v7515, {
                                                Transparency = v7520
                                            });
                                            v7529:Play();
                                            if l_Decal_300 then
                                                for _, v7531 in pairs(l_Start_156:GetChildren()) do
                                                    if v7531:IsA("Decal") then
                                                        game:GetService("TweenService"):Create(v7531, v7515, {
                                                            Transparency = v7520
                                                        }):Play();
                                                    end;
                                                end;
                                            end;
                                            if l_SpecialMesh_300 then
                                                v7529 = game:GetService("TweenService"):Create(l_SpecialMesh_300, v7515, {
                                                    Scale = l_SpecialMesh_301.Scale
                                                }):Play();
                                            end;
                                        end);
                                    else
                                        if l_SpecialMesh_300 then
                                            game:GetService("TweenService"):Create(l_SpecialMesh_300, v7515, {
                                                Scale = l_SpecialMesh_301.Scale
                                            }):Play();
                                        end;
                                        if l_Decal_300 then
                                            for _, v7533 in pairs(l_Start_156:GetChildren()) do
                                                if v7533:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7533, v7515, {
                                                        Transparency = v7520
                                                    }):Play();
                                                end;
                                            end;
                                            v7529 = game:GetService("TweenService"):Create(l_Start_156, v7515, {
                                                Size = l_End_154.Size, 
                                                CFrame = l_End_154.CFrame
                                            });
                                            v7529:Play();
                                        else
                                            v7529 = game:GetService("TweenService"):Create(l_Start_156, v7515, {
                                                Size = l_End_154.Size, 
                                                Transparency = v7520, 
                                                CFrame = l_End_154.CFrame
                                            });
                                            v7529:Play();
                                        end;
                                    end;
                                    if not l_Stay_149 then
                                        if l_Del_149 then
                                            task.wait(l_Del_149 + 0.1);
                                        end;
                                        v7529.Completed:Connect(function() --[[ Line: 4562 ]]
                                            -- upvalues: l_Model_150 (copy)
                                            l_Model_150:Destroy();
                                        end);
                                    end;
                                    return;
                                else
                                    warn("NO START OR END");
                                    return;
                                end;
                            end);
                            for v7534 = 1, 3 do
                                local v7535 = v1000({
                                    FX = v7297.Up, 
                                    Anchor = v7512 * CFrame.new(0, 20 * v7534, 0), 
                                    Maid = v7298._maid
                                });
                                v989({
                                    FX = v7535, 
                                    Scale = 0.3
                                });
                                v1014(v7535);
                                l_wait_19(0.2);
                            end;
                        end);
                    end;
                    v7508();
                    local v7537 = v7298._maid:give(Instance.new("Highlight"));
                    v7537.FillColor = Color3.fromRGB(0, 0, 0);
                    v7537.OutlineTransparency = 1;
                    game.Debris:AddItem(v7537, 0.08);
                    v7537.Parent = l_char_26;
                    local v7538 = v7298._maid:give(Instance.new("Highlight"));
                    v7538.FillColor = Color3.fromRGB(0, 0, 0);
                    v7538.OutlineTransparency = 1;
                    game.Debris:AddItem(v7538, 0.08);
                    v7538.Parent = l_hit_18;
                    task.spawn(function() --[[ Line: 17838 ]]
                        -- upvalues: l_char_26 (ref), v1000 (ref), v7297 (ref), l_PrimaryPart_49 (ref), v7298 (ref), v989 (ref), v1014 (ref), l_wait_19 (ref)
                        local v7539, v7540, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        local v7542 = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7540, 0) * CFrame.Angles(0.4363323129985824, 0, 0);
                        local v7543 = v1000({
                            FX = v7297.GoodWind, 
                            Anchor = v7542 * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 0), 
                            Maid = v7298._maid
                        });
                        v7543:ScaleTo(0.3);
                        v989({
                            FX = v7543, 
                            Scale = 2
                        });
                        v1014(v7543);
                        v7539 = v7297.Hmm3:Clone();
                        v7539:ScaleTo(4);
                        v7540 = {
                            Model = v7539, 
                            T = 0.7, 
                            Anchor = v7542 * CFrame.new(0, 0, 0) * CFrame.Angles(0, 0, 0), 
                            Info = TweenInfo.new(5, Enum.EasingStyle.Exponential)
                        };
                        task.spawn(function() --[[ Line: 4475 ]]
                            -- upvalues: v7540 (copy)
                            local l_Model_151 = v7540.Model;
                            local v7545 = v7540.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                            local l_Start_157 = l_Model_151:FindFirstChild("Start");
                            local l_End_155 = l_Model_151:FindFirstChild("End");
                            local l_Stay_150 = v7540.Stay;
                            local l_Anchor_151 = v7540.Anchor;
                            local v7550 = v7540.EndT or 1;
                            local l_Del_150 = v7540.Del;
                            local l_Skip_150 = v7540.Skip;
                            if l_Start_157 and l_End_155 then
                                l_Model_151.PrimaryPart = l_Start_157;
                                if not l_Skip_150 then
                                    for _, v7554 in pairs(l_Model_151:GetChildren()) do
                                        if v7554:IsA("BasePart") then
                                            v7554.CanCollide = false;
                                            v7554.Anchored = true;
                                        end;
                                    end;
                                end;
                                if l_Anchor_151 then
                                    l_Model_151:SetPrimaryPartCFrame(l_Anchor_151);
                                end;
                                if v7540.T then
                                    l_Start_157.Transparency = v7540.T;
                                end;
                                l_End_155.Transparency = 1;
                                l_Model_151.Parent = workspace.Thrown;
                                local l_Decal_302 = l_Start_157:FindFirstChildOfClass("Decal");
                                local l_SpecialMesh_302 = l_Start_157:FindFirstChildOfClass("SpecialMesh");
                                local l_SpecialMesh_303 = l_End_155:FindFirstChildOfClass("SpecialMesh");
                                local l_Decal_303 = l_End_155:FindFirstChildOfClass("Decal");
                                if l_Decal_303 and not l_Skip_150 then
                                    l_Decal_303.Transparency = 1;
                                end;
                                local v7559 = nil;
                                if l_Del_150 then
                                    game:GetService("TweenService"):Create(l_Start_157, v7545, {
                                        Size = l_End_155.Size, 
                                        CFrame = l_End_155.CFrame
                                    }):Play();
                                    task.delay(l_Del_150, function() --[[ Line: 4519 ]]
                                        -- upvalues: v7559 (ref), l_Start_157 (copy), v7545 (copy), v7550 (copy), l_Decal_302 (copy), l_SpecialMesh_302 (copy), l_SpecialMesh_303 (copy)
                                        v7559 = game:GetService("TweenService"):Create(l_Start_157, v7545, {
                                            Transparency = v7550
                                        });
                                        v7559:Play();
                                        if l_Decal_302 then
                                            for _, v7561 in pairs(l_Start_157:GetChildren()) do
                                                if v7561:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7561, v7545, {
                                                        Transparency = v7550
                                                    }):Play();
                                                end;
                                            end;
                                        end;
                                        if l_SpecialMesh_302 then
                                            v7559 = game:GetService("TweenService"):Create(l_SpecialMesh_302, v7545, {
                                                Scale = l_SpecialMesh_303.Scale
                                            }):Play();
                                        end;
                                    end);
                                else
                                    if l_SpecialMesh_302 then
                                        game:GetService("TweenService"):Create(l_SpecialMesh_302, v7545, {
                                            Scale = l_SpecialMesh_303.Scale
                                        }):Play();
                                    end;
                                    if l_Decal_302 then
                                        for _, v7563 in pairs(l_Start_157:GetChildren()) do
                                            if v7563:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v7563, v7545, {
                                                    Transparency = v7550
                                                }):Play();
                                            end;
                                        end;
                                        v7559 = game:GetService("TweenService"):Create(l_Start_157, v7545, {
                                            Size = l_End_155.Size, 
                                            CFrame = l_End_155.CFrame
                                        });
                                        v7559:Play();
                                    else
                                        v7559 = game:GetService("TweenService"):Create(l_Start_157, v7545, {
                                            Size = l_End_155.Size, 
                                            Transparency = v7550, 
                                            CFrame = l_End_155.CFrame
                                        });
                                        v7559:Play();
                                    end;
                                end;
                                if not l_Stay_150 then
                                    if l_Del_150 then
                                        task.wait(l_Del_150 + 0.1);
                                    end;
                                    v7559.Completed:Connect(function() --[[ Line: 4562 ]]
                                        -- upvalues: l_Model_151 (copy)
                                        l_Model_151:Destroy();
                                    end);
                                end;
                                return;
                            else
                                warn("NO START OR END");
                                return;
                            end;
                        end);
                        for v7564 = 1, 3 do
                            local v7565 = v1000({
                                FX = v7297.Up, 
                                Anchor = v7542 * CFrame.new(0, 20 * v7564, 0), 
                                Maid = v7298._maid
                            });
                            v989({
                                FX = v7565, 
                                Scale = 0.3
                            });
                            v1014(v7565);
                            l_wait_19(0.2);
                        end;
                    end);
                end);
                task.delay(3.7169999999999996, function() --[[ Line: 17889 ]]
                    -- upvalues: v1000 (ref), v7297 (copy), v7298 (copy), l_char_26 (copy), l_wait_19 (copy), v1021 (ref), v989 (ref), v7397 (copy), v7395 (copy), v1014 (ref)
                    local l_v1000_9 = v1000;
                    local v7567 = {
                        FX = v7297.Star, 
                        Maid = v7298._maid
                    };
                    local v7568, v7569, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7567.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7569, 0);
                    l_v1000_9 = l_v1000_9(v7567);
                    v7567 = tick();
                    task.spawn(function() --[[ Line: 17894 ]]
                        -- upvalues: v7567 (copy), l_v1000_9 (copy), l_char_26 (ref), l_wait_19 (ref)
                        while tick() - v7567 < 0.7 do
                            l_v1000_9:PivotTo(l_char_26["Right Leg"].CFrame * CFrame.new(0, -1, 0));
                            l_wait_19(0.01);
                        end;
                    end);
                    v1021({
                        FX = l_v1000_9, 
                        On = true
                    });
                    l_wait_19(0.315);
                    v989({
                        FX = l_v1000_9, 
                        Scale = 5
                    });
                    l_v1000_9.Star.Attachment.impact:Destroy();
                    l_v1000_9.Star.Attachment.waveimpact:Destroy();
                    l_v1000_9.Star.Attachment.star:Emit(1);
                    l_v1000_9:ScaleTo(6);
                    l_wait_19(0.06999999999999999);
                    v1021({
                        FX = l_v1000_9, 
                        On = false
                    });
                    shared.sfx({
                        SoundId = "rbxassetid://93713848313467", 
                        Parent = v7397:FindFirstChild("LowerJaw", true), 
                        RollOffMaxDistance = 1000, 
                        Volume = 6
                    }):Play();
                    task.delay(0.65, function() --[[ Line: 17920 ]]
                        -- upvalues: v7395 (ref), v7397 (ref)
                        if not v7395 then
                            return;
                        else
                            shared.sfx({
                                SoundId = "rbxassetid://135271837037426", 
                                Parent = v7397:FindFirstChild("LowerJaw", true), 
                                RollOffMaxDistance = 1000, 
                                Volume = v7395 and 9 or 6
                            }):Play();
                            return;
                        end;
                    end);
                    l_wait_19(0.3);
                    if v7395 then
                        local l_sfx_5 = shared.sfx;
                        v7568 = {
                            SoundId = "rbxassetid://101194743805933"
                        };
                        v7569 = not v7395;
                        if v7569 then
                            local _, v7573, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            v7569 = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7573, 0) * CFrame.new(-33, 75, -150);
                        end;
                        v7568.CFrame = v7569;
                        v7568.RollOffMaxDistance = 1000;
                        v7568.TimePosition = 1.1;
                        v7568.Volume = 3;
                        v7568.Parent = v7395 and workspace;
                        l_sfx_5(v7568):Resume();
                        l_sfx_5 = v1000;
                        v7568 = {
                            FX = v7297.Kanji, 
                            Maid = v7298._maid
                        };
                        local _, v7576, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        v7568.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7576, 0) * CFrame.new(-33, 75, -150);
                        l_sfx_5 = l_sfx_5(v7568);
                        l_sfx_5:ScaleTo(11.5);
                        v1014(l_sfx_5);
                    end;
                    l_wait_19(0.3);
                end);
                local _ = function(_) --[[ Line: 17949 ]] --[[ Name: Impact ]]

                end;
                local v7580 = {};
                local function v7586(v7581) --[[ Line: 17959 ]]
                    -- upvalues: v7395 (copy), l_LastBreath_0 (copy), v7580 (copy), v7294 (copy), l_LocalPlayer_0 (ref)
                    if not v7395 then
                        return;
                    else
                        local l_l_LastBreath_0_FirstChild_0 = l_LastBreath_0:FindFirstChild(v7581);
                        if not l_l_LastBreath_0_FirstChild_0 then
                            return;
                        else
                            v7580[v7581] = {};
                            for v7583, v7584 in pairs(l_l_LastBreath_0_FirstChild_0.Frames:GetChildren()) do
                                local l_ImageLabel_4 = Instance.new("ImageLabel");
                                table.insert(v7294, l_ImageLabel_4);
                                l_ImageLabel_4.Image = v7584.Image;
                                l_ImageLabel_4.BackgroundColor3 = Color3.new(1, 1, 1);
                                l_ImageLabel_4.Size = UDim2.new(0, 1, 0, 1);
                                l_ImageLabel_4.ZIndex = v7583;
                                l_ImageLabel_4.Parent = l_LocalPlayer_0.PlayerGui.MobileJunk;
                                v7580[v7581][tonumber(v7584.Name)] = l_ImageLabel_4;
                            end;
                            return;
                        end;
                    end;
                end;
                local _ = function(v7587, v7588, v7589) --[[ Line: 17975 ]]
                    -- upvalues: v7580 (copy), v7395 (copy), v12 (ref)
                    local v7590 = v7580[v7587];
                    if not v7395 then
                        return;
                    elseif not v7590 then
                        return;
                    else
                        local v7591 = nil;
                        local v7592 = 1;
                        local _ = tick();
                        local v7594 = nil;
                        v7594 = shared.loop(function() --[[ Line: 17983 ]]
                            -- upvalues: v7590 (copy), v7592 (ref), v7589 (copy), v12 (ref), v7594 (ref), v7591 (ref)
                            local v7595 = v7590[v7592];
                            if not v7595 then
                                if v7589 then
                                    v12({
                                        Effect = "Camshake", 
                                        Intensity = 20, 
                                        Last = 1.25
                                    });
                                end;
                                for _, v7597 in pairs(v7590) do
                                    v7597:Destroy();
                                end;
                                return v7594();
                            else
                                v7592 = v7592 + 1;
                                v7595.Size = UDim2.new(1, 0, 1, 0);
                                if v7591 then
                                    v7591:Destroy();
                                end;
                                v7591 = v7595;
                                return;
                            end;
                        end, v7588 or 24);
                        return;
                    end;
                end;
                v7586("Impact");
                v7586("Impact22");
                v7586("Impact3");
                task.delay(5.517, function() --[[ Line: 18011 ]]
                    -- upvalues: v7580 (copy), v7395 (copy), v12 (ref), l_QuickWeldNew_6 (copy), v7297 (copy), l_char_26 (copy), v7298 (copy), l_TweenService_26 (copy), v989 (ref), v1021 (ref), v1014 (ref), v1000 (ref), l_CurrentCamera_8 (copy), l_wait_19 (copy), l_CurrentCamera_8 (copy), v7303 (ref), l_Thrown_23 (copy), v7302 (copy), v7309 (copy)
                    task.delay(0.7, function() --[[ Line: 18012 ]]
                        -- upvalues: v7580 (ref), v7395 (ref), v12 (ref)
                        local l_Impact_0 = v7580.Impact;
                        if not v7395 then
                            return;
                        elseif not l_Impact_0 then
                            return;
                        else
                            local v7600 = nil;
                            local v7601 = 1;
                            local _ = tick();
                            local v7603 = nil;
                            local l_loop_0 = shared.loop;
                            local v7605 = nil;
                            v7603 = l_loop_0(function() --[[ Line: 17983 ]]
                                -- upvalues: l_Impact_0 (copy), v7601 (ref), v7605 (copy), v12 (ref), v7603 (ref), v7600 (ref)
                                local v7606 = l_Impact_0[v7601];
                                if not v7606 then
                                    if v7605 then
                                        v12({
                                            Effect = "Camshake", 
                                            Intensity = 20, 
                                            Last = 1.25
                                        });
                                    end;
                                    for _, v7608 in pairs(l_Impact_0) do
                                        v7608:Destroy();
                                    end;
                                    return v7603();
                                else
                                    v7601 = v7601 + 1;
                                    v7606.Size = UDim2.new(1, 0, 1, 0);
                                    if v7600 then
                                        v7600:Destroy();
                                    end;
                                    v7600 = v7606;
                                    return;
                                end;
                            end, 24);
                            return;
                        end;
                    end);
                    local v7609 = l_QuickWeldNew_6({
                        FX = v7297.Hit, 
                        P = l_char_26["Right Leg"], 
                        Maid = v7298._maid, 
                        C0 = CFrame.new(0, 1.5, 0)
                    });
                    local v7610 = v7298._maid:give(Instance.new("NumberValue"));
                    v7298._maid:giveTask(v7610.Changed:Connect(function() --[[ Line: 18023 ]]
                        -- upvalues: v7609 (copy), v7610 (copy)
                        v7609:ScaleTo(v7610.Value);
                    end));
                    v7609:ScaleTo(0.1);
                    v7610.Value = v7609:GetScale();
                    l_TweenService_26:Create(v7610, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        Value = 1.8
                    }):Play();
                    v989({
                        FX = v7609, 
                        Scale = 1
                    });
                    v1021({
                        FX = v7609, 
                        On = true
                    });
                    v1014(v7609);
                    if v7395 then
                        local v7611 = v1000({
                            FX = v7297.BlackIn, 
                            Maid = v7298._maid, 
                            Anchor = CFrame.new(0, 0, 0)
                        });
                        v1014(v7611);
                        task.spawn(function() --[[ Line: 18037 ]]
                            -- upvalues: v7611 (copy), l_CurrentCamera_8 (ref), l_wait_19 (ref)
                            local v7612 = tick();
                            while tick() - v7612 < 0.8 do
                                v7611:PivotTo(l_CurrentCamera_8.CFrame * CFrame.new(0, 0, -4));
                                l_wait_19(0.01);
                            end;
                        end);
                    end;
                    task.spawn(function() --[[ Line: 18048 ]]
                        -- upvalues: l_wait_19 (ref), v7297 (ref), l_char_26 (ref)
                        l_wait_19(0.30000000000000004);
                        local v7613 = tick();
                        while tick() - v7613 < 0.7 do
                            local v7614 = v7297.Spin:Clone();
                            v7614:ScaleTo(1);
                            local v7615 = {
                                Model = v7614
                            };
                            local _, v7617, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            v7615.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7617, 0) * CFrame.new(0, 2, 0) * CFrame.Angles(0, 0, 1.5707963267948966);
                            v7615.Info = TweenInfo.new(1, Enum.EasingStyle.Exponential);
                            task.spawn(function() --[[ Line: 4475 ]]
                                -- upvalues: v7615 (copy)
                                local l_Model_152 = v7615.Model;
                                local v7620 = v7615.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                                local l_Start_158 = l_Model_152:FindFirstChild("Start");
                                local l_End_156 = l_Model_152:FindFirstChild("End");
                                local l_Stay_151 = v7615.Stay;
                                local l_Anchor_152 = v7615.Anchor;
                                local v7625 = v7615.EndT or 1;
                                local l_Del_151 = v7615.Del;
                                local l_Skip_151 = v7615.Skip;
                                if l_Start_158 and l_End_156 then
                                    l_Model_152.PrimaryPart = l_Start_158;
                                    if not l_Skip_151 then
                                        for _, v7629 in pairs(l_Model_152:GetChildren()) do
                                            if v7629:IsA("BasePart") then
                                                v7629.CanCollide = false;
                                                v7629.Anchored = true;
                                            end;
                                        end;
                                    end;
                                    if l_Anchor_152 then
                                        l_Model_152:SetPrimaryPartCFrame(l_Anchor_152);
                                    end;
                                    if v7615.T then
                                        l_Start_158.Transparency = v7615.T;
                                    end;
                                    l_End_156.Transparency = 1;
                                    l_Model_152.Parent = workspace.Thrown;
                                    local l_Decal_304 = l_Start_158:FindFirstChildOfClass("Decal");
                                    local l_SpecialMesh_304 = l_Start_158:FindFirstChildOfClass("SpecialMesh");
                                    local l_SpecialMesh_305 = l_End_156:FindFirstChildOfClass("SpecialMesh");
                                    local l_Decal_305 = l_End_156:FindFirstChildOfClass("Decal");
                                    if l_Decal_305 and not l_Skip_151 then
                                        l_Decal_305.Transparency = 1;
                                    end;
                                    local v7634 = nil;
                                    if l_Del_151 then
                                        game:GetService("TweenService"):Create(l_Start_158, v7620, {
                                            Size = l_End_156.Size, 
                                            CFrame = l_End_156.CFrame
                                        }):Play();
                                        task.delay(l_Del_151, function() --[[ Line: 4519 ]]
                                            -- upvalues: v7634 (ref), l_Start_158 (copy), v7620 (copy), v7625 (copy), l_Decal_304 (copy), l_SpecialMesh_304 (copy), l_SpecialMesh_305 (copy)
                                            v7634 = game:GetService("TweenService"):Create(l_Start_158, v7620, {
                                                Transparency = v7625
                                            });
                                            v7634:Play();
                                            if l_Decal_304 then
                                                for _, v7636 in pairs(l_Start_158:GetChildren()) do
                                                    if v7636:IsA("Decal") then
                                                        game:GetService("TweenService"):Create(v7636, v7620, {
                                                            Transparency = v7625
                                                        }):Play();
                                                    end;
                                                end;
                                            end;
                                            if l_SpecialMesh_304 then
                                                v7634 = game:GetService("TweenService"):Create(l_SpecialMesh_304, v7620, {
                                                    Scale = l_SpecialMesh_305.Scale
                                                }):Play();
                                            end;
                                        end);
                                    else
                                        if l_SpecialMesh_304 then
                                            game:GetService("TweenService"):Create(l_SpecialMesh_304, v7620, {
                                                Scale = l_SpecialMesh_305.Scale
                                            }):Play();
                                        end;
                                        if l_Decal_304 then
                                            for _, v7638 in pairs(l_Start_158:GetChildren()) do
                                                if v7638:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7638, v7620, {
                                                        Transparency = v7625
                                                    }):Play();
                                                end;
                                            end;
                                            v7634 = game:GetService("TweenService"):Create(l_Start_158, v7620, {
                                                Size = l_End_156.Size, 
                                                CFrame = l_End_156.CFrame
                                            });
                                            v7634:Play();
                                        else
                                            v7634 = game:GetService("TweenService"):Create(l_Start_158, v7620, {
                                                Size = l_End_156.Size, 
                                                Transparency = v7625, 
                                                CFrame = l_End_156.CFrame
                                            });
                                            v7634:Play();
                                        end;
                                    end;
                                    if not l_Stay_151 then
                                        if l_Del_151 then
                                            task.wait(l_Del_151 + 0.1);
                                        end;
                                        v7634.Completed:Connect(function() --[[ Line: 4562 ]]
                                            -- upvalues: l_Model_152 (copy)
                                            l_Model_152:Destroy();
                                        end);
                                    end;
                                    return;
                                else
                                    warn("NO START OR END");
                                    return;
                                end;
                            end);
                            l_wait_19(0.15000000000000002);
                        end;
                    end);
                    task.spawn(function() --[[ Line: 18061 ]]
                        -- upvalues: l_wait_19 (ref), v7297 (ref), l_char_26 (ref)
                        l_wait_19(0.75);
                        local v7639 = tick();
                        while tick() - v7639 < 0.4 do
                            local v7640 = v7297.Hmm3:Clone();
                            v7640:ScaleTo(1);
                            local v7641 = {
                                Model = v7640, 
                                T = 0.2
                            };
                            local _, v7643, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            v7641.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7643, 0);
                            v7641.Info = TweenInfo.new(1, Enum.EasingStyle.Exponential);
                            task.spawn(function() --[[ Line: 4475 ]]
                                -- upvalues: v7641 (copy)
                                local l_Model_153 = v7641.Model;
                                local v7646 = v7641.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                                local l_Start_159 = l_Model_153:FindFirstChild("Start");
                                local l_End_157 = l_Model_153:FindFirstChild("End");
                                local l_Stay_152 = v7641.Stay;
                                local l_Anchor_153 = v7641.Anchor;
                                local v7651 = v7641.EndT or 1;
                                local l_Del_152 = v7641.Del;
                                local l_Skip_152 = v7641.Skip;
                                if l_Start_159 and l_End_157 then
                                    l_Model_153.PrimaryPart = l_Start_159;
                                    if not l_Skip_152 then
                                        for _, v7655 in pairs(l_Model_153:GetChildren()) do
                                            if v7655:IsA("BasePart") then
                                                v7655.CanCollide = false;
                                                v7655.Anchored = true;
                                            end;
                                        end;
                                    end;
                                    if l_Anchor_153 then
                                        l_Model_153:SetPrimaryPartCFrame(l_Anchor_153);
                                    end;
                                    if v7641.T then
                                        l_Start_159.Transparency = v7641.T;
                                    end;
                                    l_End_157.Transparency = 1;
                                    l_Model_153.Parent = workspace.Thrown;
                                    local l_Decal_306 = l_Start_159:FindFirstChildOfClass("Decal");
                                    local l_SpecialMesh_306 = l_Start_159:FindFirstChildOfClass("SpecialMesh");
                                    local l_SpecialMesh_307 = l_End_157:FindFirstChildOfClass("SpecialMesh");
                                    local l_Decal_307 = l_End_157:FindFirstChildOfClass("Decal");
                                    if l_Decal_307 and not l_Skip_152 then
                                        l_Decal_307.Transparency = 1;
                                    end;
                                    local v7660 = nil;
                                    if l_Del_152 then
                                        game:GetService("TweenService"):Create(l_Start_159, v7646, {
                                            Size = l_End_157.Size, 
                                            CFrame = l_End_157.CFrame
                                        }):Play();
                                        task.delay(l_Del_152, function() --[[ Line: 4519 ]]
                                            -- upvalues: v7660 (ref), l_Start_159 (copy), v7646 (copy), v7651 (copy), l_Decal_306 (copy), l_SpecialMesh_306 (copy), l_SpecialMesh_307 (copy)
                                            v7660 = game:GetService("TweenService"):Create(l_Start_159, v7646, {
                                                Transparency = v7651
                                            });
                                            v7660:Play();
                                            if l_Decal_306 then
                                                for _, v7662 in pairs(l_Start_159:GetChildren()) do
                                                    if v7662:IsA("Decal") then
                                                        game:GetService("TweenService"):Create(v7662, v7646, {
                                                            Transparency = v7651
                                                        }):Play();
                                                    end;
                                                end;
                                            end;
                                            if l_SpecialMesh_306 then
                                                v7660 = game:GetService("TweenService"):Create(l_SpecialMesh_306, v7646, {
                                                    Scale = l_SpecialMesh_307.Scale
                                                }):Play();
                                            end;
                                        end);
                                    else
                                        if l_SpecialMesh_306 then
                                            game:GetService("TweenService"):Create(l_SpecialMesh_306, v7646, {
                                                Scale = l_SpecialMesh_307.Scale
                                            }):Play();
                                        end;
                                        if l_Decal_306 then
                                            for _, v7664 in pairs(l_Start_159:GetChildren()) do
                                                if v7664:IsA("Decal") then
                                                    game:GetService("TweenService"):Create(v7664, v7646, {
                                                        Transparency = v7651
                                                    }):Play();
                                                end;
                                            end;
                                            v7660 = game:GetService("TweenService"):Create(l_Start_159, v7646, {
                                                Size = l_End_157.Size, 
                                                CFrame = l_End_157.CFrame
                                            });
                                            v7660:Play();
                                        else
                                            v7660 = game:GetService("TweenService"):Create(l_Start_159, v7646, {
                                                Size = l_End_157.Size, 
                                                Transparency = v7651, 
                                                CFrame = l_End_157.CFrame
                                            });
                                            v7660:Play();
                                        end;
                                    end;
                                    if not l_Stay_152 then
                                        if l_Del_152 then
                                            task.wait(l_Del_152 + 0.1);
                                        end;
                                        v7660.Completed:Connect(function() --[[ Line: 4562 ]]
                                            -- upvalues: l_Model_153 (copy)
                                            l_Model_153:Destroy();
                                        end);
                                    end;
                                    return;
                                else
                                    warn("NO START OR END");
                                    return;
                                end;
                            end);
                            l_wait_19(0.1);
                        end;
                    end);
                    l_wait_19(1.0499999999999998);
                    (function() --[[ Line: 18076 ]] --[[ Name: New ]]
                        -- upvalues: l_TweenService_26 (ref), v7610 (copy), l_QuickWeldNew_6 (ref), v7297 (ref), l_char_26 (ref), v7298 (ref), v989 (ref), v1021 (ref), v7395 (ref), l_CurrentCamera_8 (ref), v7303 (ref), l_Thrown_23 (ref), v7302 (ref), v7309 (ref), l_wait_19 (ref), v7609 (copy)
                        l_TweenService_26:Create(v7610, TweenInfo.new(0.2, Enum.EasingStyle.Sine), {
                            Value = 0.5
                        }):Play();
                        local v7665 = l_QuickWeldNew_6({
                            FX = v7297.Pls, 
                            P = l_char_26["Right Leg"], 
                            Maid = v7298._maid, 
                            C0 = CFrame.new(0, -4, 0)
                        });
                        v7665:ScaleTo(0.05);
                        v989({
                            FX = v7665, 
                            Scale = 0.15
                        });
                        v1021({
                            FX = v7665, 
                            On = true
                        });
                        local v7666 = v7298._maid:give(v7297.Beam:Clone());
                        task.spawn(function() --[[ Line: 18090 ]]
                            -- upvalues: v7395 (ref), v7666 (copy), l_CurrentCamera_8 (ref), v7303 (ref), v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7309 (ref), l_wait_19 (ref), l_TweenService_26 (ref), v1021 (ref), v7609 (ref), v7665 (copy)
                            local v7667 = tick();
                            while tick() - v7667 < 1 do
                                if v7395 then
                                    v7666:PivotTo(l_CurrentCamera_8.CFrame * CFrame.new(0, 0, -30) * CFrame.Angles(3.141592653589793, 0, 0.7853981633974483) * CFrame.new(0, -40, 0));
                                end;
                                v7303 = v7303 + 1;
                                if v7303 % 2 == 0 then
                                    task.spawn(function() --[[ Line: 18101 ]]
                                        -- upvalues: v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7309 (ref), l_wait_19 (ref), l_TweenService_26 (ref)
                                        local v7668 = v7297.Impact2:Clone();
                                        local l_Decal_308 = v7668:FindFirstChild("Decal");
                                        v7668:PivotTo(l_char_26["Right Leg"].CFrame * CFrame.new(0, 2, 0) * CFrame.Angles(3.141592653589793, math.rad((math.random(0, 360))), 0));
                                        v7668.Parent = l_Thrown_23;
                                        game.Debris:AddItem(v7668, 10);
                                        local _ = v7302:NextNumber(3, 6);
                                        local _ = v7302:NextNumber(0.0075, 0.015);
                                        local v7672 = nil;
                                        v7672 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                                        local l_v7309_0 = v7309;
                                        task.spawn(function() --[[ Line: 18122 ]]
                                            -- upvalues: l_v7309_0 (copy), v7668 (copy), l_wait_19 (ref)
                                            for v7674 = 1, #l_v7309_0, 2 do
                                                local v7675 = l_v7309_0[v7674];
                                                if v7675 then
                                                    v7668.Decal.Texture = v7675;
                                                end;
                                                l_wait_19(0.01);
                                            end;
                                            v7668:Destroy();
                                        end);
                                        local l_Mesh_2 = v7668.Mesh;
                                        l_Mesh_2.Scale = l_Mesh_2.Scale * 0.1;
                                        v7668.Mesh.Scale = Vector3.new(v7668.Mesh.Scale.X, v7668.Mesh.Scale.Y * 0.5, v7668.Mesh.Scale.Z);
                                        l_TweenService_26:Create(v7668, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                                            CFrame = v7668.CFrame * CFrame.new(0, 20, 0)
                                        }):Play();
                                        l_TweenService_26:Create(v7668.Mesh, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                                            Scale = Vector3.new(v7668.Mesh.Scale.X * 0.15, v7668.Mesh.Scale.Y * 1.5, v7668.Mesh.Scale.Z * 0.15)
                                        }):Play();
                                        task.wait(0.1);
                                        l_TweenService_26:Create(l_Decal_308, TweenInfo.new(0.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {
                                            Color3 = Color3.fromRGB(0, 0, 0)
                                        }):Play();
                                    end);
                                end;
                                l_wait_19(0.01);
                            end;
                            v1021({
                                FX = v7609, 
                                On = false
                            });
                            v1021({
                                FX = v7665, 
                                On = false
                            });
                            v7666:Destroy();
                        end);
                        v7666.Parent = l_Thrown_23;
                        game.Debris:AddItem(v7666, 2);
                    end)();
                end);
                task.delay(7.483999999999999, function() --[[ Line: 18159 ]]
                    -- upvalues: l_LastBreath_0 (copy), v7298 (copy), l_char_26 (copy), v1021 (ref), v7395 (copy), l_PrimaryPart_49 (copy), l_TweenService_26 (copy), v1000 (ref), v7297 (copy), l_wait_19 (copy), l_QuickWeldNew_6 (copy), v989 (ref), l_hit_18 (copy), v983 (ref), v955 (ref), v7580 (copy), v12 (ref), v7397 (copy), l_Thrown_23 (copy), v7302 (copy), v7308 (copy), v1014 (ref)
                    local function v7714() --[[ Line: 18166 ]] --[[ Name: Old ]]
                        -- upvalues: l_LastBreath_0 (ref), v7298 (ref), l_char_26 (ref), v1021 (ref), v7395 (ref), l_PrimaryPart_49 (ref), l_TweenService_26 (ref), v1000 (ref), v7297 (ref), l_wait_19 (ref), l_QuickWeldNew_6 (ref), v989 (ref), l_hit_18 (ref), v983 (ref), v955 (ref)
                        (function() --[[ Line: 18168 ]] --[[ Name: Eyes ]]
                            -- upvalues: l_LastBreath_0 (ref), v7298 (ref), l_char_26 (ref), v1021 (ref)
                            for _, v7678 in pairs(l_LastBreath_0.Eyes:GetChildren()) do
                                local v7679 = v7298._maid:give(v7678:Clone());
                                v7679.Parent = l_char_26.Head;
                                task.delay(1.5, function() --[[ Line: 18173 ]]
                                    -- upvalues: v1021 (ref), v7679 (copy)
                                    v1021({
                                        FX = v7679, 
                                        On = false
                                    });
                                end);
                            end;
                        end)();
                        local _ = function(v7680) --[[ Line: 18182 ]] --[[ Name: Darkness ]]
                            -- upvalues: v7395 (ref), v7298 (ref), l_PrimaryPart_49 (ref), l_TweenService_26 (ref)
                            task.spawn(function() --[[ Line: 18183 ]]
                                -- upvalues: v7395 (ref), v7680 (copy), v7298 (ref), l_PrimaryPart_49 (ref), l_TweenService_26 (ref)
                                if not v7395 then
                                    return;
                                elseif v7680 then
                                    local v7681 = v7298._maid:give(Instance.new("PointLight"));
                                    v7681.Range = 0;
                                    v7681.Parent = l_PrimaryPart_49;
                                    l_TweenService_26:Create(game.Lighting, TweenInfo.new(1.827, Enum.EasingStyle.Quad), {
                                        Brightness = 0, 
                                        Ambient = Color3.fromRGB(0, 0, 0), 
                                        OutdoorAmbient = Color3.fromRGB(25, 25, 25)
                                    }):Play();
                                    l_TweenService_26:Create(game.Lighting, TweenInfo.new(0.65, Enum.EasingStyle.Quad), {
                                        ClockTime = 0
                                    }):Play();
                                    l_TweenService_26:Create(v7681, TweenInfo.new(0.65, Enum.EasingStyle.Quad), {
                                        Range = 6, 
                                        Brightness = 1
                                    }):Play();
                                    return;
                                else
                                    l_TweenService_26:Create(game.Lighting, TweenInfo.new(1.827, Enum.EasingStyle.Quad), {
                                        Brightness = 2, 
                                        Ambient = Color3.fromRGB(138, 138, 138), 
                                        OutdoorAmbient = Color3.fromRGB(70, 70, 70)
                                    }):Play();
                                    l_TweenService_26:Create(game.Lighting, TweenInfo.new(0.65, Enum.EasingStyle.Quad), {
                                        ClockTime = 13
                                    }):Play();
                                    for _, v7683 in pairs(l_PrimaryPart_49:GetChildren()) do
                                        if v7683:IsA("PointLight") then
                                            v7683:Destroy();
                                        end;
                                    end;
                                    return;
                                end;
                            end);
                        end;
                        local l_v1000_10 = v1000;
                        local v7686 = {
                            FX = v7297.GroundSmoke, 
                            Maid = v7298._maid
                        };
                        local v7687, v7688, v7689 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        v7686.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7688, 0) * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 0);
                        l_v1000_10 = l_v1000_10(v7686);
                        l_v1000_10:ScaleTo(2);
                        task.delay(0.3, function() --[[ Line: 18213 ]]
                            -- upvalues: l_v1000_10 (copy), l_TweenService_26 (ref), l_wait_19 (ref)
                            for _, v7691 in pairs(l_v1000_10:GetDescendants()) do
                                if v7691:IsA("ParticleEmitter") then
                                    l_TweenService_26:Create(v7691, TweenInfo.new(0.2), {
                                        TimeScale = 0
                                    }):Play();
                                end;
                            end;
                            l_wait_19(1);
                            for _, v7693 in pairs(l_v1000_10:GetDescendants()) do
                                if v7693:IsA("ParticleEmitter") then
                                    l_TweenService_26:Create(v7693, TweenInfo.new(0.2), {
                                        TimeScale = 1
                                    }):Play();
                                end;
                            end;
                        end);
                        l_wait_19(0.1);
                        v7686 = v1000({
                            FX = v7297.Velocity, 
                            Maid = v7298._maid, 
                            Anchor = l_PrimaryPart_49.CFrame * CFrame.new(3.5393543243408203, -4.655467987060547, -113.11857604980469) * CFrame.Angles(0.9166566729545593, -4.454574716868619E-20, -3.1415927410125732)
                        });
                        v7686:ScaleTo(0.6);
                        for _, v7695 in pairs(v7686:GetDescendants()) do
                            if v7695:IsA("Beam") then
                                v7695.Enabled = true;
                                v7695.TextureSpeed = v7695.TextureSpeed * 2.5;
                                l_TweenService_26:Create(v7695, TweenInfo.new(16.3, Enum.EasingStyle.Sine), {
                                    TextureSpeed = 0
                                }):Play();
                            elseif v7695.Name == "A1" then
                                local l_A0_0 = v7695.Parent.A0;
                                local v7697 = v7695.CFrame * CFrame.new(-2, 0, 0);
                                v7695.CFrame = l_A0_0.CFrame;
                                l_TweenService_26:Create(v7695, TweenInfo.new(0.1, Enum.EasingStyle.Sine), {
                                    CFrame = v7697
                                }):Play();
                            end;
                        end;
                        local l_v1000_11 = v1000;
                        local v7699 = {
                            FX = v7297.SpeedBig
                        };
                        local _, v7701, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        v7699.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7701, 0) * CFrame.new(0, 0, 4) * CFrame.Angles(-0.7853981633974483, 0, 0);
                        v7699.Maid = v7298._maid;
                        l_v1000_11 = l_v1000_11(v7699);
                        l_v1000_11:ScaleTo(2);
                        v1021({
                            FX = l_v1000_11, 
                            On = false
                        });
                        v7699 = l_QuickWeldNew_6({
                            FX = v7297.Pls, 
                            P = l_char_26["Right Leg"], 
                            Maid = v7298._maid, 
                            C0 = CFrame.new(0, -4, 0)
                        });
                        v989({
                            FX = v7699, 
                            Scale = 0.4
                        });
                        v1021({
                            FX = v7699, 
                            On = false
                        });
                        v7687 = l_QuickWeldNew_6({
                            FX = v7297.Hit, 
                            P = l_hit_18.Head, 
                            Maid = v7298._maid, 
                            C0 = CFrame.new(0, 0, 0)
                        });
                        v7687:ScaleTo(1.5);
                        v983({
                            FX = v7687, 
                            Count = -1
                        });
                        v989({
                            FX = v7687, 
                            Scale = 1
                        });
                        v1021({
                            FX = v7687, 
                            On = false
                        });
                        for _, v7704 in pairs(v7687:GetDescendants()) do
                            if v7704:IsA("ParticleEmitter") then
                                l_TweenService_26:Create(v7704, TweenInfo.new(16.3, Enum.EasingStyle.Sine), {
                                    TimeScale = 0
                                }):Play();
                            end;
                        end;
                        v7688 = v1000;
                        v7689 = {
                            FX = v7297.BeamStrike, 
                            Maid = v7298._maid
                        };
                        local _, v7706, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        v7689.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7706, 0) * CFrame.new(0, 0, -10) * CFrame.Angles(-0, -1.5707963705062866, -0.927561342716217);
                        v7688 = v7688(v7689);
                        for _, v7709 in pairs(v7688:GetDescendants()) do
                            if v7709:IsA("Beam") then
                                v7709.Enabled = true;
                                v7709.TextureSpeed = 11;
                                l_TweenService_26:Create(v7709, TweenInfo.new(16.3, Enum.EasingStyle.Sine), {
                                    TextureSpeed = 0
                                }):Play();
                                v7709.Width1 = v7709.Width1 * 0.1;
                                v7709.ZOffset = v7709.ZOffset - 4;
                            end;
                        end;
                        task.delay(1, function() --[[ Line: 18287 ]]
                            -- upvalues: v7686 (copy), v955 (ref), v7688 (copy), l_wait_19 (ref), v1021 (ref), l_v1000_11 (copy), v7699 (copy), v7687 (copy)
                            for _, v7711 in pairs(v7686:GetDescendants()) do
                                if v7711:IsA("Beam") then
                                    v955(v7711, {
                                        Time = 0.35, 
                                        EasingStyle = "Sine", 
                                        Goal = {
                                            Transparency = NumberSequence.new({
                                                NumberSequenceKeypoint.new(0, 1), 
                                                NumberSequenceKeypoint.new(1, 1)
                                            })
                                        }
                                    });
                                    game.Debris:AddItem(v7711, 0.35);
                                end;
                            end;
                            for _, v7713 in pairs(v7688:GetDescendants()) do
                                if v7713:IsA("Beam") then
                                    v955(v7713, {
                                        Time = 0.35, 
                                        EasingStyle = "Sine", 
                                        Goal = {
                                            Transparency = NumberSequence.new({
                                                NumberSequenceKeypoint.new(0, 1), 
                                                NumberSequenceKeypoint.new(1, 1)
                                            })
                                        }
                                    });
                                    game.Debris:AddItem(v7713, 0.35);
                                end;
                            end;
                            l_wait_19(0.3);
                            v1021({
                                FX = l_v1000_11, 
                                On = false
                            });
                            l_wait_19(0.5);
                            v1021({
                                FX = v7699, 
                                On = false
                            });
                            v1021({
                                FX = v7687, 
                                On = false
                            });
                            v7687:Destroy();
                        end);
                        game.Debris:AddItem(v7686, 3);
                    end;
                    task.spawn(function() --[[ Line: 18313 ]]
                        -- upvalues: v7580 (ref), v7395 (ref), v12 (ref), v7397 (ref)
                        local l_Impact22_0 = v7580.Impact22;
                        if v7395 and l_Impact22_0 then
                            local v7716 = nil;
                            local v7717 = 1;
                            local _ = tick();
                            local v7719 = nil;
                            local l_loop_1 = shared.loop;
                            local v7721 = nil;
                            do
                                local l_v7716_0, l_v7717_0, l_v7719_0 = v7716, v7717, v7719;
                                l_v7719_0 = l_loop_1(function() --[[ Line: 17983 ]]
                                    -- upvalues: l_Impact22_0 (copy), l_v7717_0 (ref), v7721 (copy), v12 (ref), l_v7719_0 (ref), l_v7716_0 (ref)
                                    local v7725 = l_Impact22_0[l_v7717_0];
                                    if not v7725 then
                                        if v7721 then
                                            v12({
                                                Effect = "Camshake", 
                                                Intensity = 20, 
                                                Last = 1.25
                                            });
                                        end;
                                        for _, v7727 in pairs(l_Impact22_0) do
                                            v7727:Destroy();
                                        end;
                                        return l_v7719_0();
                                    else
                                        l_v7717_0 = l_v7717_0 + 1;
                                        v7725.Size = UDim2.new(1, 0, 1, 0);
                                        if l_v7716_0 then
                                            l_v7716_0:Destroy();
                                        end;
                                        l_v7716_0 = v7725;
                                        return;
                                    end;
                                end, 28);
                            end;
                        end;
                        v7397:Destroy();
                    end);
                    l_wait_19(0.1);
                    local v7728 = l_QuickWeldNew_6({
                        FX = v7297["Right Leg"], 
                        P = l_char_26["Right Leg"], 
                        Maid = v7298._maid, 
                        C0 = CFrame.new(0, 0, 0)
                    });
                    v7728:ScaleTo(1.7);
                    v1021({
                        FX = v7728, 
                        On = true
                    });
                    local v7729 = v7297.Last3:Clone();
                    v7729:ScaleTo(1);
                    local v7730 = {
                        Model = v7729, 
                        EndT = 1
                    };
                    local v7731, v7732, v7733 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7730.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7732, 0) * CFrame.Angles(-0.7853981633974483, 0, 0) * CFrame.new(0, 3, 0);
                    v7730.Info = TweenInfo.new(1, Enum.EasingStyle.Exponential);
                    local l_v7730_0 = v7730 --[[ copy: 3 -> 10 ]];
                    task.spawn(function() --[[ Line: 4475 ]]
                        -- upvalues: l_v7730_0 (copy)
                        local l_Model_154 = l_v7730_0.Model;
                        local v7736 = l_v7730_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                        local l_Start_160 = l_Model_154:FindFirstChild("Start");
                        local l_End_158 = l_Model_154:FindFirstChild("End");
                        local l_Stay_153 = l_v7730_0.Stay;
                        local l_Anchor_154 = l_v7730_0.Anchor;
                        local v7741 = l_v7730_0.EndT or 1;
                        local l_Del_153 = l_v7730_0.Del;
                        local l_Skip_153 = l_v7730_0.Skip;
                        if l_Start_160 and l_End_158 then
                            l_Model_154.PrimaryPart = l_Start_160;
                            if not l_Skip_153 then
                                for _, v7745 in pairs(l_Model_154:GetChildren()) do
                                    if v7745:IsA("BasePart") then
                                        v7745.CanCollide = false;
                                        v7745.Anchored = true;
                                    end;
                                end;
                            end;
                            if l_Anchor_154 then
                                l_Model_154:SetPrimaryPartCFrame(l_Anchor_154);
                            end;
                            if l_v7730_0.T then
                                l_Start_160.Transparency = l_v7730_0.T;
                            end;
                            l_End_158.Transparency = 1;
                            l_Model_154.Parent = workspace.Thrown;
                            local l_Decal_309 = l_Start_160:FindFirstChildOfClass("Decal");
                            local l_SpecialMesh_308 = l_Start_160:FindFirstChildOfClass("SpecialMesh");
                            local l_SpecialMesh_309 = l_End_158:FindFirstChildOfClass("SpecialMesh");
                            local l_Decal_310 = l_End_158:FindFirstChildOfClass("Decal");
                            if l_Decal_310 and not l_Skip_153 then
                                l_Decal_310.Transparency = 1;
                            end;
                            local v7750 = nil;
                            if l_Del_153 then
                                game:GetService("TweenService"):Create(l_Start_160, v7736, {
                                    Size = l_End_158.Size, 
                                    CFrame = l_End_158.CFrame
                                }):Play();
                                task.delay(l_Del_153, function() --[[ Line: 4519 ]]
                                    -- upvalues: v7750 (ref), l_Start_160 (copy), v7736 (copy), v7741 (copy), l_Decal_309 (copy), l_SpecialMesh_308 (copy), l_SpecialMesh_309 (copy)
                                    v7750 = game:GetService("TweenService"):Create(l_Start_160, v7736, {
                                        Transparency = v7741
                                    });
                                    v7750:Play();
                                    if l_Decal_309 then
                                        for _, v7752 in pairs(l_Start_160:GetChildren()) do
                                            if v7752:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v7752, v7736, {
                                                    Transparency = v7741
                                                }):Play();
                                            end;
                                        end;
                                    end;
                                    if l_SpecialMesh_308 then
                                        v7750 = game:GetService("TweenService"):Create(l_SpecialMesh_308, v7736, {
                                            Scale = l_SpecialMesh_309.Scale
                                        }):Play();
                                    end;
                                end);
                            else
                                if l_SpecialMesh_308 then
                                    game:GetService("TweenService"):Create(l_SpecialMesh_308, v7736, {
                                        Scale = l_SpecialMesh_309.Scale
                                    }):Play();
                                end;
                                if l_Decal_309 then
                                    for _, v7754 in pairs(l_Start_160:GetChildren()) do
                                        if v7754:IsA("Decal") then
                                            game:GetService("TweenService"):Create(v7754, v7736, {
                                                Transparency = v7741
                                            }):Play();
                                        end;
                                    end;
                                    v7750 = game:GetService("TweenService"):Create(l_Start_160, v7736, {
                                        Size = l_End_158.Size, 
                                        CFrame = l_End_158.CFrame
                                    });
                                    v7750:Play();
                                else
                                    v7750 = game:GetService("TweenService"):Create(l_Start_160, v7736, {
                                        Size = l_End_158.Size, 
                                        Transparency = v7741, 
                                        CFrame = l_End_158.CFrame
                                    });
                                    v7750:Play();
                                end;
                            end;
                            if not l_Stay_153 then
                                if l_Del_153 then
                                    task.wait(l_Del_153 + 0.1);
                                end;
                                v7750.Completed:Connect(function() --[[ Line: 4562 ]]
                                    -- upvalues: l_Model_154 (copy)
                                    l_Model_154:Destroy();
                                end);
                            end;
                            return;
                        else
                            warn("NO START OR END");
                            return;
                        end;
                    end);
                    v7730 = v1000;
                    local v7755 = {
                        FX = v7297.ContactTest, 
                        Maid = v7298._maid
                    };
                    v7731, v7732, v7733 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7755.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7732, 0) * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 2, 2);
                    v7730 = v7730(v7755);
                    v7730:ScaleTo(2);
                    v1021({
                        FX = v7730, 
                        On = true
                    });
                    v989({
                        FX = v7730, 
                        Scale = 0.5
                    });
                    for _, v7757 in pairs(v7730:GetDescendants()) do
                        if v7757:IsA("ParticleEmitter") then
                            v7757.Rate = v7757.Rate * 2;
                        end;
                    end;
                    task.spawn(function() --[[ Line: 18342 ]]
                        -- upvalues: v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7308 (ref), l_wait_19 (ref), l_TweenService_26 (ref), v1000 (ref), v7298 (ref), v1014 (ref), v989 (ref), v7714 (copy)
                        task.spawn(function() --[[ Line: 18345 ]]
                            -- upvalues: v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7308 (ref), l_wait_19 (ref), l_TweenService_26 (ref)
                            for _ = 1, 10 do
                                task.spawn(function() --[[ Line: 18347 ]]
                                    -- upvalues: v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7308 (ref), l_wait_19 (ref), l_TweenService_26 (ref)
                                    local v7759 = v7297.cyllinder2:Clone();
                                    local _, v7761, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                                    v7759:PivotTo(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7761, 0) * CFrame.new(0, 0, 1) * CFrame.Angles(2.443460952792061, math.rad((math.random(0, 360))), 0) * CFrame.new(0, -20, 0));
                                    v7759.Parent = l_Thrown_23;
                                    game.Debris:AddItem(v7759, 10);
                                    local _ = v7302:NextNumber(3, 6);
                                    local _ = v7302:NextNumber(0.0075, 0.015);
                                    local v7765 = nil;
                                    v7765 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                                    local l_v7308_0 = v7308;
                                    task.spawn(function() --[[ Line: 18364 ]]
                                        -- upvalues: l_v7308_0 (copy), v7759 (copy), l_wait_19 (ref)
                                        for v7767 = 1, #l_v7308_0, 2 do
                                            local v7768 = l_v7308_0[v7767];
                                            if v7768 then
                                                v7759.Decal.Texture = v7768;
                                            end;
                                            l_wait_19(0.1);
                                        end;
                                        v7759:Destroy();
                                    end);
                                    local l_Mesh_3 = v7759.Mesh;
                                    l_Mesh_3.Scale = l_Mesh_3.Scale * 3;
                                    l_TweenService_26:Create(v7759, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                                        CFrame = v7759.CFrame * CFrame.new(0, -10, 0)
                                    }):Play();
                                    l_TweenService_26:Create(v7759.Mesh, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                                        Scale = Vector3.new(v7759.Mesh.Scale.X * 0.3, v7759.Mesh.Scale.Y * 2, v7759.Mesh.Scale.Z * 0.3)
                                    }):Play();
                                end);
                                l_wait_19(0.1);
                            end;
                        end);
                        local l_v1000_12 = v1000;
                        local v7771 = {
                            FX = v7297.ExploTest, 
                            Maid = v7298._maid
                        };
                        local _, v7773, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        v7771.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7773, 0);
                        l_v1000_12 = l_v1000_12(v7771);
                        v1014(l_v1000_12);
                        v989({
                            FX = l_v1000_12, 
                            Scale = 0.5
                        });
                        l_wait_19(0.15);
                        v7714();
                    end);
                    l_wait_19(1.5);
                    v1021({
                        FX = v7730, 
                        On = false
                    });
                    v1021({
                        FX = v7728, 
                        On = false
                    });
                end);
                task.delay(8.834, function() --[[ Line: 18404 ]]
                    -- upvalues: v7395 (copy), v1090 (copy), v12 (ref), l_LastBreath_0 (copy), l_char_26 (copy), l_PrimaryPart_49 (copy), l_wait_19 (copy), v7298 (copy), l_TweenService_26 (copy), v1014 (ref), v983 (ref), l_Debris_0 (ref), v955 (ref), v1000 (ref), v7297 (copy), v989 (ref), v7302 (copy), l_Thrown_23 (copy), l_hit_18 (copy), v7580 (copy), v7396 (ref), l_LocalPlayer_0 (ref), l_char_26 (copy), v1034 (ref)
                    task.delay(2, function() --[[ Line: 18406 ]]
                        -- upvalues: v7395 (ref), v1090 (ref), v12 (ref)
                        if v7395 then
                            shared.SetCore(true, nil, true);
                        end;
                        local v7775 = RaycastParams.new();
                        v7775.FilterType = Enum.RaycastFilterType.Include;
                        v7775.FilterDescendantsInstances = {
                            workspace.Map, 
                            workspace.Built
                        };
                        local v7776 = workspace:Raycast(v1090.char.Torso.Position, Vector3.new(0, -1000, 0, 0), v7775);
                        if v7776 then
                            for v7777 = 1, 2 do
                                local l_v12_1 = v12;
                                local v7779 = {
                                    Effect = "Ground Crater", 
                                    Seed = v1090.Seed, 
                                    start = v7776.Position, 
                                    ["end"] = Vector3.new(0, -14, 0, 0), 
                                    size = v7777 * 6, 
                                    sizemult = v7777 + 4, 
                                    amount = 7, 
                                    nodebris = true, 
                                    NoUpSmoke = true, 
                                    nosound = true, 
                                    nosmoke = true
                                };
                                local v7780 = false;
                                if v7777 == 1 then
                                    v7780 = {
                                        minus = -3
                                    };
                                end;
                                v7779.stronger = v7780;
                                l_v12_1(v7779);
                                task.wait(0.095);
                            end;
                        end;
                    end);
                    local v7781 = require(game.ReplicatedStorage.Resources.RealDragonVfx.Utilities);
                    local l_Assets_0 = l_LastBreath_0.Assets;
                    local l_Modules_0 = l_LastBreath_0.Modules;
                    local v7784 = {};
                    local v7785 = RaycastParams.new();
                    v7785.FilterType = Enum.RaycastFilterType.Exclude;
                    v7785.FilterDescendantsInstances = {
                        workspace.Live, 
                        workspace.Thrown
                    };
                    local function v7873() --[[ Line: 18449 ]] --[[ Name: RealBoom ]]
                        -- upvalues: l_char_26 (ref), l_PrimaryPart_49 (ref), v7785 (copy), v7781 (copy), l_Assets_0 (copy), l_wait_19 (ref), v7298 (ref), l_TweenService_26 (ref), l_Modules_0 (copy), v1014 (ref), v983 (ref), l_Debris_0 (ref), v955 (ref), v1000 (ref), v7297 (ref), v989 (ref), v7302 (ref), l_Thrown_23 (ref)
                        local v7786, v7787, v7788 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                        local v7789 = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7787, 0) * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, -6);
                        local v7790 = game.Workspace:Raycast(v7789.Position + Vector3.new(0, 5, 0, 0), Vector3.new(0, -100, 0, 0), v7785);
                        if v7790 then
                            v7786 = CFrame.new(v7790.Position) * CFrame.new(0, 0.1, 0);
                            v7787 = function() --[[ Line: 18462 ]] --[[ Name: Ball ]]
                                -- upvalues: v7781 (ref), v7790 (copy), v7786 (copy), l_Assets_0 (ref), l_wait_19 (ref), v7298 (ref), l_TweenService_26 (ref), l_Modules_0 (ref), v1014 (ref), v983 (ref)
                                local v7791 = v7781.GetRaySurface(v7790);
                                local v7792 = CFrame.new(v7791.Position, v7786 * CFrame.new(0, 0, -40000).Position);
                                local v7793 = v7781.SpawnGroup(l_Assets_0.Impact);
                                l_wait_19(0.08);
                                for v7794 = 1, 5 do
                                    v7793:ScaleTo(5 + v7794 * 5 * 2);
                                    v7793:PivotTo(v7792);
                                    l_wait_19(0.01);
                                end;
                                v7793:Destroy();
                                local v7795 = v7781.SpawnGroup(l_Assets_0.Ball);
                                local _ = v7795.Welds.Sphere;
                                local v7797 = v7298._maid:give(Instance.new("NumberValue"));
                                v7298._maid:giveTask(v7797.Changed:Connect(function() --[[ Line: 18484 ]]
                                    -- upvalues: v7795 (copy), v7797 (copy)
                                    v7795:ScaleTo(v7797.Value);
                                end));
                                v7795:ScaleTo(1);
                                v7795:PivotTo(v7786 * CFrame.new(0, 5, 0));
                                v7797.Value = v7795:GetScale();
                                l_TweenService_26:Create(v7797, TweenInfo.new(0.1, Enum.EasingStyle.Sine), {
                                    Value = 4.3
                                }):Play();
                                local l_Wind2_0 = v7795.Wind2;
                                v7781.loopAndStopFlipbook(l_Wind2_0, l_Modules_0.WindFlipbooks.WindLoop, 0.5);
                                v7781.Emit(v7795);
                                l_wait_19(0.1);
                                for _, v7800 in pairs(v7795:GetDescendants()) do
                                    if v7800:IsA("ParticleEmitter") then
                                        if v7800.Name == "Balls" then
                                            v7800:Destroy();
                                        else
                                            v7800.Enabled = false;
                                        end;
                                    elseif v7800:IsA("BasePart") or v7800:IsA("Decal") then
                                        l_TweenService_26:Create(v7800, TweenInfo.new(0.1, Enum.EasingStyle.Sine), {
                                            Transparency = 1
                                        }):Play();
                                    end;
                                end;
                                game.Debris:AddItem(v7795, 3);
                                v1014(v7795);
                                v983({
                                    FX = v7795, 
                                    Count = 30
                                });
                            end;
                            v7788 = v7781.SpawnGroup(l_Assets_0.Beam);
                            v7788:PivotTo(v7786 * CFrame.new(0, 0, 0));
                            l_Debris_0:AddItem(v7788, 4.5);
                            v7781.Emit(v7788);
                            for _, v7802 in pairs(v7788:GetDescendants()) do
                                if v7802:IsA("Beam") then
                                    l_TweenService_26:Create(v7802, TweenInfo.new(3, Enum.EasingStyle.Sine), {
                                        TextureSpeed = 0
                                    }):Play();
                                end;
                            end;
                            local _ = task.spawn(function() --[[ Line: 18537 ]]
                                -- upvalues: v7298 (ref), v7788 (copy), l_TweenService_26 (ref), l_wait_19 (ref), v955 (ref)
                                local v7803 = v7298._maid:give(Instance.new("NumberValue"));
                                v7298._maid:giveTask(v7803.Changed:Connect(function() --[[ Line: 18540 ]]
                                    -- upvalues: v7788 (ref), v7803 (copy)
                                    v7788:ScaleTo(v7803.Value);
                                end));
                                v7788:ScaleTo(0.05);
                                v7803.Value = v7788:GetScale();
                                l_TweenService_26:Create(v7803, TweenInfo.new(1.4, Enum.EasingStyle.Exponential), {
                                    Value = 1.2
                                }):Play();
                                l_wait_19(0.2);
                                local _ = task.spawn(function() --[[ Line: 18551 ]]
                                    -- upvalues: v7788 (ref), v955 (ref)
                                    for _, v7805 in pairs(v7788:GetDescendants()) do
                                        if v7805:IsA("Beam") then
                                            v955(v7805, {
                                                Time = 0.35, 
                                                EasingStyle = "Sine", 
                                                Goal = {
                                                    Transparency = NumberSequence.new({
                                                        NumberSequenceKeypoint.new(0, 1), 
                                                        NumberSequenceKeypoint.new(1, 1)
                                                    })
                                                }
                                            });
                                            game.Debris:AddItem(v7805, 0.35);
                                        end;
                                    end;
                                end);
                                for _, v7808 in v7788.WindPack:GetDescendants() do
                                    if v7808:IsA("SpecialMesh") then
                                        l_TweenService_26:Create(v7808.Parent.Decal, TweenInfo.new(0.25), {
                                            Transparency = 1
                                        }):Play();
                                    end;
                                    if v7808:IsA("BasePart") then
                                        v7788.Welds[v7808.Name]:Destroy();
                                        v7808.Anchored = true;
                                    end;
                                end;
                                for _, v7810 in v7788:GetDescendants() do
                                    if v7810:IsA("ParticleEmitter") then
                                        v7810.Enabled = false;
                                        v7810:Destroy();
                                    end;
                                end;
                            end);
                            for _, v7813 in v7788.WindPack:GetChildren() do
                                v7781.loopAndStopFlipbook(v7813, l_Modules_0.WindFlipbooks.WindLoop, 1);
                            end;
                            l_wait_19(0.1);
                            local v7814 = v1000({
                                FX = v7297.FinalExplosion, 
                                Maid = v7298._maid, 
                                Anchor = v7786 * CFrame.Angles(1.5707963267948966, 0, 0)
                            });
                            v7814:ScaleTo(4);
                            v989({
                                FX = v7814, 
                                Scale = 2
                            });
                            v1014(v7814);
                            for _ = 1, 6 do
                                local v7816 = v7297.WindTime:Clone();
                                v7816:ScaleTo(v7302:NextNumber(0.5, 1) * 8);
                                local v7817 = {
                                    Model = v7816, 
                                    T = 0.7, 
                                    Anchor = v7786 * CFrame.new(0, v7302:NextNumber(0, 0.5), 0) * CFrame.Angles(math.rad((v7302:NextNumber(-15, 15))), math.rad((v7302:NextNumber(0, 360, 0))), (math.rad((v7302:NextNumber(-15, 15))))), 
                                    Info = TweenInfo.new(v7302:NextNumber(1.2, 2.32), Enum.EasingStyle.Sine)
                                };
                                task.spawn(function() --[[ Line: 4475 ]]
                                    -- upvalues: v7817 (copy)
                                    local l_Model_155 = v7817.Model;
                                    local v7819 = v7817.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                                    local l_Start_161 = l_Model_155:FindFirstChild("Start");
                                    local l_End_159 = l_Model_155:FindFirstChild("End");
                                    local l_Stay_154 = v7817.Stay;
                                    local l_Anchor_155 = v7817.Anchor;
                                    local v7824 = v7817.EndT or 1;
                                    local l_Del_154 = v7817.Del;
                                    local l_Skip_154 = v7817.Skip;
                                    if l_Start_161 and l_End_159 then
                                        l_Model_155.PrimaryPart = l_Start_161;
                                        if not l_Skip_154 then
                                            for _, v7828 in pairs(l_Model_155:GetChildren()) do
                                                if v7828:IsA("BasePart") then
                                                    v7828.CanCollide = false;
                                                    v7828.Anchored = true;
                                                end;
                                            end;
                                        end;
                                        if l_Anchor_155 then
                                            l_Model_155:SetPrimaryPartCFrame(l_Anchor_155);
                                        end;
                                        if v7817.T then
                                            l_Start_161.Transparency = v7817.T;
                                        end;
                                        l_End_159.Transparency = 1;
                                        l_Model_155.Parent = workspace.Thrown;
                                        local l_Decal_311 = l_Start_161:FindFirstChildOfClass("Decal");
                                        local l_SpecialMesh_310 = l_Start_161:FindFirstChildOfClass("SpecialMesh");
                                        local l_SpecialMesh_311 = l_End_159:FindFirstChildOfClass("SpecialMesh");
                                        local l_Decal_312 = l_End_159:FindFirstChildOfClass("Decal");
                                        if l_Decal_312 and not l_Skip_154 then
                                            l_Decal_312.Transparency = 1;
                                        end;
                                        local v7833 = nil;
                                        if l_Del_154 then
                                            game:GetService("TweenService"):Create(l_Start_161, v7819, {
                                                Size = l_End_159.Size, 
                                                CFrame = l_End_159.CFrame
                                            }):Play();
                                            task.delay(l_Del_154, function() --[[ Line: 4519 ]]
                                                -- upvalues: v7833 (ref), l_Start_161 (copy), v7819 (copy), v7824 (copy), l_Decal_311 (copy), l_SpecialMesh_310 (copy), l_SpecialMesh_311 (copy)
                                                v7833 = game:GetService("TweenService"):Create(l_Start_161, v7819, {
                                                    Transparency = v7824
                                                });
                                                v7833:Play();
                                                if l_Decal_311 then
                                                    for _, v7835 in pairs(l_Start_161:GetChildren()) do
                                                        if v7835:IsA("Decal") then
                                                            game:GetService("TweenService"):Create(v7835, v7819, {
                                                                Transparency = v7824
                                                            }):Play();
                                                        end;
                                                    end;
                                                end;
                                                if l_SpecialMesh_310 then
                                                    v7833 = game:GetService("TweenService"):Create(l_SpecialMesh_310, v7819, {
                                                        Scale = l_SpecialMesh_311.Scale
                                                    }):Play();
                                                end;
                                            end);
                                        else
                                            if l_SpecialMesh_310 then
                                                game:GetService("TweenService"):Create(l_SpecialMesh_310, v7819, {
                                                    Scale = l_SpecialMesh_311.Scale
                                                }):Play();
                                            end;
                                            if l_Decal_311 then
                                                for _, v7837 in pairs(l_Start_161:GetChildren()) do
                                                    if v7837:IsA("Decal") then
                                                        game:GetService("TweenService"):Create(v7837, v7819, {
                                                            Transparency = v7824
                                                        }):Play();
                                                    end;
                                                end;
                                                v7833 = game:GetService("TweenService"):Create(l_Start_161, v7819, {
                                                    Size = l_End_159.Size, 
                                                    CFrame = l_End_159.CFrame
                                                });
                                                v7833:Play();
                                            else
                                                v7833 = game:GetService("TweenService"):Create(l_Start_161, v7819, {
                                                    Size = l_End_159.Size, 
                                                    Transparency = v7824, 
                                                    CFrame = l_End_159.CFrame
                                                });
                                                v7833:Play();
                                            end;
                                        end;
                                        if not l_Stay_154 then
                                            if l_Del_154 then
                                                task.wait(l_Del_154 + 0.1);
                                            end;
                                            v7833.Completed:Connect(function() --[[ Line: 4562 ]]
                                                -- upvalues: l_Model_155 (copy)
                                                l_Model_155:Destroy();
                                            end);
                                        end;
                                        return;
                                    else
                                        warn("NO START OR END");
                                        return;
                                    end;
                                end);
                            end;
                            task.spawn(function() --[[ Line: 18613 ]]
                                -- upvalues: v7297 (ref), v7786 (copy), l_Thrown_23 (ref), l_wait_19 (ref), v7302 (ref), l_TweenService_26 (ref)
                                for _ = 1, 6 do
                                    task.spawn(function() --[[ Line: 18615 ]]
                                        -- upvalues: v7297 (ref), v7786 (ref), l_Thrown_23 (ref), l_wait_19 (ref), v7302 (ref), l_TweenService_26 (ref)
                                        for _ = 1, 1 do
                                            local v7840 = Color3.fromRGB(186, 186, 186);
                                            local _ = math.random(-180, 180);
                                            local v7842 = v7297.Tornado2:Clone();
                                            v7842:PivotTo(v7786 * CFrame.new(0, -5, 0) * CFrame.Angles(0, math.rad((math.random(0, 360))), 0));
                                            v7842.Parent = l_Thrown_23;
                                            v7842.Decal.Color3 = Color3.fromRGB(555, 555, 555);
                                            game.Debris:AddItem(v7842, 33);
                                            v7842.Mesh.Scale = v7842.Mesh.Scale * 5;
                                            task.spawn(function() --[[ Line: 18631 ]]
                                                -- upvalues: v7842 (copy), l_wait_19 (ref)
                                                local v7843 = require(v7842.Tornado);
                                                for v7844 = 1, #v7843, 2 do
                                                    if v7842.Parent then
                                                        v7842.Decal.Texture = v7843[v7844];
                                                        l_wait_19(0.5 / #v7843 * 2);
                                                    else
                                                        break;
                                                    end;
                                                end;
                                                v7842:Destroy();
                                            end);
                                            local l_Decal_313 = v7842.Decal;
                                            local l_Mesh_4 = v7842.Mesh;
                                            l_Mesh_4.Scale = l_Mesh_4.Scale / 2 + Vector3.new(0, v7842.Mesh.Scale.Y * 3, 0);
                                            local v7847 = v7302:NextNumber(4, 6) * 8;
                                            l_Decal_313.Transparency = 1;
                                            l_TweenService_26:Create(l_Decal_313, TweenInfo.new(0.2, Enum.EasingStyle.Sine), {
                                                Transparency = 0
                                            }):Play();
                                            task.spawn(function() --[[ Line: 18654 ]]
                                                -- upvalues: l_TweenService_26 (ref), l_Mesh_4 (copy), v7847 (copy), v7842 (copy), l_Decal_313 (copy), v7840 (copy)
                                                local v7848 = l_TweenService_26:Create(l_Mesh_4, TweenInfo.new(2.25, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut), {
                                                    Scale = Vector3.new(l_Mesh_4.Scale.X * v7847, l_Mesh_4.Scale.Y * v7847, l_Mesh_4.Scale.Z * v7847)
                                                });
                                                local v7849 = l_TweenService_26:Create(v7842, TweenInfo.new(2.25, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut), {
                                                    CFrame = v7842.CFrame * CFrame.new(0, 10, 0) * CFrame.Angles(0, math.rad((math.random(90, 180))), 0)
                                                });
                                                task.delay(0.2, function() --[[ Line: 18658 ]]
                                                    -- upvalues: l_TweenService_26 (ref), l_Decal_313 (ref), v7840 (ref)
                                                    l_TweenService_26:Create(l_Decal_313, TweenInfo.new(0.8499999999999999, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut), {
                                                        Color3 = v7840, 
                                                        Transparency = 1
                                                    }):Play();
                                                end);
                                                v7848:Play();
                                                v7849:Play();
                                            end);
                                        end;
                                    end);
                                    l_wait_19(0.1);
                                end;
                            end);
                            for _ = 1, 22 do
                                local v7851 = v7297.WindTime:Clone();
                                v7851:ScaleTo(v7302:NextNumber(0.5, 2) * 16);
                                local l_Start_162 = v7851.Start;
                                l_Start_162.Size = l_Start_162.Size * 0.1;
                                l_Start_162 = {
                                    Model = v7851, 
                                    T = 0.5, 
                                    Anchor = v7786 * CFrame.new(0, v7302:NextNumber(0, 0.5), 0) * CFrame.Angles(math.rad((v7302:NextNumber(-65, 65))), math.rad((v7302:NextNumber(0, 360, 0))), (math.rad((v7302:NextNumber(-65, 65))))), 
                                    Info = TweenInfo.new(v7302:NextNumber(0.3, 0.5) * 2, Enum.EasingStyle.Sine)
                                };
                                task.spawn(function() --[[ Line: 4475 ]]
                                    -- upvalues: l_Start_162 (copy)
                                    local l_Model_156 = l_Start_162.Model;
                                    local v7854 = l_Start_162.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                                    local l_Start_163 = l_Model_156:FindFirstChild("Start");
                                    local l_End_160 = l_Model_156:FindFirstChild("End");
                                    local l_Stay_155 = l_Start_162.Stay;
                                    local l_Anchor_156 = l_Start_162.Anchor;
                                    local v7859 = l_Start_162.EndT or 1;
                                    local l_Del_155 = l_Start_162.Del;
                                    local l_Skip_155 = l_Start_162.Skip;
                                    if l_Start_163 and l_End_160 then
                                        l_Model_156.PrimaryPart = l_Start_163;
                                        if not l_Skip_155 then
                                            for _, v7863 in pairs(l_Model_156:GetChildren()) do
                                                if v7863:IsA("BasePart") then
                                                    v7863.CanCollide = false;
                                                    v7863.Anchored = true;
                                                end;
                                            end;
                                        end;
                                        if l_Anchor_156 then
                                            l_Model_156:SetPrimaryPartCFrame(l_Anchor_156);
                                        end;
                                        if l_Start_162.T then
                                            l_Start_163.Transparency = l_Start_162.T;
                                        end;
                                        l_End_160.Transparency = 1;
                                        l_Model_156.Parent = workspace.Thrown;
                                        local l_Decal_314 = l_Start_163:FindFirstChildOfClass("Decal");
                                        local l_SpecialMesh_312 = l_Start_163:FindFirstChildOfClass("SpecialMesh");
                                        local l_SpecialMesh_313 = l_End_160:FindFirstChildOfClass("SpecialMesh");
                                        local l_Decal_315 = l_End_160:FindFirstChildOfClass("Decal");
                                        if l_Decal_315 and not l_Skip_155 then
                                            l_Decal_315.Transparency = 1;
                                        end;
                                        local v7868 = nil;
                                        if l_Del_155 then
                                            game:GetService("TweenService"):Create(l_Start_163, v7854, {
                                                Size = l_End_160.Size, 
                                                CFrame = l_End_160.CFrame
                                            }):Play();
                                            task.delay(l_Del_155, function() --[[ Line: 4519 ]]
                                                -- upvalues: v7868 (ref), l_Start_163 (copy), v7854 (copy), v7859 (copy), l_Decal_314 (copy), l_SpecialMesh_312 (copy), l_SpecialMesh_313 (copy)
                                                v7868 = game:GetService("TweenService"):Create(l_Start_163, v7854, {
                                                    Transparency = v7859
                                                });
                                                v7868:Play();
                                                if l_Decal_314 then
                                                    for _, v7870 in pairs(l_Start_163:GetChildren()) do
                                                        if v7870:IsA("Decal") then
                                                            game:GetService("TweenService"):Create(v7870, v7854, {
                                                                Transparency = v7859
                                                            }):Play();
                                                        end;
                                                    end;
                                                end;
                                                if l_SpecialMesh_312 then
                                                    v7868 = game:GetService("TweenService"):Create(l_SpecialMesh_312, v7854, {
                                                        Scale = l_SpecialMesh_313.Scale
                                                    }):Play();
                                                end;
                                            end);
                                        else
                                            if l_SpecialMesh_312 then
                                                game:GetService("TweenService"):Create(l_SpecialMesh_312, v7854, {
                                                    Scale = l_SpecialMesh_313.Scale
                                                }):Play();
                                            end;
                                            if l_Decal_314 then
                                                for _, v7872 in pairs(l_Start_163:GetChildren()) do
                                                    if v7872:IsA("Decal") then
                                                        game:GetService("TweenService"):Create(v7872, v7854, {
                                                            Transparency = v7859
                                                        }):Play();
                                                    end;
                                                end;
                                                v7868 = game:GetService("TweenService"):Create(l_Start_163, v7854, {
                                                    Size = l_End_160.Size, 
                                                    CFrame = l_End_160.CFrame
                                                });
                                                v7868:Play();
                                            else
                                                v7868 = game:GetService("TweenService"):Create(l_Start_163, v7854, {
                                                    Size = l_End_160.Size, 
                                                    Transparency = v7859, 
                                                    CFrame = l_End_160.CFrame
                                                });
                                                v7868:Play();
                                            end;
                                        end;
                                        if not l_Stay_155 then
                                            if l_Del_155 then
                                                task.wait(l_Del_155 + 0.1);
                                            end;
                                            v7868.Completed:Connect(function() --[[ Line: 4562 ]]
                                                -- upvalues: l_Model_156 (copy)
                                                l_Model_156:Destroy();
                                            end);
                                        end;
                                        return;
                                    else
                                        warn("NO START OR END");
                                        return;
                                    end;
                                end);
                                l_wait_19(0.01);
                            end;
                        end;
                    end;
                    local l_v1000_13 = v1000;
                    local v7875 = {
                        FX = v7297.Pre, 
                        Maid = v7298._maid
                    };
                    local l_new_6 = CFrame.new;
                    local v7877, v7878, v7879 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    local l_X_1 = (CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7878, 0)).Position.X;
                    local l_Y_2 = l_hit_18.Head.Position.Y;
                    local v7882, v7883;
                    v7879, v7882, v7883 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7875.Anchor = l_new_6(l_X_1, l_Y_2, (CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7882, 0)).Position.Z) * CFrame.new(0, -10, 0);
                    l_v1000_13 = l_v1000_13(v7875);
                    l_v1000_13:ScaleTo(4);
                    for _, v7885 in pairs(l_v1000_13:GetDescendants()) do
                        local l_v7885_Attribute_0 = v7885:GetAttribute("EmitDelay");
                        if l_v7885_Attribute_0 then
                            v7885:SetAttribute("EmitDelay", l_v7885_Attribute_0 * 2 * 1);
                        end;
                    end;
                    v983({
                        FX = l_v1000_13, 
                        Count = 5
                    });
                    v989({
                        FX = l_v1000_13, 
                        Scale = 2
                    });
                    v1014(l_v1000_13);
                    v7875 = v1000;
                    local v7887 = {
                        FX = v7297.BlackIn, 
                        Maid = v7298._maid
                    };
                    local v7888;
                    l_Y_2, v7888, v7877 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7887.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7888, 0) * CFrame.new(0, 0, 0);
                    v7875 = v7875(v7887);
                    v7875:ScaleTo(23);
                    v989({
                        FX = v7875, 
                        Scale = 0.5
                    });
                    v1014(v7875);
                    task.spawn(function() --[[ Line: 18709 ]]
                        -- upvalues: l_wait_19 (ref), v7580 (ref), v7395 (ref), v12 (ref), v7396 (ref), l_LocalPlayer_0 (ref), l_char_26 (ref)
                        l_wait_19(0.45);
                        local l_Impact3_0 = v7580.Impact3;
                        if v7395 and l_Impact3_0 then
                            local v7890 = nil;
                            local v7891 = 1;
                            local _ = tick();
                            local v7893 = nil;
                            local l_loop_2 = shared.loop;
                            local v7895 = true;
                            do
                                local l_v7890_0, l_v7891_0, l_v7893_0 = v7890, v7891, v7893;
                                l_v7893_0 = l_loop_2(function() --[[ Line: 17983 ]]
                                    -- upvalues: l_Impact3_0 (copy), l_v7891_0 (ref), v7895 (copy), v12 (ref), l_v7893_0 (ref), l_v7890_0 (ref)
                                    local v7899 = l_Impact3_0[l_v7891_0];
                                    if not v7899 then
                                        if v7895 then
                                            v12({
                                                Effect = "Camshake", 
                                                Intensity = 20, 
                                                Last = 1.25
                                            });
                                        end;
                                        for _, v7901 in pairs(l_Impact3_0) do
                                            v7901:Destroy();
                                        end;
                                        return l_v7893_0();
                                    else
                                        l_v7891_0 = l_v7891_0 + 1;
                                        v7899.Size = UDim2.new(1, 0, 1, 0);
                                        if l_v7890_0 then
                                            l_v7890_0:Destroy();
                                        end;
                                        l_v7890_0 = v7899;
                                        return;
                                    end;
                                end, 24);
                            end;
                        end;
                        task.spawn(function() --[[ Line: 18712 ]]
                            -- upvalues: v7395 (ref), v7396 (ref), l_LocalPlayer_0 (ref), l_char_26 (ref)
                            if v7395 then
                                local l_Hint_0 = workspace:FindFirstChildOfClass("Hint");
                                if l_Hint_0 then
                                    l_Hint_0:Destroy();
                                end;
                                local v7903 = tick();
                                while task.wait() do
                                    if tick() - v7903 > 2.12 then
                                        v7396();
                                        if l_LocalPlayer_0.Character ~= l_char_26 then
                                            local l_Character_17 = game.Players.LocalPlayer.Character;
                                            local l_Head_1 = l_Character_17.Head;
                                            local l_CurrentCamera_9 = workspace.CurrentCamera;
                                            l_Character_17:SetAttribute("NoHeadLerp", true);
                                            l_CurrentCamera_9.CameraSubject = l_Head_1;
                                            task.delay(0.001, function() --[[ Line: 18743 ]]
                                                -- upvalues: l_Character_17 (copy), l_CurrentCamera_9 (copy)
                                                l_Character_17.Humanoid.CameraOffset = Vector3.new(0, -0.5, 0, 0);
                                                l_CurrentCamera_9.CameraSubject = l_Character_17.Humanoid;
                                                l_Character_17:SetAttribute("NoHeadLerp", false);
                                            end);
                                            return;
                                        else
                                            break;
                                        end;
                                    end;
                                end;
                            end;
                        end);
                        task.delay(0.95, function() --[[ Line: 18755 ]]
                            -- upvalues: l_char_26 (ref)
                            for _ = 1, 1 do
                                shared.sfx({
                                    SoundId = "rbxassetid://105104068205219", 
                                    CFrame = l_char_26.Torso.CFrame, 
                                    RollOffMode = Enum.RollOffMode.LinearSquare, 
                                    RollOffMaxDistance = 600, 
                                    Volume = 10
                                }):Play();
                            end;
                        end);
                        if v7395 then
                            shared.sfx({
                                SoundId = "rbxassetid://87870885268139", 
                                Parent = workspace, 
                                Volume = 8
                            }):Play();
                        end;
                    end);
                    l_wait_19(1.3);
                    l_wait_19(0.45);
                    v7887 = function() --[[ Line: 18778 ]] --[[ Name: Test ]]
                        -- upvalues: l_PrimaryPart_49 (ref), v7298 (ref), l_LastBreath_0 (ref), v955 (ref), l_wait_19 (ref), v7302 (ref), l_Thrown_23 (ref), v1034 (ref), v7873 (copy), v7784 (copy)
                        (function() --[[ Line: 18780 ]] --[[ Name: NewExplosion ]]
                            -- upvalues: l_PrimaryPart_49 (ref), v7298 (ref), l_LastBreath_0 (ref), v955 (ref), l_wait_19 (ref), v7302 (ref), l_Thrown_23 (ref), v1034 (ref), v7873 (ref), v7784 (ref)
                            local v7908 = l_PrimaryPart_49.CFrame * CFrame.new(3.539134979248047, 0.5664405822753906, -117.58331298828125);
                            local v7909 = v7298._maid:give(l_LastBreath_0.Resources.Main:Clone());
                            local v7910 = v7298._maid:give(Instance.new("NumberValue"));
                            v7910.Parent = v7909;
                            v7910.Name = "CurrentScale";
                            v7910.Value = 0.3;
                            v955(v7910, {
                                Time = 0.75, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    Value = 20
                                }
                            });
                            v7909:SetPrimaryPartCFrame(v7908);
                            local v7911 = {};
                            spawn(function() --[[ Line: 18797 ]]
                                -- upvalues: v7911 (copy), l_wait_19 (ref)
                                while true do
                                    for _, v7913 in pairs(v7911) do
                                        if v7913.Name == "Stars" then
                                            v7913.CFrame = v7913.CFrame * CFrame.Angles(0, 0.03490658503988659, 0);
                                        else
                                            v7913.CFrame = v7913.CFrame * CFrame.Angles(0, 0.3490658503988659, 0);
                                        end;
                                    end;
                                    l_wait_19(0.01);
                                end;
                            end);
                            local function v7922(v7914, v7915) --[[ Line: 18810 ]] --[[ Name: CreateWave ]]
                                -- upvalues: v7908 (copy), v7302 (ref), v7910 (copy), v955 (ref), l_Thrown_23 (ref), v1034 (ref), v7911 (copy)
                                if not v7914 then
                                    return;
                                else
                                    local v7916 = {
                                        4, 
                                        12
                                    };
                                    local v7917 = {
                                        1.5, 
                                        2.2
                                    };
                                    local l_v7908_0 = v7908;
                                    if v7915 then
                                        l_v7908_0 = v7915;
                                    end;
                                    local v7919 = v7914:Clone();
                                    v7919.CFrame = l_v7908_0 * CFrame.new(0, v7302:NextNumber(v7916[1], v7916[1]), 0) * CFrame.Angles(math.rad((math.random(-5, 5))), math.rad((math.random(-360, 360))), (math.rad((math.random(-5, 5)))));
                                    local l_Scale_1 = v7919.Mesh.Scale;
                                    v7919.Mesh.Scale = v7919.Mesh.Scale * 0.8 * v7910.Value;
                                    v7919.Decal.Transparency = 1;
                                    v955(v7919.Decal, {
                                        Time = 0.075, 
                                        EasingStyle = "Sine", 
                                        Goal = {
                                            Transparency = 0
                                        }
                                    });
                                    v7919.Mesh.Scale = l_Scale_1;
                                    local v7921 = l_Scale_1 * v7302:NextNumber(v7917[1], v7917[2]) * v7910.Value;
                                    v955(v7919.Mesh, {
                                        Time = 0.65, 
                                        EasingStyle = "Sine", 
                                        Goal = {
                                            Scale = v7921
                                        }
                                    });
                                    v7919.Parent = l_Thrown_23;
                                    v1034({
                                        Mesh = v7919
                                    });
                                    table.insert(v7911, v7919);
                                    return;
                                end;
                            end;
                            local v7923 = true;
                            v7298._maid:giveTask(game:GetService("RunService").RenderStepped:Connect(function() --[[ Line: 18838 ]]
                                -- upvalues: v7909 (copy), v7923 (ref), v7922 (copy), l_LastBreath_0 (ref), v7908 (copy)
                                v7909:SetPrimaryPartCFrame(v7909.PrimaryPart.CFrame * CFrame.Angles(0, 0.2617993877991494, 0));
                                if math.random(1, 3) == 1 and v7923 then
                                    v7922(l_LastBreath_0.Resources.Main.Flips:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.Main.Flips:GetChildren())], v7908);
                                end;
                            end));
                            task.delay(1.5, function() --[[ Line: 18845 ]]
                                -- upvalues: v7923 (ref)
                                v7923 = false;
                            end);
                            local l_CC2_0 = l_LastBreath_0.Resources.CC2;
                            v7298.Expland1 = v7298._maid:give(l_LastBreath_0.Resources.Expland1:Clone());
                            v7298.Expland1:SetPrimaryPartCFrame(v7908 * CFrame.new(0, 25, 0));
                            v7298.Expland1.Parent = l_Thrown_23;
                            v7298.Expland1Scale = v7298._maid:give(Instance.new("NumberValue"));
                            v7298._maid:giveTask(v7298.Expland1Scale.Changed:Connect(function() --[[ Line: 18860 ]]
                                -- upvalues: v7298 (ref)
                                v7298.Expland1:ScaleTo(v7298.Expland1Scale.Value);
                            end));
                            v7298.Expland1Scale.Value = 0.1;
                            v955(v7298.Expland1Scale, {
                                Time = 0.6, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    Value = 3
                                }
                            });
                            v955(v7298.Expland1.PrimaryPart, {
                                Time = 0.3, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    CFrame = v7298.Expland1.PrimaryPart.CFrame * CFrame.new(0, 25, 0)
                                }
                            });
                            l_wait_19(0.3);
                            l_CC2_0 = l_LastBreath_0.Resources.CC3;
                            v7298.Expland1Scale.Value = 4;
                            task.delay(0.05, function() --[[ Line: 18871 ]]
                                -- upvalues: v7298 (ref), l_LastBreath_0 (ref), l_wait_19 (ref)
                                v7298.Expland1Scale.Value = 3;
                                local _ = l_LastBreath_0.Resources.CC3;
                                l_wait_19(0.08);
                                for _ = 1, 5 do
                                    for _, v7928 in pairs(v7298.Expland1:GetChildren()) do
                                        v7928.Size = v7928.Size + Vector3.new(0, 40, 0, 0);
                                    end;
                                    local _ = l_LastBreath_0.Resources.CCS:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.CCS:GetChildren())];
                                    l_wait_19(0.04);
                                end;
                            end);
                            for _, v7931 in pairs(v7298.Expland1:GetChildren()) do
                                if v7931:IsA("BasePart") then
                                    local l_FirstChild_14 = l_LastBreath_0.Resources.Expland2:FindFirstChild(v7931.Name);
                                    if l_FirstChild_14 then
                                        v955(v7931, {
                                            Time = 0.125, 
                                            EasingStyle = "EntranceExpressive", 
                                            Goal = {
                                                Size = l_FirstChild_14.Size
                                            }
                                        });
                                    end;
                                end;
                            end;
                            v955(v7298.Expland1.PrimaryPart, {
                                Time = 0.3, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    CFrame = v7298.Expland1.PrimaryPart.CFrame * CFrame.new(0, 45, 0)
                                }
                            });
                            l_wait_19(0.3);
                            l_CC2_0 = l_LastBreath_0.Resources.CC4;
                            v7298.Expland2 = v7298._maid:give(l_LastBreath_0.Resources.Expland3:Clone());
                            v7298.Expland2:SetPrimaryPartCFrame(v7908 * CFrame.new(0, 45, 0));
                            v7298.Expland2.Parent = l_Thrown_23;
                            v7298.Expland2Scale = v7298._maid:give(Instance.new("NumberValue"));
                            v7298._maid:giveTask(v7298.Expland2Scale.Changed:Connect(function() --[[ Line: 18906 ]]
                                -- upvalues: v7298 (ref)
                                v7298.Expland2:ScaleTo(v7298.Expland2Scale.Value);
                            end));
                            spawn(function() --[[ Line: 18910 ]]
                                -- upvalues: v7922 (copy), l_LastBreath_0 (ref), v7908 (copy), l_wait_19 (ref)
                                for _ = 1, 5 do
                                    v7922(l_LastBreath_0.Resources.Main.Flips:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.Main.Flips:GetChildren())], v7908 * CFrame.new(0, 30, 0));
                                    l_wait_19(0.1);
                                end;
                            end);
                            v7298.Expland2Scale.Value = 0.3;
                            v955(v7298.Expland2Scale, {
                                Time = 0.1, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    Value = 1.3
                                }
                            });
                            v955(v7298.Expland2.PrimaryPart, {
                                Time = 0.2, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    CFrame = v7298.Expland2.PrimaryPart.CFrame * CFrame.new(0, 85, 0)
                                }
                            });
                            l_wait_19(0.2);
                            v7298.Expland2:Destroy();
                            v7298.Expland1:Destroy();
                            l_CC2_0 = l_LastBreath_0.Resources.CC4;
                            v7873();
                            l_wait_19(0.75);
                            table.insert(v7784, v7298.Big2);
                            table.insert(v7784, v7909);
                            for _, v7935 in pairs(v7784) do
                                if v7935:IsA("Model") or v7935:IsA("BasePart") then
                                    for _, v7937 in pairs(v7935:GetDescendants()) do
                                        if v7937:IsA("ParticleEmitter") then
                                            v7937.Enabled = false;
                                        end;
                                    end;
                                end;
                            end;
                        end)();
                    end;
                    (function() --[[ Line: 18780 ]] --[[ Name: NewExplosion ]]
                        -- upvalues: l_PrimaryPart_49 (ref), v7298 (ref), l_LastBreath_0 (ref), v955 (ref), l_wait_19 (ref), v7302 (ref), l_Thrown_23 (ref), v1034 (ref), v7873 (copy), v7784 (copy)
                        local v7938 = l_PrimaryPart_49.CFrame * CFrame.new(3.539134979248047, 0.5664405822753906, -117.58331298828125);
                        local v7939 = v7298._maid:give(l_LastBreath_0.Resources.Main:Clone());
                        local v7940 = v7298._maid:give(Instance.new("NumberValue"));
                        v7940.Parent = v7939;
                        v7940.Name = "CurrentScale";
                        v7940.Value = 0.3;
                        v955(v7940, {
                            Time = 0.75, 
                            EasingStyle = "Sine", 
                            Goal = {
                                Value = 20
                            }
                        });
                        v7939:SetPrimaryPartCFrame(v7938);
                        local v7941 = {};
                        spawn(function() --[[ Line: 18797 ]]
                            -- upvalues: v7941 (copy), l_wait_19 (ref)
                            while true do
                                for _, v7943 in pairs(v7941) do
                                    if v7943.Name == "Stars" then
                                        v7943.CFrame = v7943.CFrame * CFrame.Angles(0, 0.03490658503988659, 0);
                                    else
                                        v7943.CFrame = v7943.CFrame * CFrame.Angles(0, 0.3490658503988659, 0);
                                    end;
                                end;
                                l_wait_19(0.01);
                            end;
                        end);
                        local function v7952(v7944, v7945) --[[ Line: 18810 ]] --[[ Name: CreateWave ]]
                            -- upvalues: v7938 (copy), v7302 (ref), v7940 (copy), v955 (ref), l_Thrown_23 (ref), v1034 (ref), v7941 (copy)
                            if not v7944 then
                                return;
                            else
                                local v7946 = {
                                    4, 
                                    12
                                };
                                local v7947 = {
                                    1.5, 
                                    2.2
                                };
                                local l_v7938_0 = v7938;
                                if v7945 then
                                    l_v7938_0 = v7945;
                                end;
                                local v7949 = v7944:Clone();
                                v7949.CFrame = l_v7938_0 * CFrame.new(0, v7302:NextNumber(v7946[1], v7946[1]), 0) * CFrame.Angles(math.rad((math.random(-5, 5))), math.rad((math.random(-360, 360))), (math.rad((math.random(-5, 5)))));
                                local l_Scale_2 = v7949.Mesh.Scale;
                                v7949.Mesh.Scale = v7949.Mesh.Scale * 0.8 * v7940.Value;
                                v7949.Decal.Transparency = 1;
                                v955(v7949.Decal, {
                                    Time = 0.075, 
                                    EasingStyle = "Sine", 
                                    Goal = {
                                        Transparency = 0
                                    }
                                });
                                v7949.Mesh.Scale = l_Scale_2;
                                local v7951 = l_Scale_2 * v7302:NextNumber(v7947[1], v7947[2]) * v7940.Value;
                                v955(v7949.Mesh, {
                                    Time = 0.65, 
                                    EasingStyle = "Sine", 
                                    Goal = {
                                        Scale = v7951
                                    }
                                });
                                v7949.Parent = l_Thrown_23;
                                v1034({
                                    Mesh = v7949
                                });
                                table.insert(v7941, v7949);
                                return;
                            end;
                        end;
                        local v7953 = true;
                        v7298._maid:giveTask(game:GetService("RunService").RenderStepped:Connect(function() --[[ Line: 18838 ]]
                            -- upvalues: v7939 (copy), v7953 (ref), v7952 (copy), l_LastBreath_0 (ref), v7938 (copy)
                            v7939:SetPrimaryPartCFrame(v7939.PrimaryPart.CFrame * CFrame.Angles(0, 0.2617993877991494, 0));
                            if math.random(1, 3) == 1 and v7953 then
                                v7952(l_LastBreath_0.Resources.Main.Flips:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.Main.Flips:GetChildren())], v7938);
                            end;
                        end));
                        task.delay(1.5, function() --[[ Line: 18845 ]]
                            -- upvalues: v7953 (ref)
                            v7953 = false;
                        end);
                        local l_CC2_1 = l_LastBreath_0.Resources.CC2;
                        v7298.Expland1 = v7298._maid:give(l_LastBreath_0.Resources.Expland1:Clone());
                        v7298.Expland1:SetPrimaryPartCFrame(v7938 * CFrame.new(0, 25, 0));
                        v7298.Expland1.Parent = l_Thrown_23;
                        v7298.Expland1Scale = v7298._maid:give(Instance.new("NumberValue"));
                        v7298._maid:giveTask(v7298.Expland1Scale.Changed:Connect(function() --[[ Line: 18860 ]]
                            -- upvalues: v7298 (ref)
                            v7298.Expland1:ScaleTo(v7298.Expland1Scale.Value);
                        end));
                        v7298.Expland1Scale.Value = 0.1;
                        v955(v7298.Expland1Scale, {
                            Time = 0.6, 
                            EasingStyle = "Sine", 
                            Goal = {
                                Value = 3
                            }
                        });
                        v955(v7298.Expland1.PrimaryPart, {
                            Time = 0.3, 
                            EasingStyle = "Sine", 
                            Goal = {
                                CFrame = v7298.Expland1.PrimaryPart.CFrame * CFrame.new(0, 25, 0)
                            }
                        });
                        l_wait_19(0.3);
                        l_CC2_1 = l_LastBreath_0.Resources.CC3;
                        v7298.Expland1Scale.Value = 4;
                        task.delay(0.05, function() --[[ Line: 18871 ]]
                            -- upvalues: v7298 (ref), l_LastBreath_0 (ref), l_wait_19 (ref)
                            v7298.Expland1Scale.Value = 3;
                            local _ = l_LastBreath_0.Resources.CC3;
                            l_wait_19(0.08);
                            for _ = 1, 5 do
                                for _, v7958 in pairs(v7298.Expland1:GetChildren()) do
                                    v7958.Size = v7958.Size + Vector3.new(0, 40, 0, 0);
                                end;
                                local _ = l_LastBreath_0.Resources.CCS:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.CCS:GetChildren())];
                                l_wait_19(0.04);
                            end;
                        end);
                        for _, v7961 in pairs(v7298.Expland1:GetChildren()) do
                            if v7961:IsA("BasePart") then
                                local l_FirstChild_15 = l_LastBreath_0.Resources.Expland2:FindFirstChild(v7961.Name);
                                if l_FirstChild_15 then
                                    v955(v7961, {
                                        Time = 0.125, 
                                        EasingStyle = "EntranceExpressive", 
                                        Goal = {
                                            Size = l_FirstChild_15.Size
                                        }
                                    });
                                end;
                            end;
                        end;
                        v955(v7298.Expland1.PrimaryPart, {
                            Time = 0.3, 
                            EasingStyle = "Sine", 
                            Goal = {
                                CFrame = v7298.Expland1.PrimaryPart.CFrame * CFrame.new(0, 45, 0)
                            }
                        });
                        l_wait_19(0.3);
                        l_CC2_1 = l_LastBreath_0.Resources.CC4;
                        v7298.Expland2 = v7298._maid:give(l_LastBreath_0.Resources.Expland3:Clone());
                        v7298.Expland2:SetPrimaryPartCFrame(v7938 * CFrame.new(0, 45, 0));
                        v7298.Expland2.Parent = l_Thrown_23;
                        v7298.Expland2Scale = v7298._maid:give(Instance.new("NumberValue"));
                        v7298._maid:giveTask(v7298.Expland2Scale.Changed:Connect(function() --[[ Line: 18906 ]]
                            -- upvalues: v7298 (ref)
                            v7298.Expland2:ScaleTo(v7298.Expland2Scale.Value);
                        end));
                        spawn(function() --[[ Line: 18910 ]]
                            -- upvalues: v7952 (copy), l_LastBreath_0 (ref), v7938 (copy), l_wait_19 (ref)
                            for _ = 1, 5 do
                                v7952(l_LastBreath_0.Resources.Main.Flips:GetChildren()[math.random(0.5, #l_LastBreath_0.Resources.Main.Flips:GetChildren())], v7938 * CFrame.new(0, 30, 0));
                                l_wait_19(0.1);
                            end;
                        end);
                        v7298.Expland2Scale.Value = 0.3;
                        v955(v7298.Expland2Scale, {
                            Time = 0.1, 
                            EasingStyle = "Sine", 
                            Goal = {
                                Value = 1.3
                            }
                        });
                        v955(v7298.Expland2.PrimaryPart, {
                            Time = 0.2, 
                            EasingStyle = "Sine", 
                            Goal = {
                                CFrame = v7298.Expland2.PrimaryPart.CFrame * CFrame.new(0, 85, 0)
                            }
                        });
                        l_wait_19(0.2);
                        v7298.Expland2:Destroy();
                        v7298.Expland1:Destroy();
                        l_CC2_1 = l_LastBreath_0.Resources.CC4;
                        v7873();
                        l_wait_19(0.75);
                        table.insert(v7784, v7298.Big2);
                        table.insert(v7784, v7939);
                        for _, v7965 in pairs(v7784) do
                            if v7965:IsA("Model") or v7965:IsA("BasePart") then
                                for _, v7967 in pairs(v7965:GetDescendants()) do
                                    if v7967:IsA("ParticleEmitter") then
                                        v7967.Enabled = false;
                                    end;
                                end;
                            end;
                        end;
                    end)();
                end);
                local function v7975(v7968) --[[ Line: 18959 ]] --[[ Name: TP ]]
                    -- upvalues: v7303 (ref), l_char_26 (copy), v7415 (copy), l_wait_19 (copy)
                    if not v7968 then
                        v7968 = 0.9;
                    end;
                    for v7969 = 1, v7303 do
                        local _ = {};
                        for _, v7972 in pairs(l_char_26:GetDescendants()) do
                            if (v7972:IsA("BasePart") or v7972:IsA("Decal")) and v7972.Transparency < 1 then
                                if not v7415[v7972] then
                                    v7415[v7972] = v7972.Transparency;
                                end;
                                v7972.Transparency = 1;
                            end;
                        end;
                        if v7969 < v7303 then
                            l_wait_19(0.05);
                        else
                            l_wait_19(v7968);
                        end;
                        for v7973, v7974 in pairs(v7415) do
                            v7973.Transparency = v7974;
                        end;
                        l_wait_19(0.05);
                    end;
                end;
                local _ = function() --[[ Line: 18995 ]] --[[ Name: Swirls ]]
                    -- upvalues: v7297 (copy), l_char_26 (copy), l_Thrown_23 (copy), v7302 (copy), v7310 (copy), l_wait_19 (copy), l_TweenService_26 (copy)
                    for _ = 1, 2 do
                        task.spawn(function() --[[ Line: 18997 ]]
                            -- upvalues: v7297 (ref), l_char_26 (ref), l_Thrown_23 (ref), v7302 (ref), v7310 (ref), l_wait_19 (ref), l_TweenService_26 (ref)
                            local v7977 = v7297.Swirlnormalfaceorigin:Clone();
                            local _, v7979, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            v7977:PivotTo(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7979, 0) * CFrame.new(0, 0, -10) * CFrame.Angles(math.rad((math.random(-180, 180))), math.rad((math.random(-180, 180))), (math.rad((math.random(-180, 180))))));
                            v7977.Parent = l_Thrown_23;
                            game.Debris:AddItem(v7977, 10);
                            local _ = v7302:NextNumber(3, 6);
                            local _ = v7302:NextNumber(0.0075, 0.015);
                            local v7983 = nil;
                            v7983 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                            local v7984 = v7310[math.random(1, 2)];
                            task.spawn(function() --[[ Line: 19013 ]]
                                -- upvalues: v7984 (copy), v7977 (copy), l_wait_19 (ref)
                                for v7985 = 1, #v7984, 2 do
                                    local v7986 = v7984[v7985];
                                    if v7986 then
                                        v7977.Decal.Texture = v7986;
                                    end;
                                    l_wait_19(0.01);
                                end;
                                v7977:Destroy();
                            end);
                            task.wait(0.1);
                            l_TweenService_26:Create(v7977.Decal, TweenInfo.new(0.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {
                                Color3 = Color3.fromRGB(0, 0, 0)
                            }):Play();
                        end);
                        task.wait(0.05);
                    end;
                end;
                local v7988 = {};
                task.spawn(function() --[[ Line: 19041 ]]
                    -- upvalues: v7297 (copy), l_char_26 (copy), l_Thrown_23 (copy), v7302 (copy), v7309 (copy), l_wait_19 (copy), l_TweenService_26 (copy)
                    local v7989 = v7297.Impact2:Clone();
                    local v7990, v7991, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7989:PivotTo(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v7991, 0) * CFrame.new(0, 0, -10) * CFrame.Angles(-1.5707963267948966, 0, 0));
                    v7989.Parent = l_Thrown_23;
                    game.Debris:AddItem(v7989, 10);
                    local _ = v7302:NextNumber(3, 6);
                    local _ = v7302:NextNumber(0.0075, 0.015);
                    local v7995 = nil;
                    v7995 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                    local l_v7309_1 = v7309;
                    task.spawn(function() --[[ Line: 19056 ]]
                        -- upvalues: l_v7309_1 (copy), v7989 (copy), l_wait_19 (ref)
                        for v7997 = 1, #l_v7309_1, 2 do
                            local v7998 = l_v7309_1[v7997];
                            if v7998 then
                                v7989.Decal.Texture = v7998;
                            end;
                            l_wait_19(0.1);
                        end;
                        v7989:Destroy();
                    end);
                    v7990 = v7989.Mesh;
                    v7990.Scale = v7990.Scale * 3;
                    l_TweenService_26:Create(v7989, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        CFrame = v7989.CFrame * CFrame.new(0, 20, 0)
                    }):Play();
                    l_TweenService_26:Create(v7989.Mesh, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        Scale = Vector3.new(v7989.Mesh.Scale.X * 0.3, v7989.Mesh.Scale.Y * 2, v7989.Mesh.Scale.Z * 0.3)
                    }):Play();
                    task.wait(0.2);
                    l_TweenService_26:Create(v7989.Decal, TweenInfo.new(0.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {
                        Color3 = Color3.fromRGB(0, 0, 0)
                    }):Play();
                end);
                task.spawn(function() --[[ Line: 19076 ]]
                    -- upvalues: v7297 (copy), l_char_26 (copy), l_Thrown_23 (copy), v7302 (copy), v7307 (copy), l_wait_19 (copy), l_TweenService_26 (copy)
                    local v7999 = v7297.FadeRing:Clone();
                    local v8000, v8001, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v7999:PivotTo(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8001, 0) * CFrame.new(0, 0, -10) * CFrame.Angles(0, -1.5707963267948966, 0));
                    v7999.Parent = l_Thrown_23;
                    game.Debris:AddItem(v7999, 10);
                    local _ = v7302:NextNumber(3, 6);
                    local _ = v7302:NextNumber(0.0075, 0.015);
                    local v8005 = nil;
                    v8005 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                    local l_v7307_0 = v7307;
                    task.spawn(function() --[[ Line: 19091 ]]
                        -- upvalues: l_v7307_0 (copy), v7999 (copy), l_wait_19 (ref)
                        for v8007 = 1, #l_v7307_0, 2 do
                            local v8008 = l_v7307_0[v8007];
                            if v8008 then
                                v7999.Decal.Texture = v8008;
                            end;
                            l_wait_19(0.1);
                        end;
                        v7999:Destroy();
                    end);
                    v8000 = v7999.Mesh;
                    v8000.Scale = v8000.Scale * 1;
                    l_TweenService_26:Create(v7999, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        CFrame = v7999.CFrame * CFrame.new(-170, 0, 0)
                    }):Play();
                    l_TweenService_26:Create(v7999.Mesh, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        Scale = Vector3.new(v7999.Mesh.Scale.X * 4, v7999.Mesh.Scale.Y * 4, v7999.Mesh.Scale.Z * 4)
                    }):Play();
                    task.wait(0.5);
                    l_TweenService_26:Create(v7999.Decal, TweenInfo.new(1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {
                        Transparency = 1
                    }):Play();
                end);
                task.spawn(function() --[[ Line: 19113 ]]
                    -- upvalues: v7297 (copy), l_char_26 (copy), l_Thrown_23 (copy), v7302 (copy), v7307 (copy), l_wait_19 (copy), l_TweenService_26 (copy)
                    local v8009 = v7297.FadeRing:Clone();
                    local v8010, v8011, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v8009:PivotTo(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8011, 0) * CFrame.new(0, 0, -10) * CFrame.Angles(0, -1.5707963267948966, 0));
                    v8009.Parent = l_Thrown_23;
                    game.Debris:AddItem(v8009, 10);
                    local _ = v7302:NextNumber(3, 6);
                    local _ = v7302:NextNumber(0.0075, 0.015);
                    local v8015 = nil;
                    v8015 = v7302:NextInteger(1, 2) == 1 and "Slahzz7MRCOOL" or "Slahzz10MRCOOL";
                    local l_v7307_1 = v7307;
                    task.spawn(function() --[[ Line: 19128 ]]
                        -- upvalues: l_v7307_1 (copy), v8009 (copy), l_wait_19 (ref)
                        for v8017 = 1, #l_v7307_1, 2 do
                            local v8018 = l_v7307_1[v8017];
                            if v8018 then
                                v8009.Decal.Texture = v8018;
                            end;
                            l_wait_19(0.02);
                        end;
                        v8009:Destroy();
                    end);
                    v8010 = v8009.Mesh;
                    v8010.Scale = v8010.Scale * 0.5;
                    l_TweenService_26:Create(v8009, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        CFrame = v8009.CFrame * CFrame.new(-170, 0, 0)
                    }):Play();
                    l_TweenService_26:Create(v8009.Mesh, TweenInfo.new(1, Enum.EasingStyle.Sine), {
                        Scale = Vector3.new(v8009.Mesh.Scale.X * 4, v8009.Mesh.Scale.Y * 4, v8009.Mesh.Scale.Z * 4)
                    }):Play();
                    l_TweenService_26:Create(v8009.Decal, TweenInfo.new(1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {
                        Transparency = 1
                    }):Play();
                end);
                (function() --[[ Line: 19149 ]] --[[ Name: More ]]
                    -- upvalues: v1000 (ref), v7297 (copy), v7298 (copy), l_char_26 (copy), v1014 (ref), v989 (ref), l_QuickWeldNew_6 (copy), l_hit_18 (copy), l_wait_19 (copy), v7975 (copy)
                    local l_v1000_14 = v1000;
                    local v8020 = {
                        FX = v7297.Prim, 
                        Maid = v7298._maid
                    };
                    local _, v8022, v8023 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v8020.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8022, 0) * CFrame.new(0, 0, -5) * CFrame.Angles(0, 3.141592653589793, 0);
                    l_v1000_14 = l_v1000_14(v8020);
                    v1014(l_v1000_14);
                    v8020 = v1000;
                    local v8024 = {
                        FX = v7297.ExploTest2, 
                        Maid = v7298._maid
                    };
                    local v8025;
                    v8022, v8023, v8025 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v8024.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8023, 0) * CFrame.new(0, 0, -35) * CFrame.Angles(-1.5707963267948966, 3.141592653589793, 0);
                    v8020 = v8020(v8024);
                    v989({
                        FX = v8020, 
                        Scale = 2
                    });
                    v1014(v8020);
                    v8024 = l_QuickWeldNew_6({
                        FX = v7297.VictimGlint, 
                        P = l_hit_18.Torso, 
                        Maid = v7298._maid
                    });
                    v989({
                        FX = v8024, 
                        Scale = 2
                    });
                    v1014(v8024);
                    task.delay(0.6, function() --[[ Line: 19165 ]]
                        -- upvalues: l_wait_19 (ref), v7975 (ref), v1000 (ref), v7297 (ref), v7298 (ref), l_char_26 (ref), v1014 (ref)
                        task.spawn(function() --[[ Line: 19169 ]]
                            -- upvalues: l_wait_19 (ref), v7975 (ref)
                            l_wait_19(0.2);
                            v7975();
                        end);
                        task.delay(0.5, function() --[[ Line: 19175 ]]
                            -- upvalues: v1000 (ref), v7297 (ref), v7298 (ref), l_char_26 (ref), v1014 (ref)
                            local l_v1000_15 = v1000;
                            local v8027 = {
                                FX = v7297.AirTP, 
                                Maid = v7298._maid
                            };
                            local _, v8029, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            v8027.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8029, 0) * CFrame.new(0, 0, -15);
                            l_v1000_15 = l_v1000_15(v8027);
                            v1014(l_v1000_15);
                        end);
                    end);
                end)();
                local v8031 = v1000({
                    FX = v7297.GroundBeam, 
                    Maid = v7298._maid, 
                    Anchor = l_PrimaryPart_49.CFrame * CFrame.new(7, -l_PrimaryPart_49.Size.Y * 1.1, -30) * CFrame.Angles(1.5707963267948966, 0, 1.5707963267948966)
                });
                for _, v8033 in pairs(v8031:GetDescendants()) do
                    if v8033.Name == "22" then
                        l_TweenService_26:Create(v8033, TweenInfo.new(2, Enum.EasingStyle.Sine), {
                            CFrame = v8033.CFrame * CFrame.new(-50, 0, 0)
                        }):Play();
                    elseif v8033:IsA("Beam") then
                        v8033.TextureSpeed = 1;
                        l_TweenService_26:Create(v8033, TweenInfo.new(2, Enum.EasingStyle.Sine), {
                            TextureSpeed = 0, 
                            Width0 = v8033.Width0 * 1, 
                            Width1 = v8033.Width1 * 1
                        }):Play();
                    end;
                end;
                task.delay(1.4, function() --[[ Line: 19203 ]]
                    -- upvalues: v8031 (copy), v955 (ref)
                    for _, v8035 in pairs(v8031:GetDescendants()) do
                        if v8035:IsA("Beam") then
                            v955(v8035, {
                                Time = 0.6, 
                                EasingStyle = "Sine", 
                                Goal = {
                                    Transparency = NumberSequence.new({
                                        NumberSequenceKeypoint.new(0, 1), 
                                        NumberSequenceKeypoint.new(1, 1)
                                    })
                                }
                            });
                            game.Debris:AddItem(v8035, 0.6);
                        end;
                    end;
                end);
                game.Debris:AddItem(v8031, 5);
                local l_TweenService_27 = game:GetService("TweenService");
                task.spawn(function() --[[ Line: 19217 ]]
                    -- upvalues: v7298 (copy), l_TweenService_27 (copy), v7297 (copy), l_PrimaryPart_49 (copy), v1000 (ref), l_char_26 (copy), v989 (ref), v1014 (ref), v7302 (copy), v7988 (copy), l_wait_19 (copy)
                    local v8037 = v7298._maid:give(Instance.new("NumberValue"));
                    l_TweenService_27:Create(v8037, TweenInfo.new(2, Enum.EasingStyle.Sine), {
                        Value = 1
                    }):Play();
                    v8037.Value = 6;
                    local v8038 = v7297.SLAP:Clone();
                    v8038:ScaleTo(1);
                    local v8039 = {
                        Model = v8038, 
                        EndT = 1, 
                        Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -9) * CFrame.Angles(0, 0, 0), 
                        Info = TweenInfo.new(0.3, Enum.EasingStyle.Sine)
                    };
                    local l_v8039_0 = v8039 --[[ copy: 2 -> 16 ]];
                    task.spawn(function() --[[ Line: 4475 ]]
                        -- upvalues: l_v8039_0 (copy)
                        local l_Model_157 = l_v8039_0.Model;
                        local v8042 = l_v8039_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                        local l_Start_164 = l_Model_157:FindFirstChild("Start");
                        local l_End_161 = l_Model_157:FindFirstChild("End");
                        local l_Stay_156 = l_v8039_0.Stay;
                        local l_Anchor_157 = l_v8039_0.Anchor;
                        local v8047 = l_v8039_0.EndT or 1;
                        local l_Del_156 = l_v8039_0.Del;
                        local l_Skip_156 = l_v8039_0.Skip;
                        if l_Start_164 and l_End_161 then
                            l_Model_157.PrimaryPart = l_Start_164;
                            if not l_Skip_156 then
                                for _, v8051 in pairs(l_Model_157:GetChildren()) do
                                    if v8051:IsA("BasePart") then
                                        v8051.CanCollide = false;
                                        v8051.Anchored = true;
                                    end;
                                end;
                            end;
                            if l_Anchor_157 then
                                l_Model_157:SetPrimaryPartCFrame(l_Anchor_157);
                            end;
                            if l_v8039_0.T then
                                l_Start_164.Transparency = l_v8039_0.T;
                            end;
                            l_End_161.Transparency = 1;
                            l_Model_157.Parent = workspace.Thrown;
                            local l_Decal_316 = l_Start_164:FindFirstChildOfClass("Decal");
                            local l_SpecialMesh_314 = l_Start_164:FindFirstChildOfClass("SpecialMesh");
                            local l_SpecialMesh_315 = l_End_161:FindFirstChildOfClass("SpecialMesh");
                            local l_Decal_317 = l_End_161:FindFirstChildOfClass("Decal");
                            if l_Decal_317 and not l_Skip_156 then
                                l_Decal_317.Transparency = 1;
                            end;
                            local v8056 = nil;
                            if l_Del_156 then
                                game:GetService("TweenService"):Create(l_Start_164, v8042, {
                                    Size = l_End_161.Size, 
                                    CFrame = l_End_161.CFrame
                                }):Play();
                                task.delay(l_Del_156, function() --[[ Line: 4519 ]]
                                    -- upvalues: v8056 (ref), l_Start_164 (copy), v8042 (copy), v8047 (copy), l_Decal_316 (copy), l_SpecialMesh_314 (copy), l_SpecialMesh_315 (copy)
                                    v8056 = game:GetService("TweenService"):Create(l_Start_164, v8042, {
                                        Transparency = v8047
                                    });
                                    v8056:Play();
                                    if l_Decal_316 then
                                        for _, v8058 in pairs(l_Start_164:GetChildren()) do
                                            if v8058:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v8058, v8042, {
                                                    Transparency = v8047
                                                }):Play();
                                            end;
                                        end;
                                    end;
                                    if l_SpecialMesh_314 then
                                        v8056 = game:GetService("TweenService"):Create(l_SpecialMesh_314, v8042, {
                                            Scale = l_SpecialMesh_315.Scale
                                        }):Play();
                                    end;
                                end);
                            else
                                if l_SpecialMesh_314 then
                                    game:GetService("TweenService"):Create(l_SpecialMesh_314, v8042, {
                                        Scale = l_SpecialMesh_315.Scale
                                    }):Play();
                                end;
                                if l_Decal_316 then
                                    for _, v8060 in pairs(l_Start_164:GetChildren()) do
                                        if v8060:IsA("Decal") then
                                            game:GetService("TweenService"):Create(v8060, v8042, {
                                                Transparency = v8047
                                            }):Play();
                                        end;
                                    end;
                                    v8056 = game:GetService("TweenService"):Create(l_Start_164, v8042, {
                                        Size = l_End_161.Size, 
                                        CFrame = l_End_161.CFrame
                                    });
                                    v8056:Play();
                                else
                                    v8056 = game:GetService("TweenService"):Create(l_Start_164, v8042, {
                                        Size = l_End_161.Size, 
                                        Transparency = v8047, 
                                        CFrame = l_End_161.CFrame
                                    });
                                    v8056:Play();
                                end;
                            end;
                            if not l_Stay_156 then
                                if l_Del_156 then
                                    task.wait(l_Del_156 + 0.1);
                                end;
                                v8056.Completed:Connect(function() --[[ Line: 4562 ]]
                                    -- upvalues: l_Model_157 (copy)
                                    l_Model_157:Destroy();
                                end);
                            end;
                            return;
                        else
                            warn("NO START OR END");
                            return;
                        end;
                    end);
                    v8039 = v1000;
                    local v8061 = {
                        FX = v7297["Fire Hit"], 
                        Maid = v7298._maid
                    };
                    local v8062, v8063, v8064 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v8061.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8063, 0) * CFrame.new(5, 0, 0);
                    v8039 = v8039(v8061);
                    v989({
                        FX = v8039, 
                        Scale = 2
                    });
                    v1014(v8039);
                    v8061 = v1000;
                    local v8065 = {
                        FX = v7297.hmmmm, 
                        Maid = v7298._maid
                    };
                    local v8066;
                    v8063, v8064, v8066 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                    v8065.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8064, 0) * CFrame.new(5, 0, 11);
                    v8061 = v8061(v8065);
                    v8061:ScaleTo(2);
                    v989({
                        FX = v8061, 
                        Scale = 1
                    });
                    v1014(v8061);
                    v8065 = v7297.Shockwave:Clone();
                    v8065:ScaleTo(0.7);
                    local v8067 = {
                        Model = v8065, 
                        EndT = 1, 
                        Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, 0, -9) * CFrame.Angles(1.5707963267948966, 0, 0), 
                        Info = TweenInfo.new(0.7, Enum.EasingStyle.Sine)
                    };
                    local l_v8067_0 = v8067 --[[ copy: 5 -> 17 ]];
                    task.spawn(function() --[[ Line: 4475 ]]
                        -- upvalues: l_v8067_0 (copy)
                        local l_Model_158 = l_v8067_0.Model;
                        local v8070 = l_v8067_0.Info or TweenInfo.new(1, Enum.EasingStyle.Sine);
                        local l_Start_165 = l_Model_158:FindFirstChild("Start");
                        local l_End_162 = l_Model_158:FindFirstChild("End");
                        local l_Stay_157 = l_v8067_0.Stay;
                        local l_Anchor_158 = l_v8067_0.Anchor;
                        local v8075 = l_v8067_0.EndT or 1;
                        local l_Del_157 = l_v8067_0.Del;
                        local l_Skip_157 = l_v8067_0.Skip;
                        if l_Start_165 and l_End_162 then
                            l_Model_158.PrimaryPart = l_Start_165;
                            if not l_Skip_157 then
                                for _, v8079 in pairs(l_Model_158:GetChildren()) do
                                    if v8079:IsA("BasePart") then
                                        v8079.CanCollide = false;
                                        v8079.Anchored = true;
                                    end;
                                end;
                            end;
                            if l_Anchor_158 then
                                l_Model_158:SetPrimaryPartCFrame(l_Anchor_158);
                            end;
                            if l_v8067_0.T then
                                l_Start_165.Transparency = l_v8067_0.T;
                            end;
                            l_End_162.Transparency = 1;
                            l_Model_158.Parent = workspace.Thrown;
                            local l_Decal_318 = l_Start_165:FindFirstChildOfClass("Decal");
                            local l_SpecialMesh_316 = l_Start_165:FindFirstChildOfClass("SpecialMesh");
                            local l_SpecialMesh_317 = l_End_162:FindFirstChildOfClass("SpecialMesh");
                            local l_Decal_319 = l_End_162:FindFirstChildOfClass("Decal");
                            if l_Decal_319 and not l_Skip_157 then
                                l_Decal_319.Transparency = 1;
                            end;
                            local v8084 = nil;
                            if l_Del_157 then
                                game:GetService("TweenService"):Create(l_Start_165, v8070, {
                                    Size = l_End_162.Size, 
                                    CFrame = l_End_162.CFrame
                                }):Play();
                                task.delay(l_Del_157, function() --[[ Line: 4519 ]]
                                    -- upvalues: v8084 (ref), l_Start_165 (copy), v8070 (copy), v8075 (copy), l_Decal_318 (copy), l_SpecialMesh_316 (copy), l_SpecialMesh_317 (copy)
                                    v8084 = game:GetService("TweenService"):Create(l_Start_165, v8070, {
                                        Transparency = v8075
                                    });
                                    v8084:Play();
                                    if l_Decal_318 then
                                        for _, v8086 in pairs(l_Start_165:GetChildren()) do
                                            if v8086:IsA("Decal") then
                                                game:GetService("TweenService"):Create(v8086, v8070, {
                                                    Transparency = v8075
                                                }):Play();
                                            end;
                                        end;
                                    end;
                                    if l_SpecialMesh_316 then
                                        v8084 = game:GetService("TweenService"):Create(l_SpecialMesh_316, v8070, {
                                            Scale = l_SpecialMesh_317.Scale
                                        }):Play();
                                    end;
                                end);
                            else
                                if l_SpecialMesh_316 then
                                    game:GetService("TweenService"):Create(l_SpecialMesh_316, v8070, {
                                        Scale = l_SpecialMesh_317.Scale
                                    }):Play();
                                end;
                                if l_Decal_318 then
                                    for _, v8088 in pairs(l_Start_165:GetChildren()) do
                                        if v8088:IsA("Decal") then
                                            game:GetService("TweenService"):Create(v8088, v8070, {
                                                Transparency = v8075
                                            }):Play();
                                        end;
                                    end;
                                    v8084 = game:GetService("TweenService"):Create(l_Start_165, v8070, {
                                        Size = l_End_162.Size, 
                                        CFrame = l_End_162.CFrame
                                    });
                                    v8084:Play();
                                else
                                    v8084 = game:GetService("TweenService"):Create(l_Start_165, v8070, {
                                        Size = l_End_162.Size, 
                                        Transparency = v8075, 
                                        CFrame = l_End_162.CFrame
                                    });
                                    v8084:Play();
                                end;
                            end;
                            if not l_Stay_157 then
                                if l_Del_157 then
                                    task.wait(l_Del_157 + 0.1);
                                end;
                                v8084.Completed:Connect(function() --[[ Line: 4562 ]]
                                    -- upvalues: l_Model_158 (copy)
                                    l_Model_158:Destroy();
                                end);
                            end;
                            return;
                        else
                            warn("NO START OR END");
                            return;
                        end;
                    end);
                    v8067 = tick();
                    v8062 = 0;
                    while tick() - v8067 < 4 do
                        v8062 = v8062 + 1;
                        if tick() - v8067 < 2.3 then
                            if v8062 % 5 == 0 then
                                v8063 = v1000;
                                v8064 = {
                                    FX = v7297.Spoop3, 
                                    Maid = v7298._maid
                                };
                                local v8089, v8090, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                                v8064.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8090, 0) * CFrame.new(0, 0, -15) * CFrame.Angles(0, -1.5707963267948966, 0) * CFrame.Angles(math.rad((math.random(0, 360))), 0, 0);
                                v8063 = v8063(v8064);
                                v8063:ScaleTo(v8063:GetScale() * v7302:NextNumber(0.9, 1.1) * 1.3);
                                game.Debris:AddItem(v8063, 1.5);
                                table.insert(v7988, v8063);
                                for _, v8093 in pairs(v8063:GetDescendants()) do
                                    if v8093:IsA("Decal") then
                                        v8089 = v8093.Transparency;
                                        v8093.Transparency = 1;
                                        l_TweenService_27:Create(v8093, TweenInfo.new(0.025, Enum.EasingStyle.Sine), {
                                            Transparency = v8089
                                        }):Play();
                                        task.delay(0.075, function() --[[ Line: 19267 ]]
                                            -- upvalues: l_TweenService_27 (ref), v8093 (copy)
                                            l_TweenService_27:Create(v8093, TweenInfo.new(0.15, Enum.EasingStyle.Sine), {
                                                Transparency = 1
                                            }):Play();
                                        end);
                                    elseif v8093:IsA("SpecialMesh") then
                                        l_TweenService_27:Create(v8093, TweenInfo.new(0.5, Enum.EasingStyle.Sine), {
                                            Scale = v8093.Scale * 2
                                        }):Play();
                                        game.Debris:AddItem(v8063, 0.5);
                                    end;
                                end;
                            end;
                            if tick() - v8067 < 0.5 and v8062 % 5 == 0 then
                                task.spawn(function() --[[ Line: 19304 ]]

                                end);
                            end;
                        end;
                        l_wait_19(0.01);
                        for _, v8095 in pairs(v7988) do
                            if v8095:IsA("Model") then
                                if v8095.Name == "Spoop" then
                                    v8095:PivotTo(v8095:GetPivot() * CFrame.Angles(0.3141592653589793, 0, 0));
                                elseif v8095.Name == "Spoop2" then
                                    v8095:PivotTo(v8095:GetPivot() * CFrame.Angles(0.15707963267948966, 0, 0));
                                elseif v8095.Name == "Tornado" then
                                    v8095:PivotTo(v8095:GetPivot() * CFrame.Angles(0, 0.3665191429188092, 0));
                                else
                                    v8095:PivotTo(v8095:GetPivot() * CFrame.Angles(-0.15707963267948966, 0, 0));
                                end;
                            end;
                        end;
                    end;
                end);
                return;
            elseif v1090.run then
                local v8096 = v7298._maid:give(v7297.FirstColdTest:Clone());
                local v8097 = v7297.Start:Clone();
                v8097:PivotTo(l_PrimaryPart_49.CFrame);
                v8097.Parent = workspace.Thrown;
                for _, v8099 in pairs(v8097:GetDescendants()) do
                    if v8099:IsA("ParticleEmitter") then
                        task.delay(v8099:GetAttribute("EmitDelay") or 0, function() --[[ Line: 19355 ]]
                            -- upvalues: v8099 (copy)
                            v8099:Emit(v8099:GetAttribute("EmitCount"));
                        end);
                    end;
                end;
                game:GetService("Debris"):AddItem(v8097, 8);
                task.delay(0.5, function() --[[ Line: 19357 ]]
                    -- upvalues: v8097 (copy)
                    for _, v8101 in v8097:GetDescendants() do
                        if v8101:IsA("ParticleEmitter") then
                            v8101.Enabled = false;
                        end;
                    end;
                end);
                v7388(v8097);
                task.spawn(v7380, v8097.Meshes, true, true);
                local v8102 = 0;
                local v8103 = 0;
                local v8104 = {};
                local v8105 = l_QuickWeldNew_6({
                    Maid = v7298._maid, 
                    FX = l_LastBreath_0.Running, 
                    P = l_char_26.Torso
                });
                local l_l_QuickWeldNew_6_0 = l_QuickWeldNew_6;
                local v8107 = {
                    Maid = v7298._maid, 
                    FX = l_LastBreath_0.AHAH
                };
                local v8108, v8109, _ = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                v8107.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8109, 0);
                l_l_QuickWeldNew_6_0 = l_l_QuickWeldNew_6_0(v8107);
                v1021({
                    FX = l_l_QuickWeldNew_6_0, 
                    On = true
                });
                v989({
                    FX = l_l_QuickWeldNew_6_0, 
                    Scale = 0.3
                });
                v8107 = v1000({
                    FX = l_LastBreath_0.Floor, 
                    Maid = v7298._maid, 
                    Anchor = l_PrimaryPart_49.CFrame * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.5, 0)
                });
                local l_PointLight_4 = Instance.new("PointLight");
                l_PointLight_4.Color = Color3.new(1, 0.337255, 0.0745098);
                l_PointLight_4.Range = 15;
                l_PointLight_4.Brightness = 5;
                l_PointLight_4.Parent = l_char_26.Torso;
                v8108 = function() --[[ Line: 19393 ]]
                    -- upvalues: v1021 (ref), v8105 (copy), v8107 (copy), l_l_QuickWeldNew_6_0 (copy), v10 (ref), l_PointLight_4 (copy)
                    v1021({
                        FX = v8105, 
                        On = false
                    });
                    v1021({
                        FX = v8107, 
                        On = false
                    });
                    v1021({
                        FX = l_l_QuickWeldNew_6_0, 
                        On = false
                    });
                    v10:Create(l_PointLight_4, TweenInfo.new(1.3, Enum.EasingStyle.Sine), {
                        Brightness = 0
                    }):Play();
                end;
                task.delay(v1090.runtime, v8108);
                task.spawn(function() --[[ Line: 19401 ]]
                    -- upvalues: v8102 (ref), v8103 (ref), v1090 (copy), v8107 (copy), l_PrimaryPart_49 (copy), l_l_QuickWeldNew_6_0 (copy), l_char_26 (copy), v1000 (ref), v7297 (copy), v7298 (copy), v7302 (copy), v8104 (copy), v10 (ref), l_wait_19 (copy), v8096 (copy), v7292 (ref), v8108 (copy)
                    local v8112 = tick();
                    local v8113 = true;
                    while tick() - v8112 < 4 do
                        v8102 = v8102 + 10;
                        v8103 = v8103 + 1;
                        if tick() - v8112 < v1090.runtime + 0.1 and v8113 then
                            v8107:SetPrimaryPartCFrame(l_PrimaryPart_49.CFrame * CFrame.new(0, -l_PrimaryPart_49.Size.Y * 1.4, 0));
                            local l_l_l_QuickWeldNew_6_0_0 = l_l_QuickWeldNew_6_0;
                            local _, v8116, v8117 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            l_l_l_QuickWeldNew_6_0_0:SetPrimaryPartCFrame(CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8116, 0) * CFrame.new(0, 0, -1));
                            if v8103 % 4.5 == 0 then
                                l_l_l_QuickWeldNew_6_0_0 = v1000;
                                local v8118 = {
                                    FX = v7297.Tornado, 
                                    Maid = v7298._maid
                                };
                                local v8119, v8120;
                                v8117, v8119, v8120 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                                v8118.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8119, 0) * CFrame.new(0, 0, -5) * CFrame.Angles(1.5707963267948966, 0, 0) * CFrame.Angles(0, math.rad((math.random(0, 360))), 0);
                                l_l_l_QuickWeldNew_6_0_0 = l_l_l_QuickWeldNew_6_0_0(v8118);
                                l_l_l_QuickWeldNew_6_0_0:ScaleTo(l_l_l_QuickWeldNew_6_0_0:GetScale() * v7302:NextNumber(0.9, 1.1) * 1.3);
                                table.insert(v8104, l_l_l_QuickWeldNew_6_0_0);
                                for _, v8122 in pairs(l_l_l_QuickWeldNew_6_0_0:GetDescendants()) do
                                    if v8122:IsA("SpecialMesh") then
                                        v10:Create(v8122, TweenInfo.new(0.3, Enum.EasingStyle.Sine), {
                                            Scale = v8122.Scale * 2
                                        }):Play();
                                    elseif v8122:IsA("Decal") then

                                    end;
                                end;
                                local l_l_l_l_QuickWeldNew_6_0_0_0 = l_l_l_QuickWeldNew_6_0_0 --[[ copy: 2 -> 13 ]];
                                task.spawn(function() --[[ Line: 19430 ]]
                                    -- upvalues: l_l_l_l_QuickWeldNew_6_0_0_0 (copy), l_wait_19 (ref)
                                    for v8124 = 1, #l_l_l_l_QuickWeldNew_6_0_0_0.PrimaryPart.Textures:GetChildren(), 2 do
                                        local l_FirstChild_16 = l_l_l_l_QuickWeldNew_6_0_0_0.PrimaryPart.Textures:FindFirstChild("e" .. v8124);
                                        if l_FirstChild_16 then
                                            l_l_l_l_QuickWeldNew_6_0_0_0.PrimaryPart.Decal.Texture = l_FirstChild_16.Texture;
                                            l_wait_19(0.01);
                                        end;
                                    end;
                                    l_l_l_l_QuickWeldNew_6_0_0_0:Destroy();
                                end);
                                v8118 = v1000;
                                local v8126 = {
                                    FX = v7297.Spoop, 
                                    Maid = v7298._maid
                                };
                                local v8127;
                                v8119, v8120, v8127 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                                v8126.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8120, 0) * CFrame.new(0, 0, -5) * CFrame.Angles(0, -1.5707963267948966, 0) * CFrame.Angles(math.rad((math.random(0, 360))), 0, 0);
                                v8118 = v8118(v8126);
                                v8118:ScaleTo(v8118:GetScale() * v7302:NextNumber(0.9, 1.1) * 1.3);
                                game.Debris:AddItem(v8118, 1.5);
                                table.insert(v8104, v8118);
                                for _, v8129 in pairs(v8118:GetDescendants()) do
                                    if v8129:IsA("Decal") then
                                        v8119 = v8129.Transparency;
                                        v8129.Transparency = 1;
                                        v10:Create(v8129, TweenInfo.new(0.06, Enum.EasingStyle.Sine), {
                                            Transparency = v8119
                                        }):Play();
                                        task.delay(0.063, function() --[[ Line: 19454 ]]
                                            -- upvalues: v10 (ref), v8129 (copy)
                                            v10:Create(v8129, TweenInfo.new(0.09, Enum.EasingStyle.Sine), {
                                                Transparency = 1
                                            }):Play();
                                        end);
                                    elseif v8129:IsA("SpecialMesh") then
                                        v10:Create(v8129, TweenInfo.new(0.15, Enum.EasingStyle.Sine), {
                                            Scale = v8129.Scale * 2
                                        }):Play();
                                    end;
                                end;
                                v8126 = v1000;
                                local v8130 = {
                                    FX = v7297.Spoop2, 
                                    Maid = v7298._maid
                                };
                                local v8131;
                                v8120, v8127, v8131 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                                v8130.Anchor = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8127, 0) * CFrame.new(0, 0, -5) * CFrame.Angles(0, -1.5707963267948966, 0) * CFrame.Angles(math.rad((math.random(0, 360))), 0, 0);
                                v8126 = v8126(v8130);
                                v8126:ScaleTo(v8126:GetScale() * v7302:NextNumber(0.9, 1.1) * 1.3);
                                game.Debris:AddItem(v8126, 1.5);
                                table.insert(v8104, v8126);
                                for _, v8133 in pairs(v8126:GetDescendants()) do
                                    if v8133:IsA("Decal") then
                                        v8120 = v8133.Transparency;
                                        v8133.Transparency = 1;
                                        v10:Create(v8133, TweenInfo.new(0.06, Enum.EasingStyle.Sine), {
                                            Transparency = v8120
                                        }):Play();
                                        task.delay(0.045, function() --[[ Line: 19475 ]]
                                            -- upvalues: v10 (ref), v8133 (copy)
                                            v10:Create(v8133, TweenInfo.new(0.18, Enum.EasingStyle.Sine), {
                                                Transparency = 1
                                            }):Play();
                                        end);
                                    elseif v8133:IsA("SpecialMesh") then
                                        v10:Create(v8133, TweenInfo.new(0.3, Enum.EasingStyle.Sine), {
                                            Scale = v8133.Scale * 2
                                        }):Play();
                                    end;
                                end;
                            end;
                            l_l_l_QuickWeldNew_6_0_0 = v8096.Bigger;
                            local v8134;
                            v8116, v8117, v8134 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            l_l_l_QuickWeldNew_6_0_0.CFrame = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8117, 0) * CFrame.new(0, 0, -2) * CFrame.Angles(0, 1.5707963267948966, 0) * CFrame.Angles(math.rad(v8102), 0, 0);
                            l_l_l_QuickWeldNew_6_0_0 = v8096.Smaller;
                            v8116, v8117, v8134 = l_char_26.HumanoidRootPart.CFrame:ToOrientation();
                            l_l_l_QuickWeldNew_6_0_0.CFrame = CFrame.new(l_char_26.Torso.Position) * CFrame.Angles(0, v8117, 0) * CFrame.new(0, 0, -2) * CFrame.Angles(0, 1.5707963267948966, 0) * CFrame.Angles(math.rad(v8102 / 2), 0, 0);
                        elseif v8113 and false then
                            v8113 = false;
                            v8108();
                        end;
                        for _, v8136 in pairs(v8104) do
                            if v8136:IsA("Model") then
                                if v8136.Name == "Spoop" then
                                    v8136:PivotTo(v8136:GetPivot() * CFrame.Angles(0.20943951023931956, 0, 0));
                                elseif v8136.Name == "Spoop2" then
                                    v8136:PivotTo(v8136:GetPivot() * CFrame.Angles(0.10471975511965978, 0, 0));
                                elseif v8136.Name == "Tornado" then
                                    v8136:PivotTo(v8136:GetPivot() * CFrame.Angles(0, 0.24434609527920614, 0));
                                else
                                    v8136:PivotTo(v8136:GetPivot() * CFrame.Angles(0.10471975511965978, 0, 0));
                                end;
                            end;
                        end;
                        l_wait_19(0.01);
                    end;
                    game.Debris:AddItem(v8096, 0.3);
                end);
                return;
            else
                local l_bind_2 = v1090.bind;
                if not l_bind_2 then
                    return;
                else
                    local v8138 = {};
                    local l_Attachment_2 = Instance.new("Attachment");
                    local v8140 = 0;
                    l_Attachment_2.Parent = l_char_26.Torso;
                    l_Attachment_2.Name = "Heart";
                    l_Attachment_2.Position = Vector3.new(-0.5669999718666077, 0.46299999952316284, -0.527999997138977, 0);
                    local v8141 = false;
                    local l_v8138_0 = v8138 --[[ copy: 35 -> 1338 ]];
                    local l_l_Attachment_2_0 = l_Attachment_2 --[[ copy: 36 -> 1339 ]];
                    local l_v7294_1 = v7294 --[[ copy: 8 -> 1340 ]];
                    do
                        local l_v7292_0, l_v8140_0, l_v8141_0 = v7292, v8140, v8141;
                        local function v8154() --[[ Line: 19568 ]]
                            -- upvalues: l_v8141_0 (ref), l_v8138_0 (copy), l_l_Attachment_2_0 (copy), l_v7294_1 (copy)
                            if not l_v8141_0 then
                                l_v8141_0 = true;
                                for _, v8149 in pairs(l_v8138_0) do
                                    v8149:Pause();
                                    v8149:Destroy();
                                end;
                                if l_l_Attachment_2_0 and l_l_Attachment_2_0.Parent then
                                    l_l_Attachment_2_0:Destroy("");
                                end;
                                shared.SetCore(true, 3);
                                local l_CurrentCamera_10 = workspace.CurrentCamera;
                                spawn(function() --[[ Line: 19585 ]]
                                    -- upvalues: l_CurrentCamera_10 (copy)
                                    local v8151 = tick();
                                    while true do
                                        if task.wait() then
                                            if tick() - v8151 >= 0.25 then
                                                return;
                                            else
                                                l_CurrentCamera_10.FieldOfView = 70;
                                            end;
                                        else
                                            return;
                                        end;
                                    end;
                                end);
                                l_CurrentCamera_10.CameraType = Enum.CameraType.Custom;
                                for _, v8153 in pairs(l_v7294_1) do
                                    if typeof(v8153) == "Instance" then
                                        v8153:Destroy("");
                                    end;
                                end;
                            end;
                        end;
                        local l_l_bind_2_0 = l_bind_2 --[[ copy: 34 -> 1341 ]];
                        local l_v8154_0 = v8154 --[[ copy: 39 -> 1342 ]];
                        local function _() --[[ Line: 19609 ]]
                            -- upvalues: l_l_bind_2_0 (copy), l_v8154_0 (copy)
                            if not l_l_bind_2_0 or not l_l_bind_2_0.Parent then
                                l_v8154_0();
                                return true;
                            else
                                return;
                            end;
                        end;
                        local v8158 = l_LastBreath_0.BB.BillboardGui:Clone();
                        table.insert(v7294, v8158);
                        v8158.Parent = l_Attachment_2;
                        local l_NumberValue_5 = Instance.new("NumberValue");
                        table.insert(v7294, l_NumberValue_5);
                        l_NumberValue_5.Value = 1;
                        local l_l_NumberValue_5_0 = l_NumberValue_5 --[[ copy: 42 -> 1343 ]];
                        table.insert(v7294, l_NumberValue_5.Changed:Connect(function() --[[ Line: 19631 ]]
                            -- upvalues: l_v7292_0 (ref), l_l_NumberValue_5_0 (copy)
                            l_v7292_0:AdjustSpeed(l_l_NumberValue_5_0.Value);
                        end));
                        table.insert(v7294, l_v7292_0:GetMarkerReachedSignal("slow"):Connect(function() --[[ Line: 19638 ]]
                            -- upvalues: v10 (ref), l_l_NumberValue_5_0 (copy), l_v8138_0 (copy)
                            local v8161 = nil;
                            v8161 = v10:Create(l_l_NumberValue_5_0, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                Value = 0.1
                            });
                            v8161:Play();
                            table.insert(l_v8138_0, v8161);
                        end));
                        task.delay(0.6, function() --[[ Line: 19647 ]]
                            -- upvalues: v10 (ref), l_l_NumberValue_5_0 (copy), l_v8138_0 (copy)
                            local v8162 = nil;
                            v8162 = v10:Create(l_l_NumberValue_5_0, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                Value = 0.01
                            });
                            v8162:Play();
                            table.insert(l_v8138_0, v8162);
                        end);
                        local l_ColorCorrectionEffect_1 = Instance.new("ColorCorrectionEffect");
                        table.insert(v7294, l_ColorCorrectionEffect_1);
                        game.Debris:AddItem(l_ColorCorrectionEffect_1, 6);
                        if l_LocalPlayer_0.Character == l_char_26 then
                            l_ColorCorrectionEffect_1.Name = "LastBreathGray";
                            l_ColorCorrectionEffect_1.Parent = game.Lighting;
                        end;
                        v8158.Enabled = true;
                        v8158.CanvasGroup.ImageLabel.Size = UDim2.new(0, 0, 0, 0);
                        for _, v8165 in pairs(l_IDs_0:GetChildren()) do
                            local v8166 = v8158.CanvasGroup.ImageLabel:Clone();
                            table.insert(v7294, v8166);
                            v8166.Name = v8165.Name;
                            v8166.Image = v8165.Texture;
                            v8166.Size = UDim2.new(0.001, 0, 0.001, 0);
                            v8166.Parent = v8158.CanvasGroup;
                            game.Debris:AddItem(v8166, 10);
                        end;
                        local l_l_char_26_1 = l_char_26 --[[ copy: 4 -> 1344 ]];
                        local l_l_CurrentCamera_8_0 = l_CurrentCamera_8 --[[ copy: 9 -> 1345 ]];
                        local l_v7294_2 = v7294 --[[ copy: 8 -> 1346 ]];
                        local l_l_PrimaryPart_49_0 = l_PrimaryPart_49 --[[ copy: 7 -> 1347 ]];
                        local l_l_Attachment_2_1 = l_Attachment_2 --[[ copy: 36 -> 1348 ]];
                        local l_l_ColorCorrectionEffect_1_0 = l_ColorCorrectionEffect_1 --[[ copy: 43 -> 1349 ]];
                        local l_l_bind_2_1 = l_bind_2 --[[ copy: 34 -> 1350 ]];
                        local l_v8154_1 = v8154 --[[ copy: 39 -> 1351 ]];
                        local l_v8158_0 = v8158 --[[ copy: 41 -> 1352 ]];
                        local l_v8138_1 = v8138 --[[ copy: 35 -> 1353 ]];
                        local l_v7297_0 = v7297 --[[ copy: 11 -> 1354 ]];
                        local l_l_NumberValue_5_1 = l_NumberValue_5 --[[ copy: 42 -> 1355 ]];
                        task.delay(0, function() --[[ Line: 19675 ]]
                            -- upvalues: l_LocalPlayer_0 (ref), l_l_char_26_1 (copy), l_l_CurrentCamera_8_0 (copy), l_v7294_2 (copy), l_l_PrimaryPart_49_0 (copy), l_l_Attachment_2_1 (copy), l_l_ColorCorrectionEffect_1_0 (copy), l_l_bind_2_1 (copy), l_v8154_1 (copy), l_v8140_0 (ref), v10 (ref), l_v8158_0 (copy), l_v8138_1 (copy), l_v7297_0 (copy), l_l_NumberValue_5_1 (copy)
                            local _ = CFrame.new(3.15377903, 2.39878464, -4.55020094, -0.905365765, -0.06841214, 0.419085562, -0, 0.986936748, 0.161108986, -0.424632668, 0.145862564, -0.893538713);
                            if l_LocalPlayer_0.Character == l_l_char_26_1 then
                                l_l_CurrentCamera_8_0.CameraType = Enum.CameraType.Scriptable;
                            end;
                            local l_CFrameValue_4 = Instance.new("CFrameValue");
                            l_CFrameValue_4.Value = l_l_CurrentCamera_8_0.CFrame;
                            table.insert(l_v7294_2, l_CFrameValue_4);
                            local _ = l_CFrameValue_4.Value;
                            local v8182 = {
                                l_CFrameValue_4.Value.Position, 
                                l_l_PrimaryPart_49_0.CFrame * CFrame.new(20, 0, 0), 
                                l_l_Attachment_2_1.WorldPosition + l_l_PrimaryPart_49_0.CFrame.lookVector * 5
                            };
                            local v8183 = nil;
                            if l_LocalPlayer_0.Character == l_l_char_26_1 then
                                require(game.ReplicatedStorage.Resources.LegacyReplication.Bezier).new(unpack(v8182)):CreateCFrameTween(l_CFrameValue_4, {
                                    "Value"
                                }, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)):Play();
                                v8183 = game:GetService("RunService").RenderStepped:Connect(function(v8184) --[[ Line: 19695 ]]
                                    -- upvalues: l_l_char_26_1 (ref), l_l_CurrentCamera_8_0 (ref), l_l_ColorCorrectionEffect_1_0 (ref), v8183 (ref), l_l_bind_2_1 (ref), l_v8154_1 (ref), l_CFrameValue_4 (copy), l_l_Attachment_2_1 (ref), l_v8140_0 (ref)
                                    if not l_l_char_26_1.Parent then
                                        l_l_CurrentCamera_8_0.FieldOfView = 70;
                                        l_l_ColorCorrectionEffect_1_0:Destroy();
                                        return v8183:Disconnect();
                                    elseif not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                        l_v8154_1();
                                        return v8183:Disconnect();
                                    else
                                        local v8185 = math.sin(tick() * 3.141592653589793 / 2) * 0.08;
                                        if l_l_CurrentCamera_8_0.CameraType ~= Enum.CameraType.Scriptable then
                                            l_l_CurrentCamera_8_0.CameraType = Enum.CameraType.Scriptable;
                                        end;
                                        local l_Value_4 = l_CFrameValue_4.Value;
                                        l_Value_4 = CFrame.new(l_Value_4.Position, l_l_Attachment_2_1.WorldPosition + Vector3.new(0, l_v8140_0, 0));
                                        l_l_CurrentCamera_8_0.CFrame = l_l_CurrentCamera_8_0.CFrame:lerp(l_Value_4 * CFrame.new(v8185, -v8185 * 0.5, v8185 / 2.5) * CFrame.Angles(math.rad(-v8185 * 7), math.rad(v8185 * 6), (math.rad(-v8185 * 3))), 1 - 1.0E-6 ^ v8184);
                                        return;
                                    end;
                                end);
                                table.insert(l_v7294_2, v8183);
                                v10:Create(l_l_ColorCorrectionEffect_1_0, TweenInfo.new(1.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                    Saturation = -1
                                }):Play();
                            end;
                            task.delay(0.25, function() --[[ Line: 19720 ]]
                                -- upvalues: l_l_bind_2_1 (ref), l_v8154_1 (ref), v10 (ref), l_v8158_0 (ref), l_v8138_1 (ref)
                                local v8187;
                                if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                    l_v8154_1();
                                    v8187 = true;
                                else
                                    v8187 = nil;
                                end;
                                if v8187 then
                                    return;
                                else
                                    v8187 = nil;
                                    v8187 = v10:Create(l_v8158_0.CanvasGroup, TweenInfo.new(1.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                        GroupTransparency = 0
                                    });
                                    v8187:Play();
                                    table.insert(l_v8138_1, v8187);
                                    return;
                                end;
                            end);
                            task.wait(1.35);
                            local v8188;
                            if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                l_v8154_1();
                                v8188 = true;
                            else
                                v8188 = nil;
                            end;
                            if v8188 then
                                return;
                            else
                                if l_LocalPlayer_0.Character == l_l_char_26_1 then
                                    if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                        l_v8154_1();
                                        v8188 = true;
                                    else
                                        v8188 = nil;
                                    end;
                                    if v8188 then
                                        return;
                                    else
                                        v8188 = nil;
                                        v8188 = v10:Create(l_l_CurrentCamera_8_0, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                            FieldOfView = 25
                                        });
                                        v8188:Play();
                                        table.insert(l_v8138_1, v8188);
                                    end;
                                end;
                                task.wait(1.5);
                                if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                    l_v8154_1();
                                    v8188 = true;
                                else
                                    v8188 = nil;
                                end;
                                if v8188 then
                                    return;
                                else
                                    if l_LocalPlayer_0.Character == l_l_char_26_1 then
                                        v10:Create(l_l_CurrentCamera_8_0, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                            FieldOfView = 70
                                        }):Play();
                                        v10:Create(l_l_ColorCorrectionEffect_1_0, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                            Saturation = 0
                                        }):Play();
                                    end;
                                    if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                        l_v8154_1();
                                        v8188 = true;
                                    else
                                        v8188 = nil;
                                    end;
                                    if v8188 then
                                        return;
                                    else
                                        v8188 = l_v7297_0.StartF:Clone();
                                        v8188.Parent = workspace.Thrown;
                                        v8188:PivotTo(l_l_PrimaryPart_49_0.CFrame);
                                        local l_Head_2 = v8188:FindFirstChild("Head");
                                        task.spawn(function() --[[ Line: 19769 ]]
                                            -- upvalues: l_l_char_26_1 (ref), l_Head_2 (copy), l_l_Attachment_2_1 (ref)
                                            while task.wait() and l_l_char_26_1.Parent and l_Head_2.Parent do
                                                l_Head_2.Position = l_l_Attachment_2_1.WorldPosition;
                                            end;
                                        end);
                                        task.delay(0.4, function() --[[ Line: 19770 ]]
                                            -- upvalues: l_l_bind_2_1 (ref), l_v8154_1 (ref), v10 (ref), l_v8158_0 (ref), l_l_NumberValue_5_1 (ref), l_LocalPlayer_0 (ref), l_l_char_26_1 (ref), v8183 (ref), l_l_CurrentCamera_8_0 (ref), l_l_PrimaryPart_49_0 (ref), l_v7294_2 (ref)
                                            local v8190;
                                            if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                                l_v8154_1();
                                                v8190 = true;
                                            else
                                                v8190 = nil;
                                            end;
                                            if v8190 then
                                                return;
                                            else
                                                v10:Create(l_v8158_0, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                                    Size = UDim2.new(0, 0, 0, 0)
                                                }):Play();
                                                v10:Create(l_l_NumberValue_5_1, TweenInfo.new(0.75, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                                    Value = 1
                                                }):Play();
                                                task.delay(0.75, function() --[[ Line: 19780 ]]
                                                    -- upvalues: l_l_bind_2_1 (ref), l_v8154_1 (ref), l_LocalPlayer_0 (ref), l_l_char_26_1 (ref), v8183 (ref), l_l_CurrentCamera_8_0 (ref), l_l_PrimaryPart_49_0 (ref), l_v7294_2 (ref), v10 (ref), l_l_NumberValue_5_1 (ref)
                                                    local v8191;
                                                    if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                                        l_v8154_1();
                                                        v8191 = true;
                                                    else
                                                        v8191 = nil;
                                                    end;
                                                    if v8191 then
                                                        return;
                                                    else
                                                        if l_LocalPlayer_0.Character == l_l_char_26_1 then
                                                            v8183:Disconnect();
                                                            l_l_CurrentCamera_8_0.CameraType = Enum.CameraType.Custom;
                                                            l_l_CurrentCamera_8_0.FieldOfView = 70;
                                                            v8191 = l_l_PrimaryPart_49_0.CFrame * CFrame.new(0.121032715, 4.415802, 15.8661346, 0.999988973, -7.91892409E-4, 0.00464361906, -2.88034645E-8, 0.985767841, 0.168112651, -0.00471064448, -0.168110803, 0.985756874);
                                                            l_l_CurrentCamera_8_0.CFrame = CFrame.new(v8191.Position, (l_l_PrimaryPart_49_0.CFrame + l_l_PrimaryPart_49_0.CFrame.lookVector * 10).Position + Vector3.new(0, 2, 0, 0));
                                                            l_l_CurrentCamera_8_0.CameraType = Enum.CameraType.Custom;
                                                            local v8192 = tick();
                                                            local v8193 = nil;
                                                            do
                                                                local l_v8193_0 = v8193;
                                                                l_v8193_0 = game:GetService("RunService").Heartbeat:Connect(function() --[[ Line: 19794 ]]
                                                                    -- upvalues: v8192 (copy), l_v8193_0 (ref), l_l_bind_2_1 (ref), l_v8154_1 (ref), l_l_CurrentCamera_8_0 (ref), v8191 (copy), l_l_PrimaryPart_49_0 (ref)
                                                                    if tick() - v8192 > 0.15 then
                                                                        return l_v8193_0:Disconnect();
                                                                    else
                                                                        local v8195;
                                                                        if not l_l_bind_2_1 or not l_l_bind_2_1.Parent then
                                                                            l_v8154_1();
                                                                            v8195 = true;
                                                                        else
                                                                            v8195 = nil;
                                                                        end;
                                                                        if v8195 then
                                                                            return;
                                                                        else
                                                                            l_l_CurrentCamera_8_0.CFrame = CFrame.new(v8191.Position, (l_l_PrimaryPart_49_0.CFrame + l_l_PrimaryPart_49_0.CFrame.lookVector * 10).Position);
                                                                            return;
                                                                        end;
                                                                    end;
                                                                end);
                                                                table.insert(l_v7294_2, l_v8193_0);
                                                            end;
                                                        end;
                                                        v10:Create(l_l_NumberValue_5_1, TweenInfo.new(0.75, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                                                            Value = 1
                                                        }):Play();
                                                        return;
                                                    end;
                                                end);
                                                return;
                                            end;
                                        end);
                                        for _, v8197 in pairs(v8188:GetDescendants()) do
                                            if v8197:IsA("ParticleEmitter") then
                                                task.delay(v8197:GetAttribute("EmitDelay") or 0, function() --[[ Line: 19812 ]]
                                                    -- upvalues: v8197 (copy)
                                                    v8197:Emit(v8197:GetAttribute("EmitCount"));
                                                end);
                                            end;
                                        end;
                                        return;
                                    end;
                                end;
                            end;
                        end);
                        local l_l_IDs_0_0 = l_IDs_0 --[[ copy: 18 -> 1356 ]];
                        task.delay(1, function() --[[ Line: 19815 ]]
                            -- upvalues: l_l_IDs_0_0 (copy), l_v8158_0 (copy), v1090 (copy), l_l_Attachment_2_1 (copy), l_v8140_0 (ref)
                            local v8199 = nil;
                            for v8200 = 1, #l_l_IDs_0_0:GetChildren() do
                                local v8201 = l_v8158_0.CanvasGroup[v8200];
                                if not v1090.bind or not v1090.bind.Parent then
                                    if l_l_Attachment_2_1 and l_l_Attachment_2_1.Parent then
                                        l_l_Attachment_2_1:Destroy("");
                                    end;
                                    return;
                                else
                                    if v8200 == 38 then
                                        local l_l_l_Attachment_2_1_0 = l_l_Attachment_2_1;
                                        l_l_l_Attachment_2_1_0.Position = l_l_l_Attachment_2_1_0.Position - Vector3.new(0, 0.04800000041723251, 0, 0);
                                        l_v8140_0 = 0.048;
                                    end;
                                    if v8199 then
                                        v8199.Size = UDim2.new(0, 0, 0, 0);
                                    end;
                                    v8201.Size = UDim2.new(1, 0, 1, 0);
                                    v8199 = v8201;
                                    task.wait(0.04);
                                end;
                            end;
                        end);
                    end;
                end;
            end;

    -- ==========================================
    -- GROUND CRATER (support system)
    -- ==========================================
    elseif v1091 == "Ground Crater" then
                if typeof(v1090.start) == "Instance" then
                    if v1090.start:IsA("Attachment") then
                        v1090.start = v1090.start.WorldPosition;
                    else
                        v1090.start = v1090.start.Position;
                    end;
                end;
                local v23481 = Random.new(v1090.Seed);
                local l_v102_4 = v102;
                local l_l_v102_4_0 = l_v102_4 --[[ copy: 5 -> 595 ]];
                local l_v23481_0 = v23481 --[[ copy: 4 -> 596 ]];
                local function _(v23485, v23486) --[[ Line: 48303 ]]
                    -- upvalues: l_l_v102_4_0 (copy), l_v23481_0 (copy)
                    return l_l_v102_4_0(v23485, v23486, nil, l_v23481_0);
                end;
                local v23488, v23489, _, v23491 = v717({
                    orig = v1090.start, 
                    dir = v1090["end"]
                });
                if v1090.Sound then
                    v1090.Sound.CFrame = CFrame.new(v23489);
                    shared.sfx(v1090.Sound):Play();
                end;
                if v23488 then
                    local v23492 = v327(CFrame.new(v23489).UpVector, v23491, (Vector3.new(0, 1, 0, 0)));
                    local l_Attachment_18 = Instance.new("Attachment");
                    l_Attachment_18.Parent = v23488;
                    if not v23488.Anchored or v1090.smoked then
                        l_Attachment_18.Parent = workspace.Terrain;
                    end;
                    l_Attachment_18.WorldCFrame = CFrame.new(v23489) * v23492;
                    if not v1090.nosmoke then
                        local v23494 = 8;
                        if l_LocalPlayer_0:GetAttribute("S_FastMode") then
                            v23494 = v23494 / 2;
                        end;
                        local v23495 = -0.001;
                        local v23496 = 0.001;
                        local v23497 = v23481 or v95;
                        if not v23496 and v23495 then
                            v23496 = v23495;
                            v23495 = 1;
                        end;
                        if not v23496 and not v23495 then
                            v23495 = 0;
                            v23496 = 1;
                        end;
                        local v23498 = v23497:NextNumber(v23495, v23496);
                        v23495 = game.ReplicatedStorage.Resources.Smoke:Clone();
                        v23495.Parent = l_Attachment_18;
                        v23495.ZOffset = v23495.ZOffset + v23498;
                        v23495.Color = ColorSequence.new(v23488 == workspace.Terrain and Color3.fromRGB(227, 206, 157) or v23488.Color);
                        if v1090.stronger then
                            shared.resizeparticle(v23495, v1090.stronger.size1 or 4.2);
                            v23495.Speed = NumberRange.new(v23495.Speed.Min * (4.5 - (v1090.stronger.minus or 0)), v23495.Speed.Max * (4.5 - (v1090.stronger.minus or 0)));
                            v23495.Lifetime = NumberRange.new(v23495.Lifetime.Min * 2, v23495.Lifetime.Max * 2);
                            v23495.RotSpeed = NumberRange.new(v23495.RotSpeed.Min * 0.35, v23495.RotSpeed.Max * 0.35);
                        else
                            shared.resizeparticle(v23495, 1.25);
                            if v1090.CloserCircle then
                                v23495.Speed = NumberRange.new(25, 50);
                                shared.resizeparticle(v23495, 0.7);
                                v23494 = v23494 / 1.5;
                            end;
                            if v1090.Closerr then
                                shared.resizeparticle(v23495, 0.55);
                                v23495.Speed = NumberRange.new(7, 25);
                            end;
                        end;
                        if v23488 ~= workspace.Terrain and not v1090.NoCircleSmoke then
                            v23495:Emit(v23494);
                        end;
                        v23496 = 8;
                        if l_LocalPlayer_0:GetAttribute("S_FastMode") then
                            v23496 = v23496 / 2;
                        end;
                        v23497 = game.ReplicatedStorage.Resources.UpSmoke:Clone();
                        v23497.Parent = l_Attachment_18;
                        v23497.ZOffset = v23497.ZOffset + v23498;
                        v23497.Color = ColorSequence.new(v23488 == workspace.Terrain and Color3.fromRGB(227, 206, 157) or v23488.Color);
                        if v1090.stronger then
                            shared.resizeparticle(v23497, v1090.stronger.size2 or 4.2);
                            v23497.Speed = v1090.speed1 or NumberRange.new(v23497.Speed.Min * (4.5 - (v1090.stronger.minus or 0)), v23497.Speed.Max * (4.5 - (v1090.stronger.minus or 0)));
                            v23497.Lifetime = NumberRange.new(v23497.Lifetime.Min * 2, v23497.Lifetime.Max * 2);
                            v23497.RotSpeed = NumberRange.new(v23497.RotSpeed.Min * 0.35, v23497.RotSpeed.Max * 0.35);
                        else
                            shared.resizeparticle(v23497, 1.25);
                            if v1090.Closerr then
                                shared.resizeparticle(v23497, 0.3);
                                v23496 = v23496 / 1.5;
                                v23497.Speed = NumberRange.new(20, 35);
                            end;
                        end;
                        shared.resizeparticle(v23497, 1.25);
                        if v23488 ~= workspace.Terrain and not v1090.NoUpSmoke then
                            v23497:Emit(v23496);
                        end;
                    end;
                    game:GetService("Debris"):AddItem(l_Attachment_18, 9);
                    local v23499 = false;
                    if v23488.Material == Enum.Material.Sand or v23488.Material == Enum.Material.Snow then
                        v23499 = true;
                    end;
                    if not v1090.NoCrater2 then
                        v471({
                            ground = v23488, 
                            cframe = CFrame.new(v23489), 
                            amount = (v1090.amount or v23499 and 7 or 10) / 1.25, 
                            normal = v23491, 
                            sand = v23499 and true or nil, 
                            add = {
                                sounds = true
                            }, 
                            sizemult = v1090.sizemult, 
                            new = {
                                5 * (v1090.size or 1), 
                                5.5 * (v1090.size or 1)
                            }, 
                            nosound = v1090.nosound, 
                            angle = v1090.angle, 
                            dontcollide = v1090.dontcollide, 
                            anglecfr = v1090.anglecfr, 
                            nodebris = v1090.nodebris, 
                            notiles = v1090.notiles, 
                            Seed = v1090.Seed, 
                            dispersefast = v1090.dispersefast
                        });
                    end;
                    return;
                end;

    -- ==========================================
    -- CAMSHAKE (support system)
    -- ==========================================
    elseif v1091 == "Camshake" then
                if v1090.OnScreenOnly and v1090.RealPart then
                    local l_Position_96 = v1090.RealPart.PrimaryPart.Position;
                    local _, v23210 = workspace:FindFirstChildOfClass("Camera"):WorldToScreenPoint(l_Position_96);
                    if not v23210 then
                        return;
                    end;
                end;
                if v1090.Last and typeof(v1090.Last) ~= "number" then
                    v1090.Last = nil;
                end;
                if v1090.Last then
                    task.spawn(function() --[[ Line: 47432 ]]
                        -- upvalues: v1090 (copy)
                        for _ = 1, v1090.Last / 0.03 do
                            local l_addshake_0 = shared.addshake;
                            if l_addshake_0 then
                                l_addshake_0(v1090.Intensity);
                            end;
                            local l_v1090_0 = v1090;
                            l_v1090_0.Intensity = l_v1090_0.Intensity * 0.9;
                            task.wait(0.03);
                        end;
                    end);
                    return;
                else
                    shared.addshake(v1090.Intensity, v1090.IgnoreReduced);
                end;

    end -- closes if/elseif v1091 chain
end -- closes v12

-- ===========================================================================
-- ENTRY POINT
-- ===========================================================================
local player    = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

v12({
    Effect = "Last Breath New",
    char   = character,
    id     = 0,
    second = false,
    hit    = character,
})
