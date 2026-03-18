# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- **Auto-incremented 4th version component**: `__version__` in `main.py` now appends the total git commit count at runtime (e.g. `0.5.3.47`). The base 3-part version (`0.5.3`) is only bumped manually for real releases. The 4th component is computed by calling `git rev-list --count HEAD` at startup; if git is unavailable the base version is used as-is. The full version is shown in the window title and About dialog.
- **`widgets/flow_layout.py`**: New `FlowLayout(QLayout)` that arranges child widgets left-to-right, wrapping to the next line when the available width is exceeded.
- **Fixed publishing workflow**: The new versioning broke the automatic publishing of the documentation and the executable. This has been fixed.

### Fixed
- **Channel selector — source buttons overflow**: Source toggle buttons were all placed on a single `QHBoxLayout`, making the popup extremely wide when many data sources are present. Now uses `FlowLayout` so buttons wrap automatically onto additional rows.
- **Time trace y-axis label**: `_format_axis_label` was returning channel names plus unit (e.g. `Lens 2, Deflector (V)`). Now returns only the unit string (e.g. `V`); channel names belong in the legend, not on the axis.
- **Legend overflow with many channels**: New `_place_legend()` helper in `GraphingTool`. For ≤ 8 entries the legend stays inside the axes (unchanged). For > 8 entries it is anchored just above the axes via `bbox_to_anchor`, and the existing `tight_layout` pass keeps the plot area intact.

## [0.5.3] - 2026-03-17

### Changed
- **Distribution — Public repo**: The main repository has been renamed to `TWH5Viewer-dev` (private). A new public `TWH5Viewer` repository now serves as the distribution point: it hosts the documentation site (`thegeologician.github.io/TWH5Viewer`) and the Windows executable as a GitHub Release asset. A CI sync workflow in the private repo automatically pushes user-facing files (README, CHANGELOG, docs, roadmap) to the public repo on every relevant commit.
- **Docs — MkDocs button rendering**: Download button on the documentation home page now renders correctly with icon and styling. Added `attr_list`, `md_in_html`, and `pymdownx.emoji` markdown extensions to `mkdocs.yml` (previously missing, causing `{ .md-button }` and `:material-download:` to appear as literal text).

### Fixed
- **icpTOFpy CI — wheel not included in deployed Pages site**: The `update_docs.py` build script was copying the wheel into `site_dir` before `mkdocs build`, which cleans the output directory first and discards the wheel. Fixed by reversing the order (build docs first, copy wheel after) and switching the deploy step from `mkdocs gh-deploy` (which rebuilds from scratch) to `ghp-import` (which pushes the already-built `site_dir` as-is). The wheel is now reliably served at `thegeologician.github.io/icpTOFpy/dist/icptofpy-latest-py3-none-any.whl` after each CI run.

## [0.5.2] - 2026-03-13

### Added
- **File Info — Context menu**: Right-clicking anywhere on the metadata tree now shows a context-sensitive menu. On a group header: "Copy group values" (tab-separated parameter/value pairs to clipboard) and "Export group…" / "Export all metadata…". On a leaf parameter: "Copy value", "Copy parameter name", and group/all export shortcuts. On the file path row: "Copy file path". On the background: "Export all metadata…".
- **File Info — Metadata export**: All export actions open a Save dialog offering five formats — CSV, JSON, HTML, PDF, and PNG:
  - **CSV**: `# File: <filename>` comment on line 1; then Group / Parameter / Value columns with a group-label row preceding each section's data rows.
  - **JSON**: Structured as `{"file": "<filename>", "groups": {"Group": [{"parameter": …, "value": …}, …], …}}`.
  - **HTML**: Styled single-page document with the filename as an `<h1>` header and each metadata group under its own `<h2>` heading, each rendered as an independent table.
  - **PDF / PNG**: Rendered via Matplotlib. Filename printed as a bold title above the table. Group sections introduced by light-blue header rows; data rows alternate white / light-grey. Figure height is calculated precisely from row count to eliminate whitespace (`bbox=[0,0,1,1]` table layout).
- **Channel Selector — Phase 4: Toggle-state group filters**: Geochemical group buttons ([REE], [Silicate], [PGE], etc.) are now stateful toggles. Multiple groups can be active simultaneously and their selections are unioned. A [Major] toggle (default ON) narrows any peak-group selection to the single most-abundant isotope per element. Toggling a group off leaves the current selection unchanged; [All] and [None] clear all toggle state.
- **Channel Selector — Registered data source buttons**: After each file load, metadata source prefixes (e.g. ICAP, TPS2, ODG) are detected from the `"PREFIX: column"` naming convention and appear as toggle buttons in a third row of the channel popup. Multiple sources can be active simultaneously (union). A [Monitor] toggle (default ON) restricts each source's contribution to channels whose column name contains "monitor".
- **Channel Selector — `set_source_names()` API**: New public method on `ChannelSelectorWidget` allows the controller to push registered source names after each file load. The source row is rebuilt lazily on the next popup open, keeping the startup path fast.
- **Channel Selector — Centralised tooltips**: All button tooltip strings are now defined in a single `_BUTTON_TOOLTIPS` dict at the top of `channel_selector.py`. Editing that dict changes all button tooltips in one place. The [All] / [None] / [Peaks] / [Meta] buttons also now display tooltips (previously they had none).

### Changed
- **Correlation plot — Lin X / Lin Y global toggles**: A single stacked pair of Lin X / Lin Y overlay buttons (top-left of the canvas) now appears for correlation plots that contain histogram diagonal cells or scatter triangle cells. Toggling either button applies the scale change to all applicable sub-axes simultaneously. Pure colour-swatch configurations (no histogram or scatter cells) retain the existing hidden-button behaviour.
- **Correlation plot — Tick labels**: Tick labels are now shown on the left column and bottom row only when the lower triangle is configured as scatter plots — the only case where axis labels are meaningful and readable. With colour swatch lower triangles, all tick labels are suppressed.

### Fixed
- **Time Traces — Overlaid mode with 3+ unit groups**: Channels whose unit string did not appear in the first two distinct units were silently omitted from the plot in Overlaid mode. Fixed: with ≤ 2 distinct units the original dual-axis (left primary / right secondary) layout is preserved; with 3+ distinct units all traces are flattened onto the single left axis and the y-label lists up to four units separated by commas.
- **Time Traces — Y-axis label for metadata channels without parseable units**: Metadata channels that carry no unit in their column label (e.g. plain ICAP columns or channels without bracket syntax) were incorrectly shown as "Intensity (counts)". Fixed by removing the unconditional `if not unit: unit = 'Counts'` fallback after the per-trace source-detection logic. Only traces sourced from `raw_image_data` (mass-spectrometry peaks) now receive the "Intensity (counts)" label. Metadata channels with an unresolvable unit are labelled with the channel name(s) only.

## [0.5.1] - 2026-03-11
### Added
- **Graphing Tool — Zoom Range Buttons**: Added separate "Reset X" (↔) and "Reset Y" (↕) overlay buttons next to the `Lin/Log` toggles to independently autoscale the X and Y axes back to the full data extent without resetting the opposite axis.

### Changed
- **Performance — Asynchronous Data Loading**: Massively improved application responsiveness when opening large datasets (e.g. > 5 million rows). All heavy Pandas operations (merging datasets, filtering gas blanks, slicing padding, dropping invalid spots, index casting) have been migrated from the main GUI thread into the background `ImageDataLoader` QThread. The application window no longer freezes ("Not Responding") while the progress bar runs.
- **Performance — Time Traces Plotting**: Eliminated the >1 minute UI freeze when toggling trace channels on large files. The trace plotter now uses an intelligent **Dynamic Min-Max Decimation** downsampling algorithm. If a trace exceeds 10,000 points, it is chunked and only the local minimum and maximum values are emitted to Matplotlib, losslessly perfectly preserving the visual envelope of high-intensity isotope peaks while drawing 100x fewer vertices. 
- **Performance — Interactive Zooming**: Panning or drawing a zoom rectangle on Time Traces seamlessly interacts with the Decimation engine (`xlim_changed` event). Once the visible bounds zoom past the 10,000 point threshold, the plotting engine instantly fetches the raw, full-resolution array chunk directly from the DataFrame and redraws without any sawtooth artifacts.

### Fixed
- **Graphing Tool — Log Scale Crash**: Fixed an `OverflowError` ("Exceeded cell block limit in Agg") that occurred when switching the time-trace Y-axis to Log scale on dense datasets. The combination of downsampling and a globally relaxed `matplotlib.rcParams['agg.path.chunksize'] = 10000` prevents the rendering overflow.
- **Graphing Tool — Time Trace Zooming**: Fixed a `TypeError` raised by Pandas when zooming deep into a time-trace due to a timezone aware vs naive mismatch. Matplotlib limits are now properly aligned with the source array's timezone before calling `searchsorted`.

## [0.5.0] - 2026-03-10
### Added
- **Graphing Tool — Data Plots: Heatmap**: Channels × time rendered as a `pcolormesh` heatmap. Each channel is normalised independently to [0, 1] so high-intensity peaks do not drown out weak ones. Colour scale defaults to 1st/99th percentile clipping for better contrast; the "Colour range" option in Plot Options switches to the full extent. Colourbar labels always display the normalised [0, 1] range. Log/lin overlay buttons are hidden (irrelevant for a colour map). Grid lines suppressed. Channel labels shown on the y-axis.
- **Graphing Tool — Data Plots: Correlation**: N×N pairplot-style correlation matrix. Configurable per zone (diagonal, lower, upper): colour-swatch (Pearson/Spearman r mapped to a diverging colourmap), density histogram, or scatter plot. Optional regression line overlay on scatter cells. Channel names placed inside the diagonal cells via a text annotation to avoid label clipping. Colour scale bar labelled with the correlation method.
- **Graphing Tool — Data Plots: Histogram**: Selected channels plotted as overlapping, semi-transparent distributions for direct comparison. Two styles: **Histogram** (matplotlib `hist`) and **Density (KDE)** (scipy `gaussian_kde` with filled area; falls back to density histogram if scipy is unavailable or data is constant). Log-spaced bins used automatically when log-X is active and all values are positive. Lin/log toggles active on both axes. Options: style, bin count (5–500), normalise to density.
- **Graphing Tool — Data Plots: Options dialog**: Plot-type-specific options panel (`⚙` button) extended with sections for all three new plot types:
  - *Heatmap*: colourmap, per-channel normalisation toggle, sort by mass, colour range (Percentile 1–99 % / Full extent).
  - *Correlation*: colourmap, correlation method (Pearson/Spearman), diagonal/lower/upper zone style, regression line toggle.
  - *Histogram*: style (Histogram/Density KDE), bin count, normalise to probability density.

### Fixed
- **Graphing Tool — Heatmap: single-channel rendering**: `apply_theme_to_canvas` internally calls `autoscale_view()`, which collapsed the y-axis to [0, 0] for a single-point y-array. Fixed by moving `ax.set_ylim(-0.5, n_ch - 0.5)` to after the theme call so it is never overridden.
- **Graphing Tool — Heatmap: grid lines**: Theme application re-enabled the axes grid. Added an explicit `ax.grid(False)` after the theme call.
- **Data Plots — Zoom rectangle not drawn**: After `fig.clf()` in `_reset_data_plot()`, `ax.add_patch(rect)` updates `rect.axes` but leaves `rect._transform` pointing at the destroyed old axes' `transData`. The `on_press` check then never corrected it, so the rectangle was drawn off-screen. Fixed by using `ax.transAxes` as the resting transform (excluded from matplotlib autoscaling) and unconditionally switching to `ax.transData` at the start of every zoom drag in `on_press`.
- **Data Plots — Heatmap x-only zoom**: Added a `_zoom_x_only` flag on `MplWidget`. When `True` (set by `update_heatmap_plot`), the zoom rectangle is pinned to the full y-extent of the axes and only x-limits are applied on release — zooming the channel axis of a heatmap makes no sense.
- **`on_log_x_toggled` — non-positive ylim warning**: The method preserved the y-limits across an x-scale switch, but the saved limits could contain 0 or negative values (from a prior autoscale) if the y-axis was already log-scaled. Added a clamp to `max(value, 1e-300)` before restoring, silencing the matplotlib `UserWarning`.
- **`MplWidget.closeEvent` — constrained-layout shutdown warning**: Each `MplWidget` figure is created with `constrained_layout=True`. During Qt widget teardown the layout engine could fire one last time on a zero-sized canvas and emit a spurious "axes sizes collapsed to zero" warning. Fixed by calling `figure.set_layout_engine(None)` in `closeEvent` before handing off to `super()`.

## [0.4.9] - 2026-03-06
### Fixed
- **Multi-resolution time axis for non-LA files**: When loading segmented non-LA files (e.g. liquid analyses with `nbrSegments > 1`), metadata and peak data now correctly use independent time axes at their native resolutions. Previously, metadata was padded with NaN to match the segment-expanded length, causing mostly-empty metadata traces plotted at wrong x-positions. Metadata traces now plot at their natural buf/write-level timestamps while peak traces use the segment-expanded time axis. Time cursor, spinbox navigation, and data exports all handle the resolution difference correctly. No behavioral change for LA files (`nbrSegments = 1`).
- **Cursor TOF Spectrum — sub-buf segment index**: The spectrum plot now correctly loads the TOF spectrum for the exact segment the time cursor is positioned on within a buf/write event. Previously, it always loaded segment 0, so the spectrum only changed when the cursor crossed a whole-buf boundary rather than updating at every segment step.
- **Average TOF Spectrum — segmented files**: The averaged TOF spectrum over a cursor range now correctly handles files with `nbrSegments > 1` (any file type). `WriteIndex`, `BufIndex`, and `SegmentIndex` are read directly from `raw_image_data` for both LA and non-LA paths, so no special-casing is needed and the spectrum is always loaded from the exact (write, buf, segment) coordinates in the HDF5 file.
- **Exact per-row write/buf/segment indices in `get_peak_data()`**: `icpTOFpy.doubleWrap.get_peak_data()` now always prepends three integer columns — `WriteIndex`, `BufIndex`, `SegmentIndex` — to every DataFrame it returns. These are derived directly from the buffer shape used to call `TwGetPeakData` (write-major C order) and are therefore exact regardless of whether total rows are a perfect multiple of `nbrSegments`. `get_imagedata()` drops the arithmetic `WriteIndex`/`BufIndex` from `get_peak_data()` before concat, keeping the authoritative SpotData values; `SegmentIndex` is always kept.
- **Correct segment/metadata-row mapping for all file types**: `segment_index_to_meta_index()` now uses a pre-built `_meta_index_for_row` lookup array (computed at load time from `WriteIndex * nbrBufs + BufIndex`, or identity for filtered LA data). `get_segment_for_row()` reads `SegmentIndex` directly from `raw_image_data`. Both helpers now work correctly for any `nbrBufs` value, replacing error-prone row-position arithmetic. The `DataModel` also stores `nbr_bufs` and `nbr_writes` from the descriptor alongside the existing `nbr_segments`.
- **Removed incorrect `cached_time_axis['event']`-based lookup**: A previous attempt to fix segment/event indexing via `timing_data_to_posix`'s `'event'` column was reverted. See `icpTOFpy/docs/DATA_DIMENSIONS.md` for details.
- **Fixed BufTimes buffer ordering**: `timing_data_to_posix()` now correctly allocates the timing data buffer as `(nbrWrites, nbrBufs)` in C row-major order (write-major), matching the official TOFWERK HDF5 format specification. Previously `(nbrBufs, nbrWrites)` — buf-major — was used, which would produce incorrect timestamps when `nbrBufs > 1`. All downstream metadata row lookups now use the write-major formula `WriteIndex * nbrBufs + BufIndex`.
- **Per-write registered data handling**: `get_time_resolved_metadata()` now correctly detects and expands per-write data sources (`TwRegisterUserDataWrite`, `NbrWrites` rows) vs per-buf sources (`TwRegisterUserDataBuf`, `NbrWrites × NbrBufs` rows). Per-write sources are expanded by repeating each write's values for every buf within that write.

## [0.4.8] - 2026-03-05
### Added
- **Graphing Tool — Numerical Data Export**: All three plot panels now have a working "Export Data…" menu entry (under the Export button dropdown):
    - **Spectrum**: exports mass/TOF x-axis and all visible signal lines as CSV or JSON.
    - **Time Trace**: exports all selected trace channels aligned to the authoritative `cached_time_axis` time series. Time column is user-selectable as **relative seconds from start** (`time_relative_s`) or **full-precision POSIX timestamp** (`time_posix_s`, float64). In CSV mode, POSIX timestamps are always written as 9-decimal fixed-point strings to prevent precision loss.
    - **Data Plot**: exports x- and y-channel arrays; when `Time (s)` is the x-axis, the same time-mode option is presented.
- **Imaging Tool — Numerical Data Export**: "Export Data…" menu entry under the Export button offers two modes:
    - **Point data**: one row per measurement spot, with X/Y coordinates and selected channels.
    - **Grid matrix**: selected channels interpolated onto a user-specified rectangular raster using `scipy.spatial.cKDTree` nearest-neighbour lookup (O(M log N); tree built once and reused across all channels). Output is a DataFrame with Y-indexed rows and X-labelled columns (µm). Multi-channel CSV exports create one file per channel. The output resolution is independent of the input spot spacing and works correctly for arbitrary (non-grid) point locations.
- **Export dialog — Format options**: The shared `NumericalExportDialog` supports CSV (configurable separator, decimal mark, float precision) and JSON (orientation, pretty-print). The POSIX time mode locks the precision spinner to 17 and shows an explanatory hint.
- **`scipy` added as a project dependency** (`requirements.txt`).

### Fixed
- **Graphing Tool — Time-Trace Export**: Time column previously contained a constant value due to searching for a "Time"-like column in `cached_metadata`/`raw_image_data`. Now reads exclusively from `cached_time_axis['time']`.
- **Graphing Tool — Data-Plot Export**: Same fix applied to the `Time (s)` x-axis path.
- **Export dialog — POSIX precision**: Float precision spinner range raised to 1–17; POSIX mode auto-sets it to 17 and restores the previous value correctly on switching back to relative (fixed double-signal-connection bug that prevented restore).

## [0.4.6] - 2026-03-04
### Added
- **Graphing Tool:**
    - **Graph Export Dialog**: "Export" opens a simplified layout dialog specifically tailored to cleanly saving 1D line graphs and scatter plots as `.png`, `.svg`, `.pdf`, or `.tiff` images with precise physical size mapping matching the screen aspect ratio.
    - **Quick Clipboard**: The "Copy" button instantly takes a snapshot of the current plot exactly as styled and ships it to the system clipboard for immediate pasting elsewhere.

- **Imaging Tool:**
    - **RGB Channel Auto-Naming**: The text field for the channel name now auto-updates seamlessly as the user selects different target data channels, and is smart enough to handle user-customized names without overriding them randomly.
    - **Apply Button**: Added an "Apply" button to both the Ratio and RGB composites dialog to preview settings effortlessly on the main plot without needing to repeatedly close and reopen the dialog.
    - **Contextual Reset Buttons**: The Ratio and RGB composites property dialogs now fully initialize using the active map's true data domain, populating the contrast `auto` check ranges correctly using 1st/99th percentiles of the source data layer. Added "Reset" buttons to individual RGB channel bands to mimic the ratio tool.

### Fixed
- **Image Export:**
    - When selecting "Separate Overlays" inside the image export options, RGB channels now successfully export with data, rather than yielding blank images.
    - The empty colorbar frame artifact traditionally accompanying exported RGB images is now forcibly suppressed globally to yield clean exports.
    - Batch Image Export logic now interprets the UI channel labels safely, meaning RGB map grids render correctly instead of exporting blank plots.

## [0.4.5] - 2026-03-03
### Added
- **File Info Tab:**
    - **Configuration File Contents**: The root HDF5 `Configuration file content (inifile_content)` attribute is now displayed as a collapsible text box at the bottom of the "Configuration" group. A **Copy** button in column 1 copies the full INI content to the clipboard without needing to expand the text box.
    - **Configuration group**: `inifile` (the configuration file path) and `inifile_content` are now shown together under a dedicated "Configuration" section in the metadata tree (requires `icpTOFpy` ≥ 0.2.8).

### Fixed
- **Graphing Tool:** Time-resolved metadata channels (e.g. ICAP, TPS2, ODG) were appearing twice in the channel selector dropdowns — once as a flat list after the isotope peaks, and again in their correctly grouped sections. Fixed by excluding `cached_metadata` columns from the "Peaks (Image)" section in `GraphingController.populate_trace_dropdown()`.
- **Graphing Tool Availability:** Graphing is now enabled for *all* valid TOFWERK dataset files (including mipTOF and fibTOF), instead of rigidly enforcing the `is_icpTOFData` check (which looked for `/ICAP` specifically).
- **Imaging Tool Accessibility:** Fixed a bug where returning from the background loading worker unconditionally enabled the Imaging tool regardless of whether laser ablation data (`/ImageData` or `/SpotData`) actually existed in the file.
- **Metadata decoding:** Column labels in registered data sources (TPS2, ICAP, ODG) that contain non-ASCII characters (e.g. `°`, `µ`, `·`) no longer cause `UnicodeDecodeError`. Fixed in `icpTOFpy` ≥ 0.2.8 via a UTF-8 → Latin-1 fallback decode.

## [0.4.4] - 2026-02-27
### Added
- **Imaging Tool**:
    - **Plot Titles**: Titles can now be toggled on/off and positioned inside the plot area (four corners) or outside (top). Includes customizable template syntax (e.g., `{sample}` or `{channel}`) replacing hardcoded values.
    - **Physical Scale Bar**: Added a dynamic scale bar reflecting physical dimensions (e.g., "10 µm") tailored to the loaded image bounds. Built on Matplotlib's `AnchoredSizeBar`, it actively recalculates specific integer widths natively during zoom/pan interactivity.

### Fixed
- **Image Export**:
    - **Contact Sheet Proportions**: Fixed a regression in Batch Export where the contact sheet ignored the user's `current view` limit settings, and properly wired it to render within the exact zoom scope. 
    - Resolved UI ghosting logic preventing outside plot titles from properly clearing when switching to interior titles without resetting the plot.

## [0.4.3] - 2026-02-27
### Added
- **Graphing Tool**:
    - **Time as Plot Parameter**: "Time (s)" is now available as a selectable X-axis metadata parameter for Scatter Plots.
    - **Spectrum Selection**: Added a drop-down to switch the Spectrum Plot between the `Sum Mass Spectrum`, a specific `Cursor TOF Spectrum` or `Cursor Stick Spectrum` at the current time-cursor index, or an `Average` variant of both when using a time range.
    - **Interval Averaging**: The second time cursor (Time Range) is now used to automatically request the averaged TOF mass or Stick spectrum across the bounded acquisition indices. 

### Fixed
- **Graphing Tool**:
    - Fixed a bug where zooming out (double-clicking or the `Reset` button) on the Time-Trace plot would erroneously clear all active cursors or distort the timeline axis bounds.
    - Enhanced string parsing logic to safely determine numeric properties (`U238`, `Li6`) of Peak traces.
    - Fixed an issue causing Stick Spectrum rendering to fail on certain scans due to empty `NaN` values by automatically dropping invalid data points.
    - Corrected Matplotlib `TypeError` crashes when toggling the Log-X scale on datetime/timestamp axes.

## [0.4.2] - 2026-02-25
### Added
- **Graphing Tool**:
    - **Data Plots Panel**: Added a new, fully collapsible "Data Plots" panel.
    - **Scatter Plots**: Implemented dynamic scatter plots allowing any channel (peaks, metadata, derived channels) to be plotted on the X-axis against multiple channels on the Y-axis.
    - **Log Scale Toggles**: Added on-plot `Lin X` and `Lin Y` toggles for independent logarithmic scaling of both axes.
    - **Plot Interactions**: Scatter plots are fully interactive and connected to the main DataModel, updating reliably when the "Update Plot" or "Auto-update" controls are used.

## [0.4.1] - 2026-02-25
### Added
- **Graphing Tool Refactoring**:
    - **Collapsible Views**: Added `CollapsibleWidget` headers for the Sum Mass Spectrum and Time Traces panels to allow users to maximize vertical screen space.
    - **Inline Toolbars**: Migrated plot-specific tools (Traces dropdown, Update Plot, Time Cursor, Reset) from the global toolbar to live inline with their respective collapsible headers for a cleaner, modern layout.
    - **Smooth Index Scrolling**: Implemented modulo-based integer divisions to allow the Time Cursor's `BufIndex` spinbox to smoothly wrap around its boundaries and organically increment/decrement the `WriteIndex` when scrolling.

### Fixed
- **Graphing Tool**:
    - Fixed an issue where manual typing in the Time Cursor spinboxes would prematurely jump due to immediate keystroke signals; they now wait for `Enter` or loss of focus.
    - Fixed an issue where the Time Cursor line would permanently disappear after double-clicking the plot to reset the zoom bounds.
    - Optimized internal `QSplitter` math to properly recalculate heights based on actual nested widget sizes, perfectly clamping collapsed headers to their minimum bounds.

## [0.3.5] - 2026-02-20
### Added
- **Adaptive "Physical Anchor" Layout:** 
    - Exported images now interpret the requested size as the **exact plot area** (Physical Anchor).
    - Figure margins adaptively expand in mm to accommodate all decorations (labels, titles, colorbars) without cropping or jitter.
    - **Deterministic Batch Alignment**: Margins are locked to the "worst-case" requirements for all channels in a batch, ensuring perfect spatial stability between images.
    - **Enforced Font Scaling**: Limits enforced for titles (8-48pt) and labels (5-35pt) with balance-aware scaling based on final figure area.

### Fixed
- **Code Robustness**:
    - Resolved `UnboundLocalError` by consolidating plotting imports.
    - Fixed `ValueError` in subplot initialization and `TypeError` in layout parameter handling.
    - Standardized export signature to support advanced layout parameters.

## [0.3.4] - 2026-02-17
### Fixed
- **Imaging Tool:**
    - **Plot Options (vmin/vmax):** Auto-computed values are now always displayed in the spin boxes (read-only when Auto is on). Unchecking Auto keeps the value and makes the field editable.
    - **Image Export:** Export dialog now accounts for axes, labels, and colorbar space. The specified Size is the plot area; total export dimensions (including decorations) are shown below. Reset button moved next to Lock aspect ratio checkbox.
    - **Reset vmin/vmax:** Added Reset buttons to Plot Options dialog. Resets to auto percentile (when Auto is on) or data min/max (when Auto is off).
    - **Ratio channel Reset:** Added Reset buttons to Ratio Channel dialog. Dynamically computes min/max and 1st/99th percentile of the actual ratio data.
    - **Divide-by-zero warnings:** Suppressed expected RuntimeWarnings when computing ratio channels with zero denominators.
- **Graphing Tool:**
    - **Y-axis Labels:** Y-axis now shows descriptive labels based on selected channel names and units (e.g., "Intensity (counts)" for peaks, "Einzel 1, Einzel 2 (V)" for metadata).
    - **Unit parsing:** Added `parse_column_units()` to icpTOFpy for robust extraction of units from column labels with different formats (TPS2 `[unit]`, ICAP/ODG `(unit) [range]`).
    - **Double-click reset:** Double-clicking the time-trace plot now resets the view to full data range.
    - **Peak order:** Trace dropdown now preserves data-file order instead of alphabetical sorting.
    - **Trace/Peak parity:** Trace dropdown now shows the exact same peak list as the Imaging peak dropdown.

### Changed
- **Documentation:** Updated README, docs pages (index.md, usage.md) to reflect all v0.3.x features. Created ROADMAP.md for deferred features.

## [0.3.3] - 2026-02-13
### Fixed
- **Metadata Display:**
    - Correctly displays "Laser Vendor" (e.g., Teledyne Photon Machines).
    - Correctly displays "Analysis run time" duration.
    - Removed debug console output for cleaner execution.
- **Graphing Tool:**
    - **Time Traces**: Fixed X-axis to display formatted Date/Time (instead of relative seconds) for metadata traces.
    - **Dropdown Sync**: Added Imaging Peaks (isotopes) to the Time Trace selector, allowing plotting of image data sequence alongside metadata.
    - **Mixed Data Handling**: Implemented logic to handle plotting of Time-based metadata and Index-based image data (on separate X-axis domains).

## [0.3.2] - 2026-02-13
### Added
- **Imaging Tool Capabilities:**
    - implemented **Sync-on-Select** for Ratio and RGB channels: Selecting a derived channel now automatically updates the global Plot Options (Contrast settings) to match the channel's saved properties.
    - Added **Apply Button** to "Edit Ratio Channel" dialog for immediate visual feedback without closing the dialog.
    - Added **Clip Mode** support ("Clamp" vs "Transparent") for Ratio images.

### Fixed
- **Metadata Display:**
    - Resolved `NaN` display issues for Laser Spot Size.
    - Added proper formatting for durations (d h m s) and timestamps (local time with timezone).
- **Imaging Tool:**
    - Fixed **Status Bar** display for Ratio and RGB channels (now shows calculated values under cursor).
    - Fixed **Contrast Control** for Ratio images (now respects global Min/Max settings).
    - Fixed **Synchronization** issues between "Plot Options" and "Ratio Channel" dialogs.
    - Improved **Default Filenames** for RGB (e.g. `File_R..._G..._B...`) and Ratio exports.
    - Implementing **Proportional Scaling** in Image Export dialog when switching between Current View and Full Map.

## [0.3.1] - 2026-02-06
### Fixed
- **Critical Plot Extent Bug:** Resolved "Initial Zoomed Out" issue where the Imaging plot would initialize with an incorrect extent (showing the origin `0,0`) due to an invisible Zoom Rectangle being included in the autoscale calculation.
    - Added `rect.set_in_layout(False)` to exclude the rectangle from autoscaling.
    - Initialized the rectangle at valid data coordinates `(min_x, min_y)` instead of `(0,0)`.
- **Reset Logic:** Standardized the "Reset" button to use the exact same logic as the "Double-Click Reset" handler, ensuring consistent behavior.
- **Data Filtering:** Added robust filtering to remove invalid data points (e.g., `-1, -1`) from the plotting pipeline, preventing map distortion.

## [0.3.0] - 2026-02-05
### Added
- **Extended Metadata Browser:**
    - Replaced basic parameter table with a structured `QTreeWidget`.
    - Organized metadata into collapsible groups (General, Default, Plasma, TOF, Tuning, Laser, Image, Timing, DAQ).
    - Intelligent expansion of relevant groups based on file type (e.g., Image group expands for LA Images).
    - Tooltips showing parameter descriptions and ranges.
- **HDF5 Structure Browser:**
    - Replaced "Data Summary" with a full HDF5 hierarchical tree view.
    - Displays Groups, Datasets (with shape/dtype/size), and Attributes.
- **Plotting Enhancements:**
    - Implemented In-Place updates for `PatchCollection` to prevent "Zoom Drift" during parameter changes.
    - Added "Location Preview" mode for LA Image files.
    - Added "Zoom Rectangle" with custom styling (`#ff7116`).
    - Standardized `GridSpec` layout for robust plot resizing.
- **Dark Mode Support:**
    - Applied custom dark theme (`#2b2b2b` background, `#9da1a6` text) to Matplotlib widgets.

### Changed
- **Performance:**
    - Implemented hybrid "Lazy + Injection" loading strategy for HDF5 files.
    - Moved heavy metadata calculations to background threads.
    - Optimized large dataset handling in Imaging tab to prevent crashes.

## [0.2.0] - 2026-02-01
### Added
- Initial support for Graphing tool.
- Basic File Info tab.
- Integration with early `icpTOFpy` versions.
