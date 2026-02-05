# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

DazToHue-Scripts is a collection of DAZ Studio scripts (.dsa files) for automating the DazToHue (DTH) workflow. These scripts automate the process of applying Range of Motion (ROM) poses and morph keyframes to Genesis 9 figures for export to Houdini/Unreal Engine.

## Script Architecture

### Core Libraries (included by other scripts)
- **DthUtils.dsa** - Utility functions for timeline manipulation, property access, morph frame creation, CSV parsing, and node traversal
- **DthOptions.dsa** - Default configuration options and ROM file paths; users must set `DTH_POSES_PATH` to their local Daz library
- **ScanKeyFrames.dsa** - Functions to scan nodes for keyframe data and export to CSV format

### User-Invokable Scripts (bind to DAZ custom actions)
- **DthWorkflowExample.dsa** - Main workflow script; applies JCM/FAC ROMs, then processes extraJSONs
- **DthWorkflowFromCSV.dsa** - Applies animation from JSON files only (no ROM loading)
- **DthAddMorphFrameFromSelection.dsa** - Creates sawtooth keyframe pattern for selected parameter
- **DthScanFrames.dsa** - Scans selected nodes and exports keyframes to CSV

### Include Chain
```
DthWorkflowExample.dsa
  └── DthWorkflow.dsa
        ├── DthUtils.dsa
        ├── DthOptions.dsa
        └── ScanKeyFrames.dsa
```

## Key Concepts

### FrameDatas CSV Format
Each row: `frameNumber,sectionName,frameName,nodeName,propName,propValue[,nodeName,propName,propValue,...]`

- First 3 columns: frame number, section (e.g., GEN, PHY, EXP), morph name for Houdini/UE
- Subsequent column triplets: nodeName (Daz internal name), propName (Daz internal name), float value (1.0 = 100%)

### Sawtooth Pattern
Morph frames use a sawtooth pattern: the target frame gets the morph value while adjacent frames preserve their previous values, creating isolated peaks.

### Linear Interpolation
Scripts set all morph keyframes to linear interpolation (`DzProperty.Linear`) to ensure predictable export behavior.

## Configuration

In DthOptions.dsa, key options include:
- `DTH_POSES_PATH` - **Must be set** to your DazToHue/Poses folder (use forward slashes or escaped backslashes)
- `bIncludeJCM/bIncludeFAC` - Whether to load base JCM and FAC ROMs
- `bDQS` - Use Dual Quaternion Skinning ROMs (328 frames) vs Linear (617 frames)
- `extraJSONs` - Array of JSON file paths to apply after ROM loading
- `preserveMorphs` - Array of morphs to restore after ROM loading

## DAZ Studio Script API Notes

- Scripts use `beginUndo()`/`acceptUndo()` for undo support
- Node hierarchy: find children with `findNodeChild(name, recursive)` or `findNodeChildByLabel(label, recursive)`
- Properties can be on nodes directly or on morph modifiers accessed via `node.getObject().getModifier(i)`
- Time is in internal units; convert frames with `Scene.getTimeStep()`
