# Roadmap

This document tracks planned features and enhancements for TWH5Viewer.
Known bugs and issues are tracked in [BUGS.md](BUGS.md).

> Priority ordering is determined by a combination of **user impact** and **implementation effort**.

---

## v0.5.x (Current version — 0.5.7)
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

### App Structure & Export
- [x] **Image export completeness**: Ensure `image_export.py` supports every option exposed in the Export dialog.

### icpTOFpy
- [x] **Better TPS2 parameter names**: Translate names of TPS2 parameters to be more user-friendly. Requires lookup table.
- [x] **Implement is_Quantistar()**: New metadata function and integration into the metadata loading system.

### File info
- [x] **Copy values**: Add "Copy value" and "Copy path" options to context menu. Copy whole branch/group when called on a group header. *(v0.5.2)*
- [x] **File path**: Add "Copy file path" option to context menu. Either make file path available in the metadata tree or add it to the file info dialog or show the file name and the "copy path" option globally. *(v0.5.2)*
- [x] **Export metadata**: Add "Export metadata" option to context menu. Export selected group or currently expanded groups or the whole metadata to a CSV file, JSON file, or a nicely formatted table as html, pdf, or png. *(v0.5.2)*

---

## v0.6.0 (UX and Documentation)
*Focus: Improve UX, documentation, and export completeness.*

### Documentation and UX
- [ ] **Online Docs**: Expand Usage information with more explanations of all the different options and screenshots.
- [x] **Better Tooltips**: Descriptive tooltips added to all interactive controls (toolbar actions, channel selectors, plot-type combos, copy/export/reset buttons, search boxes, preferences theme selector).
- [x] **Help option**: "Documentation" entry in the About menu (F1 shortcut) opens the online docs in the system browser.
- [x] **Better "About" dialog**: Rich About dialog with TOFWERK logo, version number, description, and quick-links to docs and GitHub.
- [x] **App icon**: ICO bundled and embedded in the exe via `--windows-icon-from-ico`; shown in File Explorer and title bar. *(Taskbar icon and splash screen do not work in Nuitka onefile mode due to bootstrapper/subprocess architecture — shelved.)*
- [x] **App-wide theme**: System default / dark / light theme for the whole Qt application (menus, dialogs, and plot backgrounds), independent of the per-plot style set in Plot Options.
- [x] **Basic keyboard shortcuts**: Ctrl+O (Open file), Ctrl+W (Close file), F1 (Documentation).
- [x] **Search/Filter**: Search box for Metadata and HDF5 Structure trees.
- [x] **User Preferences**: Save/restore "Expanded Groups" tree state between sessions.

### Graphing Tool
- [ ] **Automatic labels**: String metadata (scan/sample names) plotted along the time axis.
- [ ] **Time range quick stats**: When a time range is selected in time trace view, show quick stats (min, max, mean, std, median, count) for each selected channel in the info box.
- [ ] **Performance improvements**: Implement decimation for the heatmap, similar to the approach for the time traces outlined in [Performance Optimizations](plot_performance_optimizations.md). Is something similar possible for the other plots, is it sensible? For scatter plot or histogram plot, maybe sub-sample to manageable amount of randomly sampled data points.
- [ ] **Bulk-selection of channels**: Allow for bulk-selection of channels in the channel selector (select one, Shift-select the last, allow to toggle all in between).

### Imaging Tool
- [ ] **Interpolated output extent**: Clip numerical output and interpolated image export to the extent of the actual spot locations + 1/2 spot size.
- [ ] **Interpolated image export**: Export an interpolated image where pixel values are interpolated between raw data points. Include interpolation method (bilinear, bicubic) and resolution options.
- [ ] **Multi-channel images**: Export a selection of channels as a multi-channel image to TIFF, layers to SVG/PDF, or GIF.
- [ ] **Export layers**: SVG/PDF/TIFF export with image and decorations in separate layers.
- [ ] **Contrast histogram**: Display pixel values as an interactive histogram with draggable vmin/vmax handles.
- [ ] **Colour scale for false-colour images**: Show RGB colour scale as three separate bars or a mixing-triangle.
- [ ] **Editing RGB channels**: Buttons to rotate (RGB→GBR) or swap (RGB→RBG) channel assignment.
- [ ] **Colorblind-safe colormaps**: viridis, cividis, batlow, greyscale.

### App Structure & Export
- [ ] **Standalone image export CLI**: Finalize the `image_export.py` `__main__` CLI block so it works end-to-end as a standalone command-line tool.
- [ ] **Document stand-alone image export CLI**: Add documentation for the stand-alone image export CLI in Readme, and in the help menu on the website.
- [ ] **Check if image export could be built into a stand-alone executable**: Check what additional data loading and processing logic would be required to make the image export functionality available as a stand-alone executable. Check library dependencies and executable build size.

### icpTOFpy
- [ ] **Performance improvements**: Implement performance improvements for `icpTOFpy`.
- [ ] **Instrument lookup**: Implement a function to translate ADQ serial number to instrument serial number. Needs lookup table, that should NOT be included in general distribution. If lookup table is given, return instrument serial no, if not given/present, return nothing, gracefully.
- [ ] **DAQ hardware**: Add support for different DAQ hardware (ADQ1600, ADQ14, ...) — include into metadata system.
- [ ] **Better error messages**: Improve error messages to be more user-friendly. Review all error messages and add more context and helpful information. Review default logging levels and messages.

### File Tool
- [ ] **Better overview**: Show status of the various "is_...()" functions more graphically, only show instruments that apply to the current file. Maybe include instrument logos for different instruments types, and a table of tick marks / crosses for the "is_..." functions. - Needs to also work in metadata exports (CSV, HTML, PDF, PNG).

---

## v0.7.0 (Enhanced data querying and analysis)
*Focus: derived channels, statistics, ROIs, and cross-tool parameter exchange.*

### Graphing Tool
- [ ] **Derived channels**: Formula-based derived channels (arithmetic expressions between any combination of channels) usable as time traces, scatter/histogram axes, and spatial maps. Replaces and extends current hard-coded ratio channels; definitions are freely exchangeable between Graphing and Imaging Tools.
- [ ] **Statistics Data Plot**: New Data Plot type — "Statistics" to show currently selected channels as a labelled box plot.
- [ ] **Regression lines**: Option to put a regression line, optionally with correlation coefficient and R-squared value, on scatter plots.

### Analysis Features
- [ ] **Laser metadata**: Make scan names, sample types, and laser spot types available as time-resolved data and spatial data. *(Prerequisite for ROI by scan/sample.)*
- [ ] **ROI Tool**: Draw Rectangle/Circle/Polygon ROIs; extract statistics and spectra. Multi-ROI comparison.
- [ ] **Histogram ROI**: Pixel intensity histogram for the active peak (ROI "full map" option).
- [ ] **ROI by scan/sample**: Auto-generate ROIs from laser metadata (one ROI per scan/sample). *Requires Laser metadata.*

---

## v1.0.0 (Architecture & QoL)
*Focus: Overall optimizations, usability polish, and robust state management.*

### App Structure
- [ ] **Settings Dialog**: UI for application-wide defaults (colormap, spot shape, theme, etc.) and default presets for all the individual options dialogs.
- [ ] **Recent Files**: "Open Recent" menu item.
- [ ] **Active Channel Filter**: Global "Active Channels" filter visible in all tools/exports (option to hide or not load them).
- [ ] **Undo/redo system**: Command pattern for all state-mutating operations.

### icpTOFpy
- [ ] **Extract upstream data logic**: Migrate pixel filtering, column extraction, and ratio computation from `TWH5Viewer` into `icpTOFpy`.

---

## v1.1.0 (Data analysis features)
*Focus: Data analysis features and application enhancements.*

### App Structure
- [ ] **Save / Load Workspace**: Save open file, selections, ROIs, derived channels to a file and restore.
- [ ] **Save derived channel definitions**: Persist ratio/RGB/formula definitions to disk.
- [ ] **Status indicators**: "Unsaved changes" / "read-only" indicators in status bar.
- [ ] **Plugin System**: Run custom Python scripts on loaded data.

### Advanced Capabilities (Long Term)
- [ ] **Peak integration**: Show time trace for interactively defined integration window; calculate area.
- [ ] **Multi-file overlay**: Compare spectra or time traces from multiple open files.
- [ ] **Mass calibration viewer**: Visualize/edit calibration curves; plot peak position over time.
- [ ] **Image annotations**: Text, arrows, shapes as toggleable overlays.
- [ ] **Overlay images**: Overlay multiple channels or false-colour images as a new composite view.
- [ ] **Segmented Image data**: Handle Image data with an additional dimension, usually in Segments.
- [ ] **FIB Images**: Handle fibTOF images properly.
- [ ] **2.5D and 3D visualization**: Slider to scroll through coordinates (2.5D) or 3D voxel plot.

---

## Continuous Tasks
*These items have no fixed target version and are worked on incrementally as opportunities arise.*

- **More instruments and TPS parameters** *(icpTOFpy)*: Extend the range of recognised instruments (Vocus range, EI?, Quantistar variants) and add more instrument-specific TPS parameters. Standardise the way the masterlists are generated from `tps1rc.cfg` files.

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
