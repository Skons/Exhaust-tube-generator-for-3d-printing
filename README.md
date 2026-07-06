# Exhaust Tube STL Generator

A browser-based tool for building custom exhaust tubes segment by segment, previewing them live in 3D, and exporting them as STL files for 3D printing. Everything runs locally in the browser — no installation, no server required.

## Getting started

Open [Exhaust Tube STL Generator](http://skons.github.io/Exhaust-tube-generator-for-3d-printing) in a browser (Chrome, Firefox, Edge, Safari). The page loads three external libraries via CDN (Three.js, earcut, JSZip), so an active internet connection is required when opening it.

## Interface overview

- **Left panel** — list of segments (tube sections) and the settings for the currently selected segment.
- **Right panel (3D view)** — live preview of the tube. Drag to rotate, scroll to zoom. Hold shift+right mouse to move the focal point.
- **Scroll wheel on number fields** — move the mouse over a number field and scroll to adjust the value step by step, without needing to click first.

## Managing segments

- **+ Add segment** — adds a new tube section at the end of the chain, using the same shape/size as the last segment.
- **✕** — deletes a segment.
- **👁 / 🚫** — hides a segment from the 3D view and from the STL export, without deleting it. Segments after a hidden one stay correctly positioned.
- Click a segment in the list to edit it.

## Per-segment settings

### Shape and dimensions
- **Output shape**: round, square, or rectangle.
- **Diameter / Width / Height (mm)**: output dimensions of this segment. For round or square, height is locked to width.
- The **input** of a segment is automatically inherited from the output of the previous segment (continuous tubing) — unless decoupled mode is active (see below).

### Area indicator
Below both the input and output dimensions, the editor shows the cross-sectional area in mm², along with a percentage relative to the area of the very first segment. An orange ⬇︎ icon (shown both in the segment list and in the editor) appears whenever a segment narrows relative to that starting value — useful for spotting unwanted flow restrictions.

### Segment settings
- **Name** — free-form label.
- **Wall thickness (mm)** — material thickness of the tube wall.
- **Length (mm)** — length of this segment.
- **Bend angle (°)** — bend of this segment, from -360° to 360°.

### Bend axis and bend zone
- **Bend axis (X/Y/Z)** — which local axis the segment bends around.
- **Bend start (%) / Bend end (%)** — which portion of the segment length actually bends. Before the start and after the end, the tube runs straight. A minimum value for "Bend start" is enforced automatically when the previous segment has a slip or magnetic coupling (see Couplings), so the tube doesn't start bending inside that coupling.

### Coupling at end of segment
Three options for how this segment transitions into the next:

- **Continuous** — seamless transition, no separation. This also determines where the STL export is split: continuous segments stay together as a single downloadable part.
- **Magnetic coupling** — a wavy ring with round bumps, each containing a round magnet hole, used to join the two parts together with magnets. Both halves (male on this segment, female on the next) share the same shape and holes, so they fit together exactly.
  - **Ring thickness (mm)** — how far the ring extends into the tube, and how far the bumps visibly protrude.
  - **Magnet ⌀ / Magnet thickness (mm)** — dimensions of the disc magnets to be used (adjustable in 0.1mm steps). The ring thickness automatically increases if the magnet is thicker than the set ring thickness.
  - **Number of magnets** — evenly distributed around the circumference.
- **Slip** — a sleeve that slides over the next segment, for a glued connection.
  - **Sleeve length (mm)** — how far the sleeve extends over the next segment.
  - **Clearance (mm)** — gap between sleeve and tube (minimum 0.1mm, guaranteeing it can always be slid together).

### Decoupled mode
Turn on "Decoupled" to make this segment independent from the rest of the chain: its input dimensions are no longer inherited from the previous segment, and this segment's own output dimensions are no longer passed on to the next segment either. Useful for standalone parts that don't need to connect seamlessly. When enabled, separate fields appear to set this segment's own input shape and dimensions.

## STL export

The download button at the bottom right of the 3D view shows how many separate parts the model contains, based on the couplings:

- Only continuous couplings → a single `.stl` file.
- One or more magnetic/slip couplings → a `.zip` file containing a separate `.stl` file per part (split at every non-continuous coupling), so each part can be printed independently.

## Technical notes

- Built with Three.js (3D rendering and geometry), earcut (2D polygon triangulation for the magnetic coupling ring), and JSZip (bundled download when there are multiple parts).
- All geometry is generated client-side; nothing is sent to a server.
- Works best in a recent version of Chrome, Firefox, Edge, or Safari.
