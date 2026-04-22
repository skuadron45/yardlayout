# Yard Layout Implementation Plan

This document tracks the implementation plan for improving `index.html` into a single-yard parking layout designer. The current direction is to keep one logical yard, no visible yard boundary, and manage slots with line, row, status, drag and drop, rotation, save/load, and editing controls.

## Target Outcome

The application should allow users to:

- Manage one main yard.
- Add individual parking slots.
- Generate multiple slots by line and row.
- Edit slot information such as label, line, row, status, position, size, and rotation.
- Use slot statuses: available, reserved, occupied, and disabled.
- Drag slots around the canvas.
- Rotate slots by fixed increments or with the rotation handle.
- Save and load layouts as JSON.
- See clear statistics for each slot status.

## Target Data Model

### Yard

```js
{
  id,
  name,
  x,
  y,
  width,
  height,
  rotation
}
```

The yard is kept as layout metadata. It does not need a visible boundary on the canvas.

### Slot

```js
{
  id,
  yardId,
  line,
  row,
  label,
  status,
  x,
  y,
  width,
  height,
  rotation
}
```

Supported `status` values:

```js
"available"
"reserved"
"occupied"
"disabled"
```

## Status Mapping

```js
const SLOT_STATUSES = {
  available: { label: 'AVAILABLE', color: '#22c55e' },
  reserved: { label: 'RESERVED', color: '#f59e0b' },
  occupied: { label: 'OCCUPIED', color: '#ef4444' },
  disabled: { label: 'DISABLED', color: '#94a3b8' }
};
```

## Phase 1 - Core Cleanup

- [ ] Keep the implementation as a single-file `index.html` for now.
- [ ] Use `slot` terminology consistently in UI and state.
- [ ] Store slot status as a string instead of a boolean.
- [ ] Render status labels in English.
- [ ] Update the legend for available, reserved, occupied, and disabled.
- [ ] Update statistics for all statuses.
- [ ] Update the mini map so slot colors follow status.

## Phase 2 - Single Yard

- [ ] Keep one main yard as metadata.
- [ ] Do not render a visible yard boundary on the canvas.
- [ ] Keep all slots associated with the main yard.
- [ ] Preserve `yardId` for save/load compatibility.
- [ ] Avoid multi-yard UI controls.

## Phase 3 - Slot Line and Row

- [ ] Add `line` to each slot.
- [ ] Add `row` to each slot.
- [ ] Generate labels such as `A-01`, `A-02`, and `B-01`.
- [ ] Show line and row on each slot.
- [ ] Allow editing line and row from the context menu or property panel.
- [ ] Allow search by label, line, and row.

## Phase 4 - Multi Slot Generator

- [ ] Generate multiple slots from line count and row count.
- [ ] Support line prefix.
- [ ] Support start row.
- [ ] Support start X and Y.
- [ ] Support horizontal and vertical gaps.
- [ ] Support default status, including disabled.
- [ ] Refresh canvas, list, statistics, and mini map after generation.

## Phase 5 - Drag and Drop

- [ ] Support stable drag for single slots.
- [ ] Snap to grid when enabled.
- [ ] Clamp rotated slots against the top and left canvas edges.
- [ ] Clamp final drop position against the canvas.
- [ ] Keep property panel position fields synced during drag.
- [ ] Keep the mini map synced after drag.

## Phase 6 - Rotation

- [ ] Show the rotation handle only for the selected slot.
- [ ] Rotate around the slot center.
- [ ] Support rotate 90 degrees.
- [ ] Support rotate -90 degrees.
- [ ] Support reset rotation.
- [ ] Support free rotation with 15-degree snap.
- [ ] Clamp rotated bounds against the canvas.

## Phase 7 - Slot Status

- [ ] Support available.
- [ ] Support reserved.
- [ ] Support occupied.
- [ ] Support disabled.
- [ ] Show status on the slot body.
- [ ] Show status in the slot list.
- [ ] Filter by status in the sidebar.
- [ ] Save and load status values.

## Phase 8 - Property Panel

- [ ] Show a details panel when a slot is selected.
- [ ] Edit label.
- [ ] Edit line.
- [ ] Edit row.
- [ ] Edit status.
- [ ] Edit X and Y.
- [ ] Edit width and height.
- [ ] Edit rotation.
- [ ] Re-render the selected slot after edits.

## Phase 9 - Save, Load, and Migration

Target JSON format:

```js
{
  version: 2,
  yardIdCounter,
  slotIdCounter,
  activeYardId,
  yards: [],
  slots: [],
  canvas: {
    width,
    height,
    gridSize
  }
}
```

- [ ] Save the current schema.
- [ ] Load the current schema.
- [ ] Migrate old files that use `spots`.
- [ ] Create `Main Yard` when loading data without a yard.
- [ ] Preserve line, row, status, position, size, and rotation.
- [ ] Fall back to `available` for unknown statuses.

## Phase 10 - Manual Testing

- [ ] Open the page with no default slots.
- [ ] Add one slot.
- [ ] Generate multiple slots.
- [ ] Drag a slot.
- [ ] Drag a rotated slot against the top and left edges.
- [ ] Rotate a slot 90 degrees.
- [ ] Rotate a slot with the handle.
- [ ] Set status to available.
- [ ] Set status to reserved.
- [ ] Set status to occupied.
- [ ] Set status to disabled.
- [ ] Filter slots by status.
- [ ] Search by label, line, and row.
- [ ] Save layout.
- [ ] Load layout.
- [ ] Load an old `spots` format file.
- [ ] Check the mini map indicator and mini map dragging.

## Implementation Order

1. Stabilize slot status and labels.
2. Keep the yard model single-yard only.
3. Improve line and row generation.
4. Improve drag and rotated bounds.
5. Improve rotation controls.
6. Add disabled status everywhere.
7. Keep save/load migration compatible.
8. Polish mini map behavior.
9. Run manual browser checks.

