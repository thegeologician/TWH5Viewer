# Roadmap

This document tracks planned features and enhancements for TWH5Viewer.
Known bugs and issues are tracked in [BUGS.md](BUGS.md).

> Priority ordering is determined by a combination of **user impact** and **implementation effort**. 

---

## v0.5.x (Current version — 0.5.6)
*Focus: Visualization enhancements, channel filtering, layout flexibility, export completion, and core application stability.*

### Graphing Tool
- [x] **Histogram plot binning options**: Options to either keep current, use same bin width for all channels, or calculate bin edges from min/max of all channels.
- [x] **Histogram plot split**: Option to split into individual histograms for each channel, or plot them all in one plot.
- [x] **Lin/Log toggles**: Make Lin/Log toggles available for all scatter- and histogram-plots in the Coplot all at once.
- [x] **Logarithmic & specific color scaling**: Logarithmic and other (ECDF, etc.) color-scaling options for heatmaps. *(v0.5.5)*
- [x] **Improved channel selectors — imaging tool**: Port `ChannelSelectorWidget` to the imaging tool's single-select channel dropdown (search box + group pre-filters, single-select mode). *(v0.5.5)*

### Imaging Tool
- [x] **Metadata channels as spatial maps**: Metadata channels (e.g. gas flow, pressure, temperature logged per buf/write) can now be selected in the imaging tool channel picker and rendered as spatial colour maps. Values are joined to spot rows via `WriteIndex × nbr_bufs + BufIndex`. *(v0.5.6)*
- [x] **Correct Y-axis orientation**: Image Y-axis is now inverted so the first scan line appears at the top. *(v0.5.6)*
- [ ] **Interpolated output extent**: Clip numerical output and interpolated image export to the extent of the actual spot locations + 1/2 spot size.
- [ ] **Interpolated image export**: Export an interpolated image where pixel values are interpolated between raw data points. Include interpolation method (bilinear, bicubic) and resolution options.

### App Structure & Export
- [ ] **Standalone image export CLI**: Finalize and document the `image_export.py` `__main__` CLI block so it works end-to-end as a standalone command-line tool.
- [ ] **Image export completeness**: Ensure `image_export.py` supports every option exposed in the Export dialog.

### icpTOFpy
- [x] **Better TPS2 parameter names**: Translate names of TPS2 parameters to be more user-friendly. Requires lookup table.
- [x] **Implement is_Quantistar()**: New metadata function and integration into the metadata loading system.

### File info
- [x] **Copy values**: Add "Copy value" and "Copy path" options to context menu. Copy whole branch/group when called on a group header. *(v0.5.2)*
- [x] **File path**: Add "Copy file path" option to context menu. Either make file path available in the metadata tree or add it to the file info dialog or show the file name and the "copy path" option globally. *(v0.5.2)*
- [x] **Export metadata**: Add "Export metadata" option to context menu. Export selected group or currently expanded groups or the whole metadata to a CSV file, JSON file, or a nicely formatted table as html, pdf, or png. *(v0.5.2)*

---

## v0.6.0 (Analysis Workflows)
*Focus: ROIs, derived channels, statistics, and cross-tool parameter exchange.*

### Documentation and UX
- [ ] **Online Docs**: Expand Usage information with more explanations of all the different options and screenshots.
- [ ] **Better Tooltips**: Add tooltips to all the different options and parameters. Prepare a table of all items and tooltips for review.
- [ ] **Help option**: Add a help option to the menu bar that opens the online docs.
- [ ] **Better "About" dialog**: Add a "About" dialog that shows the version number, the author, and the license. Include a link to the website.
- [ ] **App icon and splash screen**: Add an app icon and a splash screen (Nuitka or in python?). Add TW logo to the About screen.
- [ ] **Dark mode**: Add dark mode / light mode support.

### Graphing Tool
- [ ] **Derived parameters**: Formula-based derived channels freely exchangeable and usable in both Graphing and Imaging Tools.
- [ ] **"Universal" derived channels**: Allow for complex, mathematical formula calculations between channels, progressing past hard-coded ratios.
- [ ] **Statistics Data Plot**: New Data Plot type — "Statistics" to show currently selected channels as a labelled box plot.
- [ ] **Time range quick stats**: When a time range is selected in time trace view, show quick stats for each channel (min, max, mean, std, median, count) in the info box.
- [ ] **Regression lines**: Option to put a regression line, optionally with correlation coefficient and R-squared value, on scatter plots.
- [ ] **Derived time traces**: Calculated time traces from existing parameters.
- [ ] **Automatic labels**: String metadata (scan/sample names) plotted along the time axis.
- [ ] **Performance improvements**: Implement decimation for the heatmap, similar to the approach for the time traces outlined in [Performance Optimizations](plot_performance_optimizations.md). Is something similar possible for the other plots, is it sensible? For scatter plot or histogram plot, maybe sub-sample to manageable amount of randomly sampled data points.
- [ ] **Bulk-selection of channels**: Allow for bulk-selection of channels in the channel selector (select one, Shift-select the last, allow to toggle all in between). 

### Imaging Tool
- [ ] **Contrast histogram**: Display pixel values as an interactive histogram with draggable vmin/vmax handles.
- [ ] **Colour scale for false-colour images**: Show RGB colour scale as three separate bars or a mixing-triangle.
- [ ] **Editing RGB channels**: Buttons to rotate (RGB→GBR) or swap (RGB→RBG) channel assignment.
- [ ] **Colorblind-safe colormaps**: viridis, cividis, batlow.
- [ ] **Multi-channel images**: Export a selection of channels as a multi-channel image to TIFF, layers to SVG/PDF, or GIF.
- [ ] **Export layers**: SVG/PDF/TIFF export with image and decorations in separate layers.

### Analysis Features
- [ ] **ROI Tool**: Draw Rectangle/Circle/Polygon ROIs; extract statistics and spectra. Multi-ROI comparison.
- [ ] **Histogram ROI**: Pixel intensity histogram for the active peak (ROI "full map" option).
- [ ] **Laser metadata**: Make scan names, sample types, and laser spot types available as time-resolved data and spatial data.
- [ ] **ROI by scan/sample**: Auto-generate ROIs from laser metadata (one ROI per scan/sample).

### icpTOFpy
- [ ] **Extract upstream data logic**: Migrate pixel filtering, column extraction, and ratio computation from `TWH5Viewer` into `icpTOFpy`.
- [ ] **Instrument lookup**: Implement a function to translate ADQ serial number to instrument serial number.
- [ ] **Further instrument models**: Add support for more instrument models (Vocus line, ecTOF?) - include into metadata system.
- [ ] **DAQ hardware**: Add support for DAQ hardware (ADQ1600, ADQ14, ...) - include into metadata system.

### File Tool
- [ ] **Better overview**: Show status of the various "is_...()" functions more graphically, only show instruments that apply to the current file.

---

## v1.0.0 (Architecture & QoL)
*Focus: Overall optimizations, usability polish, plugin systems, and robust state management.*

### App Structure
- [ ] **Active Channel Filter**: Global "Active Channels" filter visible in all tools/exports (option to hide or not load them).
- [ ] **Settings Dialog**: UI for application-wide defaults (colormap, spot shape, theme, etc.) and default presets for all the individual options dialogs.
- [ ] **Save / Load Workspace**: Save open file, selections, ROIs, derived channels to a file and restore.
- [ ] **Keyboard shortcuts**: Common actions (Ctrl+O, Ctrl+W, etc.).
- [ ] **Undo/redo system**: Command pattern for all state-mutating operations.
- [ ] **Plugin System**: Run custom Python scripts on loaded data.
- [ ] **Save derived channel definitions**: Persist ratio/RGB/formula definitions to disk.
- [ ] **Recent Files**: "Open Recent" menu item.
- [ ] **Status indicators**: "Unsaved changes" / "read-only" indicators in status bar.

### Metadata & Info
- [ ] **Search/Filter**: Search box for Metadata and HDF5 Structure trees.
- [ ] **User Preferences**: Save/restore "Expanded Groups" tree state between sessions.
- [ ] **FIB data**: Handle fibTOF data properly.

### Advanced Capabilities (Long Term)
- [ ] **Peak integration**: Show time trace for interactively defined integration window; calculate area.
- [ ] **Multi-file overlay**: Compare spectra or time traces from multiple open files.
- [ ] **Mass calibration viewer**: Visualize/edit calibration curves; plot peak position over time.
- [ ] **Image annotations**: Text, arrows, shapes as toggleable overlays.
- [ ] **2.5D and 3D visualization**: Slider to scroll through coordinates (2.5D) or 3D voxel plot.
- [ ] **Overlay images**: Overlay multiple channels or false-colour images as a new composite view.

---

## Completed Features (Historical)

<details>
<summary>Click to expand and view released features (up to v0.5.x)</summary>

### Core & Integration
- [x] **Root cleanup**: Deleted stale debug/analysis scripts.
- [x] **Tighten imports**: Slimmed PySide6 imports to reduce executable size.
- [x] **Nuitka build script**: Added necessary include-package options and compression for smaller builds.
- [x] **Performance improvements**: Enhanced data loading, plot updates, and image display times using scipy spatial trees over brute-force methods. Threaded Pandas processing.
- [x] **Bundle pyrec from source**: Integrated pyrec directly into icpTOFpy to eliminate the separate installation step.
- [x] **Testing**: Added tests/test_image_export.py.
- [x] **HDF5 dimension reference**: Documented TOFWERK HDF5 dimensions, ordering, and units in icpTOFpy.

### Graphing Tool Features
- [x] **Correlation plot — optional coefficient labels on colour swatches**: New "Show correlation coefficient labels" checkbox in the ⚙ options dialog. *(v0.5.2)*
- [x] **Correlation plot — tick labels and global Lin/Log toggles**: Tick labels suppressed when lower triangle is colour swatches; shown on outer edges for scatter. A single stacked Lin Y / Lin X pair controls all histogram/scatter sub-axes at once. *(v0.5.2)*
- [x] **Channel selector — toggle-state group filters and data source buttons**: Geochemical group buttons are stateful toggles (multi-group union). [Major] narrows to most-abundant isotope per element. Registered data source prefixes (ICAP, TPS2, ODG, …) appear as toggle buttons; [Monitor] filters to monitoring channels. Tooltip strings centralised in `_BUTTON_TOOLTIPS`. *(v0.5.2)*
- [x] **Time Traces — Overlaid mode 3+ unit groups**: Channels with more than two distinct unit groups no longer silently dropped; all plotted on single axis with concatenated label. *(v0.5.2)*
- [x] **Time Traces — Correct Y-axis label for metadata**: Metadata channels with no parseable unit no longer incorrectly labelled "Intensity (counts)". *(v0.5.2)*
- [x] **Heatmap**: Channels × time rendered as a `pcolormesh` heatmap with per-channel normalization and clipping. *(v0.5.0)*
- [x] **Correlation plot**: N×N pairplot-style correlation matrix with pearson/spearman methods. *(v0.5.0)*
- [x] **Histogram / Density distributions**: Overlapping semi-transparent histograms/KDE with lin/log toggles. *(v0.5.0)*
- [x] **Independent Y-axis scaling**: Separate y-axis scale per parameter unit group.
- [x] **Split trace plots**: Stacked subplots in time trace view. Auto-split by unit. *(v0.4.6)*
- [x] **Zoom range buttons**: Overlay buttons to reset x- or y-axis bounds independently.
- [x] **Time as a parameter**: Parameter vs. time plotting in Data plotting panel.
- [x] **Read specific mass spectrum**: Read & display spectrum for a cursor's index via `TwGetTofSpectrumFromH5()`.
- [x] **Stick spectrum**: Render all peak intensities as sticks at the cursor's write/buf position.
- [x] **Time-average spectrum**: Averaged stick and mass spectra bounded by dual time cursors.
- [x] **Export plots & Data**: Save plots (PNG, SVG, PDF) and numerical output (CSV, JSON).

### Imaging & Metadata Features
- [x] **Scale Bar**: Physical scale bar added to images.
- [x] **Title customization**: Custom title text and internal/external placement for imaging plot.
- [x] **Export numerical formats**: Export map arrays or channel lists to CSV/JSON.
- [x] **mipTOF metadata**: Adapted metadata loading system for mipTOF parameter placement.
- [x] **Display full Configuration File Content**: Made text box viewable under Metadata tab.

### File Info
- [x] **Context-menu copy actions**: Right-click any tree item to copy its value, parameter name, or file path; right-click a group header to copy all parameter/value pairs as tab-separated text. *(v0.5.2)*
- [x] **Metadata export**: Export a single group or all metadata to CSV, JSON, HTML, PDF, or PNG. All formats include the filename as a title and group section headings. *(v0.5.2)*
</details>
