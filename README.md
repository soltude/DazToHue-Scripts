# DazToHue-Scripts

DAZ Studio scripts that automate building a full **Range of Motion (ROM)** on a
Genesis 9 figure for the [DazToHue](https://www.artstation.com/marketplace/p/BLM5K/daztohue)
(DTH) workflow — ready to export to Houdini / Unreal Engine.

One wrapper script applies, in order:

1. the base **JCM (+ FAC mouth)** ROM from a DTH `.duf` pose preset,
2. optional **Golden Palace**, **Dicktator** *(experimental)* and **Physics** ROM blocks,
3. your own **full-body / expression / corrective morphs** from a FrameData JSON,

then sets every key to linear interpolation so the export is predictable.

## How it works

A small per-character wrapper (see `DthWorkflowExample.dsa` and
`DthWorkflowElectraG9.dsa`) includes `DthWorkflow.dsa`, fills in an `options`
object, and calls `ApplyDTHWorkflow(options)`:

```
DthWorkflowElectraG9.dsa            (per-character wrapper — sets options)
  └── DthWorkflow.dsa               (ApplyDTHWorkflow + ApplyDefaultROMs/GP9/DK9/Physics)
        ├── DthUtils.dsa            (timeline / morph / JSON / node helpers)
        ├── DthOptions.dsa          (default options + getRomPaths)
        └── ScanKeyFrames.dsa       (post-op keyframe scanning, debug only)
```

The framework does **not** contain the ROM data — the JCM/FAC/GP/DK/Physics
keyframes live in mrpdean's licensed `.duf` presets, which the script loads from
your Daz library. The scripts orchestrate that loading and stamp your custom
morphs on top.

## Setup

1. In `DthOptions.dsa`, set **`DTH_POSES_PATH`** to your `DazToHue/Poses/` folder
   (forward slashes, or escaped backslashes). *Or* pass exact `.duf` paths via the
   `*RomPath` options and skip this.
2. Copy `DthWorkflowExample.dsa` to a per-character script and set its `options`.
3. Create a DAZ **custom action** for that script (Window → Workspace → Customize)
   and optionally bind a shortcut.
4. Select the **Genesis 9** root node and any sub-figures you're posing
   (`Genesis 9 Mouth`, `GoldenPalace_G9`, `DicktatorG9`).
5. Invoke. The run is wrapped in a single undo.

## Options (`DthOptions.dsa`)

| Option | Type | Default | Description |
|---|---|---|---|
| `bIncludeJCM` | bool | `true` | Load the base JCM ROM (and the FAC mouth ROM if `bIncludeFAC`). When `false`, only the `extraJSONs` are applied (from frame 0). |
| `bIncludeFAC` | bool | `true` | Use the FAC version of the base ROM and load the Mouth ROM. |
| `bIncludeGP` | bool | `false` | Append the Golden Palace 9 ROM block (~103 frames). |
| `bIncludeDK` | bool | `false` | **Experimental** — append the Dicktator G9 ROM block (incomplete, see `ApplyDK9`). |
| `bIncludePhysics` | bool | `false` | Append the G9 Physics Example ROM (breast/glute jiggle, 43 frames). |
| `bDQS` | bool | `false` | Dual-Quaternion-Skinning base ROM (328 frames) vs Linear (~626 frames). |
| `FACsDetailStrength` | float | `1.0` | Sets `facs_ctrl_FACSDetailStrength` at frame 0 (when > 0). |
| `FlexionStrength` | float | `1.0` | Sets `body_ctrl_FlexionAutoStrength` at frame 0 (when > 0). |
| `extraJSONs` | [string] | `[]` | FrameData JSON files applied after the ROM blocks (see format below). |
| `jcmRomPath`, `mouthRomPath`, `gpRomPath`, `dkRomPath`, `physRomPath` | string \| null | `null` | Exact `.duf` paths. Non-null values override the `DTH_POSES_PATH` resolution. |
| `gpArtDirectionPath`, `dkArtDirectionPath` | string \| null | `null` | Per-character art-direction JSON stamped onto the GP/DK block. Falls back to `GP9_ArtDirection.json` / `DK9_ArtDirection.json` next to the scripts. |
| `aGPFenceOffsets` | [int] | `[0,95,99,102]` | Fence-frame offsets within the GP block (memorize/restore figure to stop drift between art-directed sections). |
| `aDK9FenceOffsets` | [int] | `[0,33,46,47,49,52]` | DK fence offsets *(experimental — not currently applied)*. |
| `preserveMorphs` | [{name, keepValue}] | `[]` | Morphs to re-assert after ROM loading, e.g. `{ name: "body_ctrl_BreastsUp-Down", keepValue: 0.6 }`. |
| `preserveNodeTransforms` | [{nodeLabel}] | `[]` | Node transforms memorized before ROM loading and restored after, e.g. `{ nodeLabel: "Left Eye" }`. |
| `jcmMorphMods` | [obj] | `[]` | Drive corrective morphs proportionally to a bone's rotation keyframes (linear angle→value mapping). |
| `extraCSVs` | [string] | `[]` | Legacy — only used by `DthExportFBMs.dsa`. |
| `bWriteDebugCSV` | bool | `false` | Write `OUT*.csv` dumps (frameDatas / success / failed / scanned) to the scripts folder. |

## FrameData JSON format

The primary way to add custom morphs. Frame numbers are **relative** — the ROM
block's start frame is added automatically, so the same JSON works regardless of
where it lands on the timeline.

```json
{
  "meta": { "resetGPBeforeApplying": true, "resetDKBeforeApplying": true },
  "frames": [
    {
      "frame": 0,
      "section": "FBM",
      "name": "Heavy",
      "morphs": [
        { "node": "Genesis9", "prop": "body_bs_Heavy", "value": 1.0 }
      ]
    },
    {
      "frame": 1,
      "section": "GEN",
      "name": "Anus_Open",
      "morphs": [
        { "node": "GoldenPalace_G9", "prop": "GP9_Anus_Open", "value": 1.0, "base": 0.0, "autoBase": false }
      ]
    }
  ],
  "groups": [
    { "name": "EyelidsClosed", "method": "additive", "startFrame": 10, "endFrame": 13 }
  ]
}
```

- `meta.resetGPBeforeApplying` / `meta.resetDKBeforeApplying` zero the active
  genital ROM's morphs (Golden Palace / Dicktator) at the **first frame of this
  block**, so the GP/DK morphs don't leak into the custom (FBM) poses. The reset
  reads the morph list from the matching sibling `GP9_ArtDirection.json` /
  `DK9_ArtDirection.json` (or the inline `gpArtDirection` / `dkArtDirection`), and
  only fires for whichever block's art-direction data is present. DTH Character
  Studio emits both flags from one generic "Reset genitalia morphs before extra
  frames" option.
- `node` / `prop` are Daz **internal names** (not labels). `value` is a float —
  `1.0` = 100% on a slider.
- `base` (optional) is the sawtooth anchor value the adjacent frames are set to
  (default `0`). `autoBase: true` resolves that anchor from the morph's current
  scene value at run time.
- `section` / `name` are the Houdini/UE category and morph name (carried through
  for the PoseAsset side).
- `groups` (optional) marks frame ranges with a **generation method**:
  - `additive` — the first pose sustains across the group; the rest sawtooth on top.
  - `cumulative` — every pose sustains until the group end (repeated props ramp).
  - `advancedAdditive` — shaped like `additive` here (the distinction is Houdini-side).
  - omitted / `individual` / `default` — plain isolated sawtooth.

## FrameData CSV format (scan / legacy)

`DthScanFrames.dsa` exports the current timeline to this CSV, editable in any
spreadsheet. Each row: `frame, section, name, nodeName, propName, propValue[, …]`

```
4,,,l_toes,XRotate,-60
306,,,r_upperarm,XRotate,-90,r_upperarm,YRotate,-45,r_upperarm,ZRotate,-45
```

After the first three columns, column triplets specify a node/prop/value. Names
must be Daz internal names. Save as comma-delimited with no quoting.

## Invokable user scripts

- **`DthWorkflowExample.dsa`** — copy, set `options`, apply a full ROM. The main entry.
- **`DthWorkflowFromCSV.dsa`** — apply morphs from JSON/CSV only, no ROM loading.
- **`DthAddMorphFrameFromSelection.dsa`** — sawtooth-key the property hovered in the Parameters pane, then advance a frame.
- **`DthScanFrames.dsa`** — scan selected nodes and export keyframes to CSV.
- **`DthSetLinearInterp.dsa`** — set all morph keys on the selection to linear interpolation.
- **`DthExportFBMs.dsa`** — apply FBMs at frame 0 and drive the DazToMaya `B_FIG.fbx` export.

## Status

- **Validated:** Genesis 9 (female), DQS, `JCM + FAC + GP9 + Physics` plus
  FBM/expression/corrective morphs from JSON.
- **Functional, not hardened:** Dicktator G9 (`bIncludeDK`) — runs end-to-end and
  the morphs come through in Unreal, but it hasn't had the byte-level validation the
  other paths have. The internal fences are intentionally not applied (tested fine
  without them); the uncircumcised foreskin path is unmodeled. See the notes in `ApplyDK9`.
- **Not supported:** G8 / G8.1 / G3.

## Caveats

- For morphs driven by a controller, you may need to click the **toggle override**
  icon on the Parameters-pane slider when applying onto a character that already
  has shaping. (Build ROMs on a rest-pose figure to avoid this.)
- Rudimentary testing only. Keep backups; user-invokable scripts are undo-able.
- The timeline range isn't always set perfectly — if a run completes but the range
  looks short, extend it; all frames are still applied.
- Node lookup matches the **first** node with a given name. If the wrong node gets
  a morph, switch to full paths (hasn't been observed in practice).

## License

See [LICENSE](./LICENSE). The `.duf` ROM presets are **not** part of this repo —
they are mrpdean's licensed DazToHue assets; bring your own.
