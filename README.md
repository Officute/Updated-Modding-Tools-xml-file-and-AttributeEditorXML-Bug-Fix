# CoH2 Mod Tools Patch (Bug fixes + New features + Agent MCP)

\---

## Part 1 — For humans

### What this is

A drop-in patch for the **Company of Heroes 2 Tools** that fixes several Attribute Editor / ModBuilder
bugs, adds safer delete tools, and lets an AI agent (Claude, Codex, OpenClaw, etc.) work inside your
open Attribute Editor — **only after you allow it**. It also ships updated base-game attribute data
(the official tools stopped at the April 22nd 2020 hotfix).

### Install

1. Close the Attribute Editor and ModBuilder.
2. Copy everything in this folder into your tools folder, overwriting when asked:
`...\\Steam\\steamapps\\common\\Company of Heroes 2 Tools\\`
3. Open your mod in ModBuilder and open Attributes as usual.

You should now see an **Agent** menu at the right end of the Attribute Editor's menu bar.

To uninstall, use Steam → Company of Heroes 2 Tools → Properties → Installed Files →
**Verify integrity of tool files**; Steam restores the original executables. Deleting `AEAgent.exe`
alone turns off the Agent, the new editor features and the base-game text fix; the scroll and copy
fixes stay (they are in the editor itself).

### What changed

**Bug fixes**

* **Scroll jumps to top** when clicking a row in a long right-hand panel (wrong scroll clamp in
`VarViewBase.updateScrollBars()`; vertical and horizontal fixed).
* **Crash when copying** (Copy, Paste, Copy Value, right-click menu, ModBuilder's Copy Id) while another
program holds the clipboard — emulators (MuMu, BlueStacks), clipboard managers, Win+V history,
remote desktop. Clipboard writes no longer flush, so copying is also instant. Copied text still
survives closing the tool normally.
* **Base-game text is shown** even when the editor is started directly (not only from ModBuilder).

**Safer delete (right-click on an instance, or the Delete key)**

The original Delete leaves every reference to the deleted instance as *null*, **clears your whole Undo
history**, and cannot be undone. The new **Delete...** first moves those
references to another instance, then deletes — and both steps can be undone with Ctrl+Z.

* Target **Original**: the base-game instance of the same name (i.e. "un-override").
* Target **None**: references become *null* (like the original Delete, but undoable).
* Target **Another instance**: pick from a filterable list (space-separated words, any order).
* **Redirect References...** (above *Find All References*) moves references without deleting.
* The Delete key on selected instances opens the same dialog. The original Delete is hidden from
the instance menu, since **None** does the same thing undoably.

Updated Attribute Editor stats
Before overwriting
Before you use these files to overwrite the mod tools files, you have to delete these:

`abilities\west_german\modal_ability\artillery\artillery_barrage_ability\walking_stuka_rocket_barrage_creeping_napalm_mp`

`sbps\races\brits`

`sbps\races\soviets`

---
Known vanilla bugs
Instance	Bug
`abilities\british\modal_ability\raid_section_general_mine_mp`	Lost pinned warning text

`abilities\british\modal_ability\artillery\artillery_barrage_ability\smoke_barrage_tommy_flare_mp`	Hides one of the Tommies' firearms even though the ability doesn't use a marker grenade anymore

`abilities\german\modal_ability\commander_ability\assault_officer_draw_fire`	`1073766205` in `ui_unit_modifier_action` `help_text`

`abilities\soviet\modal_ability\infantry_medkits_partisans_mp`	`ui_unit_modifier_action` lost `screen_name` and `help_text`

`abilities\soviet\modal_ability\commander_ability\air_dropped_supplies_soviet_mp`	`kicker_message_action` message and "Disabled during a blizzard" in requirements are gone

`sbps\races\british\soldiers\tommy_squad\tommy_squad_raid_mp`	Reinforce `screen_name` text lost

`sbps\races\british\team_weapons\m1_81mm_mortar_squad\m1_81mm_mortar_british_squad_commando_mp`	Smoke barrage doesn't increase at vet 2

`sbps\races\german\soldiers\officer_squad\assault_officer_squad_luftwaffe_variant_mp`	Reinforce `screen_name` text lost

`ebps\races\aef\soldiers\vehicle_crew_troop\vehicle_crew_troop_repair_station_mp`	Has `population_ext`

`ebps\races\german\soldiers\pioneer\repair_pioneer`	Has a cost, so it gives free experience to enemy units

`weapon\soviet\ballistic_weapon\tank_gun\t34_85_zis-s-53_85mm_t34_mp`	Not affected by Armored Vehicle Detection

### Letting an AI agent help

**Connecting the agent** (needs [Python 3](https://www.python.org/) installed, nothing else):

* **Claude Code, started in the tools folder**: the included `.mcp.json` is picked up automatically.
Claude Code asks once whether to enable the `aeagent` server; say yes.
* **Claude Code, started anywhere else** (e.g. your mod folder): run this once, with your own tools path:

```
  claude mcp add --scope user aeagent -- python -I "C:\\Program Files (x86)\\Steam\\steamapps\\common\\Company of Heroes 2 Tools\\AEAgent\_clients\\mcp\_server.py"
  ```

  No `claude` command in your terminal (common if you only use the desktop app)? Just ask Claude to
run this line for you, then start a new session so the tools show up.

* **Other agents** (Codex, OpenClaw, ...): add an MCP server with command `python` and arguments
`-I <tools folder>\\AEAgent\_clients\\mcp\_server.py` in that agent's own MCP settings, or just point
the agent at this readme; Part 2 is written for it.

The editor never lets an agent in by itself:

1. The agent asks to connect. The editor shows **"Agent access request"** with the agent's name.

   * **No** blocks it (and further requests for 30 seconds).
   * **Yes** gives that agent access until you disconnect or close the editor.
2. While connected, the title bar ends with **\[Agent connected]** and **Agent** menu shows who and since when.
3. **Agent → Disconnect Agent** cuts access immediately.

What an agent can and cannot do:

* It can read data, search, compare instances, find references, open instances in the editor, and
edit values / lists / clone / override / redirect / delete — you see every change live.
* Every change is a normal, named Undo entry (Ctrl+Z works). If one of its requests fails halfway,
everything from that request is rolled back automatically.
* It **never saves**. Saving is always your decision.
* It only listens on your own computer (127.0.0.1); nothing is reachable from the network.

### Files

|File|Purpose|
|-|-|
|`AttributeEditorXML.exe`|Patched editor (bug fixes + loads AEAgent if present)|
|`ModBuilder.exe`|Patched ModBuilder (clipboard crash fix)|
|`AEAgent.exe`|Agent server, Agent menu, startup fixes. Can also be run directly: drag a `.mod` onto it|
|`AEAgent.Bridge.dll`|Editing features and the new right-click / Delete-key behaviour|
|`AEAgent\_clients\\`|Optional helpers for agents (see Part 2); not needed by the editor|
|`assets\\data\\attributes\\`|Updated base-game attribute data (the official tools were not updated after the April 22nd 2020 hotfix). Read `\_readme\_for\_update\_attribute\_editor\_stats.md` first|
|`.mcp.json`|Lets Claude Code started in the tools folder find the agent tools; not needed by the editor|
|`\_readme\_CoH2\_Mod\_Tools\_Patch.md`|This file|

\---

## Part 2 — For AI agents

Read this before touching the user's editor.

### Rules

* **Never save** and never ask the editor to save. The user saves.
* **Ask before connecting** — tell the user an "Agent access request" will pop up in the editor and that
they should click Yes. If they click No, stop and ask; do not retry in a loop (30 s cooldown applies).
* Prefer **one named undo entry per logical step** (default). Use `atomic: true` only when the user wants
a change as one undo step.
* Use `delete` / `redirect` instead of describing the editor's own Delete: the built-in one nulls
references and wipes the undo history.
* Several editors may be open (one per mod). If it's unclear which mod the user means, ask.
* Report what you changed (instance + path + old → new). The user verifies in the UI.

### CoH2 data primer

**Overlays.** `attributes` is the base game (read-only). The mod (e.g. `VeLas`) is writable. An instance
can exist in both: the mod's copy *overrides* the original under the same name. Tools prefer the mod copy.
To change something that only exists in the base game, `override` it into the mod first.

**Templates** (first segment of an instance name; `find` with `template=`):

|Template|What it is|
|-|-|
|`ebps`|Entity blueprints: one soldier, vehicle, building, projectile, territory point...|
|`sbps`|Squad blueprints: what the player builds/commands; lists its entities in the loadout|
|`weapon`|Weapons: damage, range, accuracy, timings, penetration|
|`upgrade`|Upgrades: cost, requirements, actions applied when researched|
|`abilities`|Abilities: activation, range, cost, recharge, actions|
|`slot\_item`|Items picked up / equipped by squads (e.g. weapon drops)|
|`ai\_\*`, `commander`, `commander\_ability`, `aura`, `critical`, `modifier\_tooltip`, ...|Less common|

Names mirror folders: `ebps\\races\\<faction>\\<category>\\...`, factions `aef` (USF), `british` (UKF),
`german` (Ostheer), `soviet`, `west\_german` (OKW).

**Extensions.** `ebps` / `sbps` keep almost everything under `extensions`; each child is named after its
type, so paths read naturally (`extensions/health\_ext/hitpoints`).

|Where|Typical fields|
|-|-|
|ebps `extensions/health\_ext`|`hitpoints`, `armor`, `rear\_armor`, `target\_size`, `death\_actions`|
|ebps `extensions/cost\_ext/time\_cost`|`time\_seconds`; `cost/manpower`, `munition`, `fuel`, `popcap`|
|ebps `extensions/moving\_ext`|`speed\_max`, `acceleration`, `rotation\_rate`|
|ebps `extensions/sight\_ext`|`sight\_package`, `detect\_camouflage`|
|ebps `extensions/population\_ext`|`personnel\_pop`, `vehicle\_pop`|
|ebps `extensions/combat\_ext/hardpoints/hardpoint/weapon\_table/weapon\[i]/weapon`|which weapon the entity carries|
|ebps `extensions/construction\_ext`|buildings: placement rules (`must\_be\_on`, `must\_be\_in\_territory`...), `on\_construction\_actions`|
|ebps `extensions/upgrade\_ext/standard\_upgrades`|upgrades available on this entity|
|sbps `extensions/squad\_loadout\_ext/unit\_list/loadout\_data`|`num` and `type` (the ebps) of squad members|
|sbps `extensions/squad\_upgrade\_ext/upgrades`, `squad\_ability\_ext`, `squad\_reinforce\_ext`|squad upgrades, abilities, reinforcement|
|weapon `weapon\_bag/damage`|`min`, `max`, `post\_damage\_actions`|
|weapon `weapon\_bag/range` · `accuracy` · `penetration`|`min`/`max` · `near`/`mid`/`far`|
|weapon `weapon\_bag/aim`, `burst`, `cooldown`, `reload`|timings (often groups with `min`/`max`)|
|upgrade `upgrade\_bag`|`time\_cost`, `requirements`, `actions`, `ui\_info`, `owner\_type`|
|abilities `ability\_bag`|`activation`, `range`, `recharge\_time`, `cost`, `action\_list`, `requirements`|

These are starting points, not a schema — always `ae\_get` / `GET /instance` to see what is really there.

**Infantry prices are spread over several places.** A squad's build price is the sum of its members'
ebps `cost\_ext` (e.g. 5 × 56 manpower), not a field on the squad. The base game then patches the side
effects separately:

* The XP an enemy earns for a kill also follows that cost, so some soldiers carry an
`entity\_veterency\_experience\_modifier` (modifier\_id `xp\_discount`) to give less.
* Reinforcing one member costs its ebps cost × the squad's
`squad\_reinforce\_ext/time\_cost\_percentage/cost\_percentage` (often 0.5: riflemen 56 → 28).
On top of that, `upgrade\\army\_items\\infantry\_discount\_mp` (an army item every player has) lowers it
for some unit types with `squad\_cost\_manpower\_modifier`.

So before changing an infantry price, check all of them (cost\_ext, cost\_percentage, xp\_discount, infantry\_discount\_mp)
and tell the user what each change affects: build price, reinforce price, or XP given. A cleaner layout
some modders prefer: set the ebps cost to the intended reinforce price, drop `xp\_discount` and the
reinforce discount, and bring only the build price back with a `cost\_manpower\_modifier` (ebps,
`apply\_to\_entity\_type`) in `infantry\_discount\_mp`. Offer it; don't
restructure prices unless asked.

**How the numbers play out in game** (condensed from the community reference *COH2数据汇总 - 基础设定篇*,
coh2吧数据\&翻译组, data as of 2023-02; vanilla defaults, so check the real values before relying on them):

* *Hit chance* = weapon accuracy × target's `target\_size`. Accuracy, penetration and AOE are given at the
near/mid/far distances and interpolate linearly between them (flat inside near and beyond far).
A miss can still hit something on the projectile's path, or land inside the scatter area
(angle scatter + distance scatter, capped by max scatter); weapons without accuracy (mortars) only scatter.
* *Penetration*: chance = penetration / armor; a penetration does full damage, a bounce does none.
Below 3 % it always bounces (armor above \~34 is immune to rifle fire).
* *Shot cycle*: aim → wind-up → fire (burst) → wind-down → cooldown, with a reload every N cooldowns
(reload frequency = magazine). DPS ≈ accuracy × damage × burst duration × rate of fire × reload frequency
÷ (aim + wind-up/down + burst duration × reload frequency + cooldown × (reload frequency − 1) + reload).
* *Infantry defaults*: 80 HP, armor 1, target size 1, 2 weapon slots, 1 pop per model; speed tiers
6 / 5.4 / 3.6 (default) / 2 / 1.5.
* *Cover* vs small arms (received accuracy / damage / suppression): heavy 0.5 / 0.5 / 0.1; light
0.5 / – / 0.5; negative 1.25 / 1.25 / 1.5; garrison 0.55 / 0.5 / none. Small arms ignore non-garrison
cover within 10 m.
* *Suppression*: suppressed at 0.2 (recovers below 0.15): speed −2 tiers, cooldown/reload ×4, accuracy ×0.25.
Pinned at 0.6 (recovers below 0.5): can't move, fire or capture. Base recovery 0.008/s, ×50 out of combat.
* *Veterancy*: XP from damage scales with the target's *value* and the % of HP dealt; the attacker gets
75 %, the target 25 %. Infantry AT weapons get only 1/3. Each vet level usually needs 2× the previous
and raises the unit's value (XP given) by 20 %.
* *Economy*: manpower income 300/min minus upkeep 1.5 per pop (floor 100/min). Costs keep decimals
internally and display rounded down, so reinforcing several squads may not add up exactly.
Command points: 1 CP per 1000 command XP, earned from unit value (manpower 1 : fuel 5).

**Value kinds** (`kind` in dumps): `InstanceFloat`/`InstanceInt`/`InstanceBool`/`InstanceString` plain
values; `InstanceEnum` (pick a valid name); `InstanceLocString` (a number = text ID; dumps show the text);
`InstanceInstanceRef` (points to another instance — this is what `references` follows); `InstanceTemplateRef`
(a typed slot such as an action or requirement; its value is the template, its children are that template's
fields); `InstanceList` (add with `list\_add` / `list\_copy`); `InstanceGroup` (fixed sub-fields);
`InstanceUniqueID` (`id` fields: generated, never copy them by hand); `pbgid` (instance id: never edit).

**Recipes**

* *Who uses this weapon / upgrade / ability?* → `references` on it.
* *Change a base-game unit in the mod* → `override` it, then `set`.
* *New variant of something* → `clone` (new name), edit the copy, then point users at it with `set` or
`redirect`.
* *Make A behave like B in one area* → `diff` A and B with `path=`, then `set` / `list\_copy` the differences.
* *Add an action / requirement* → `list\_options` on the list to see allowed types, `list\_add` with the type,
then `set` the fields of the new item (use the `path` it returns). Or `list\_copy` a working one from
another instance.
* *Remove something that is referenced* → `delete` with `to` (or `redirect` first); never leave nulls.

### Discovery

Each running editor writes `%LOCALAPPDATA%\\AEAgent\\instances\\<pid>.json`:

```json
{"pid": 84564, "port": 47821, "mod": "C:\\\\...\\\\VeLas\\\\VeLas.xml", "started": "2026-10-12 17:23:00"}
```

Ports are 47821–47840 on `127.0.0.1`. Stale files may exist; confirm with `GET /hello`.

### Access

|Request|Auth|Result|
|-|-|-|
|`GET /hello`|none|`{app, pid, mod, locked, connected}`|
|`POST /connect {"client": "<your name>"}`|none|Blocks until the user answers (≤ 5 min). `200 {"token": "..."}`, `403` denied/cooldown, `409` another request pending|
|anything else|header `X-AEAgent-Token: <token>`|`401 {"locked": true}` without a valid token|

Tokens last until the user disconnects or closes the editor; on `401`, call `/connect` again (once).

### Endpoints (all JSON; GET args as query string, POST args as JSON body)

|Endpoint|Purpose|
|-|-|
|`GET /help`|Live endpoint list (authoritative)|
|`GET /status`|Overlays (base game `attributes` is read-only, mods writable), dirty flags, undo/redo stacks, active document|
|`GET /selection?depth=`|What the user has selected in the editor|
|`GET /find?q=\&template=\&limit=`|Search instance names (substring); template e.g. `ebps`, `sbps`, `weapon`, `upgrade`, `abilities`|
|`GET /instance?name=\&path=\&depth=\&overlay=`|Dump an instance (mod copy preferred) or a sub-path. Each node carries its `path`|
|`GET /diff?a=\&b=\&path=`|Compare two instances; no a/b = the two the user selected|
|`GET /references?name=`|Every reference to that exact instance (mod copy and base-game original are different targets)|
|`GET /list/options?name=\&path=`|What may be added to a list (item names, allowed values such as action/requirement templates)|
|`POST /goto {name}`|Open an instance in the editor UI|
|`POST /batch {ops, name?, atomic?, label?}`|Apply edits (below). Ops without `name` inherit it|
|`POST /reload`|Reload `AEAgent.Bridge.dll` after rebuilding it|

Single-op shortcuts also exist: `/set`, `/comment`, `/list/add`, `/list/copy`, `/list/remove`,
`/list/move`, `/clone`, `/override`, `/redirect`, `/delete`.

**Names and paths.** Instance names use `\\` (or `/`), e.g.
`ebps/races/aef/buildings/observation\_post/observation\_post\_aef\_mp`. Paths are child names joined by `/`;
when siblings share a name, add `\[i]`: `extensions/action\_apply\_ext/actions/requirement\_action\[1]/...`.
New list items are named after their template (adding `action\\add\_aura` creates `.../add\_aura`); use the
`path` returned by the op rather than guessing.

### Batch ops

|op|Fields|Notes|
|-|-|-|
|`set`|`name, path, value`|Numbers, strings, bools, enums, locstring IDs (number), file paths. `template\_reference`: value = template path, switches the type. Instance refs: name (mod copy preferred); unknown names are rejected, never written as null|
|`comment`|`name, path?, comment`||
|`list\_add`|`name, path, item?, value?, index?`|`path` = the list. Returns the new item's path|
|`list\_copy`|`name, path, from, fromPath, fromOverlay?, index?`|Copy an item and its subtree from another instance; unique ids are regenerated|
|`list\_remove`|`name, path`|`path` = the item|
|`list\_move`|`name, path, index`||
|`clone`|`name, newName, overlay?, description?, cloneLocStrings?`|Into the first writable mod by default|
|`override`|`name`|Copy a base-game instance into the mod under the same name (keeps PBGID, fixes the mod's references)|
|`redirect`|`name, to`|`to` = instance name, `original` (same-name base-game instance) or `null`|
|`delete`|`name, to?`|Redirects references first (default `original`), then deletes; refuses if any reference can't be moved|

If any op fails, every change made by that request is undone and removed from the redo stack; the error
says which op failed.

### Example

```text
POST /batch
{"name": "ebps/races/aef/buildings/observation\_post/observation\_post\_fuel\_aef\_mp",
 "ops": \[
   {"op": "set", "path": "extensions/ui\_ext/hotkey\_name", "value": "F"},
   {"op": "list\_copy", "path": "extensions/health\_ext/death\_actions",
    "from": "ebps/races/aef/buildings/observation\_post/observation\_post\_munition\_aef\_mp",
    "fromPath": "extensions/health\_ext/death\_actions/timed\_action"}
 ]}
→ {"results": \[...], "undoSteps": 2, "undoText": "Undo Agent: Copy timed\_action from ..."}
```

### Clients in `AEAgent\_clients\\` (optional, Python 3, no dependencies)

* `ae.py` — CLI and module: discovery, auto-connect on 401, token cache in
`%LOCALAPPDATA%\\AEAgent\\tokens\\<pid>.token`.
`python ae.py instances` · `python ae.py --mod VeLas find q=observation\_post` ·
`python ae.py batch "{\\"ops\\": \[...]}"`
* `mcp\_server.py` — MCP (stdio) server with tools `ae\_connect`, `ae\_instances`, `ae\_status`, `ae\_selection`,
`ae\_find`, `ae\_get`, `ae\_diff`, `ae\_references`, `ae\_list\_options`, `ae\_goto`, `ae\_edit`, `ae\_reload`.
The tools folder ships a `.mcp.json` with a relative path (works when the agent's working directory is
the tools folder). From anywhere else, use the absolute path, e.g.:

```json
{"mcpServers": {"aeagent": {"command": "python", "args": \["-I", "<tools folder>\\\\AEAgent\_clients\\\\mcp\_server.py"]}}}
```

The server finds running editors through `%LOCALAPPDATA%\\AEAgent\\instances`, so its own location
doesn't matter.

