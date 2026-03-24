# Usage Guide

## Starting the Application

Run the Windows .exe file. You can also open a file by dragging and dropping it onto the application window or by using the File > Open with... menu.

## Interface Overview

The application interface is divided into several main areas:

1.  **HDF5 Structure Browser**: On the left, this tree view allows you to explore the raw HDF5 file structure — Groups, Datasets (shape, type, size), and Attributes.
2.  **Metadata Browser**: Also on the left (tabbed), this provides a parsed, formatted, and grouped view of file metadata with tooltips.
3.  **Main View Area**: The central part of the window where different tools (Imaging, Graphing, File Info) are displayed.

## Tools

### Imaging Tool

The Imaging Tool is designed for visualizing Laser Ablation (LA) element maps.

-   **Navigation**: Use the mouse to pan and zoom the image. Draw a rectangle to zoom to a region.
-   **Reset View**: Double-click on the image or use the "Reset" button to return to the full view.
-   **Peak Selection**: Choose an isotope peak from the dropdown to visualize its intensity map.
-   **Scaling Modes**: Toggle between Fit-to-window and 1:1 pixel scaling using toolbar buttons.
-   **Contrast Control**: Open Plot Options to adjust:
    -   **vmin/vmax**: Set manually or use Auto (1st/99th percentile). The auto-computed values are displayed in the fields and become editable when Auto is unchecked.
    -   **Color Scale**: Switch between linear and logarithmic scaling.
    -   **Clip Mode**: Choose between clamping to bounds or showing out-of-range values as transparent.
-   **Derived Channels**: Create ratio images (element A ÷ element B) or RGB false-colour composites from three channels.
-   **Export Image**: Export images as PNG, SVG, PDF, or TIFF. Configure size (plot area), DPI, scope (current view or full map), and metadata embedding. The dialog shows the true export size including axes and labels.
-   **Export Data**: Under the Export button dropdown, choose "Export Data…" to save numerical data as CSV or JSON:
    -   **Point data** mode: exports one row per measurement spot with X/Y coordinates and all selected channels. Suitable for importing into other analysis tools.
    -   **Grid matrix** mode: interpolates selected channels onto a regular rectangular raster at a user-specified cell size (µm/cell). The output resolution is independent of the original acquisition spacing and works for arbitrary point locations. Uses `scipy.spatial.cKDTree` nearest-neighbour lookup for performance on large datasets. Multi-channel CSV exports produce one file per channel. Both modes support configurable separator, decimal mark, and float precision.
-   **Clipboard**: Copy the current view to the clipboard using the toolbar button.
-   **Cursor Info**: Moving the mouse over the image shows the X/Y coordinates and data value in the status bar.

### Graphing Tool

The Graphing Tool provides visualization for mass spectra and time traces.

-   **Mass Spectrum**: Displayed in the upper plot, showing intensity vs. mass-to-charge ratio. Switch between Sum, Cursor, and time-averaged variants using the dropdown.
-   **Time Traces**: Select one or more traces from the dropdown to plot in the lower panel.
    -   Isotope peaks and time-resolved metadata channels are available.
    -   The x-axis shows formatted Date/Time from POSIX timestamps.
    -   The y-axis adapts its label to show channel names and units.
    -   A secondary y-axis appears automatically when traces with different units are selected.
    -   **Navigation**: Double-click the plot to reset both axes. Alternatively, use the on-plot **Reset X (↔)** and **Reset Y (↕)** buttons to independently autoscale an axis back to the full data extent.
-   **Data Plots**: A third panel below Time Traces provides multi-channel analytical plots. Select a plot type from the **Type** dropdown, choose channels in the **Y** selector, and press **Update Plot** (or enable **Auto-update**). The **⚙** button opens per-type options.

    | Type | Description |
    |---|---|
    | **Scatter** | X vs Y scatter for any combination of channels. Supports log/lin on both axes. |
    | **Heatmap** | Channels × time as a colour-mapped grid. Each channel is normalised independently to [0, 1]. Colour scale clips at 1st/99th percentile by default (configurable). Zoom is constrained to the time axis — the channel axis is discrete. |
    | **Correlation** | N×N pairplot matrix showing Pearson or Spearman r between all selected channels. Diagonal, lower, and upper zones can each show colour swatches, density histograms, or scatter plots. |
    | **Histogram** | Selected channels plotted as overlapping semi-transparent distributions for direct comparison. Switch between bar histograms and kernel-density (KDE) curves. Log-spaced bins are used automatically in log-X mode. Lin/log toggles active on both axes. |

    *(Note: Data Plots and Time Traces support independent Log/Lin toggles and Reset X/Y bound buttons overlaid directly on the plotting canvas).*

-   **Export Data**: Each plot panel (Spectrum, Time Trace, Data Plot) has an "Export Data…" option in the Export button dropdown. Data is saved as CSV or JSON.
    -   For Time Trace and Data Plot (when time is the x-axis), a **Time Column** option lets you choose between:
        -   **Relative time** (`time_relative_s`): seconds elapsed from the first sample — recommended for most analysis.
        -   **POSIX timestamp** (`time_posix_s`): absolute float64 seconds since the Unix epoch, written as 9-decimal fixed-point in CSV to preserve full sub-microsecond precision.

### File Info

Displays a collapsible, grouped metadata tree populated from the loaded HDF5 file. Parameters are organised into named sections (e.g. *General*, *Image*, *Laser*, *Configuration*), shown with long descriptive names, smart value formatting (units appended, floats trimmed), and per-parameter tooltips where available.

The file path is always shown as the first row. Groups that are most relevant to the detected file type are expanded automatically on load.

#### Context Menu

Right-clicking any item opens a context-sensitive menu:

| Click target | Available actions |
|---|---|
| File path row | **Copy file path** |
| Group header | **Copy group values** (all parameters as tab-separated text), **Export group…**, **Export all metadata…** |
| Leaf parameter | **Copy value**, **Copy parameter name**, **Export group…**, **Export all metadata…** |
| Background | **Export all metadata…** |

#### Metadata Export

"Export group…" and "Export all metadata…" open a Save dialog with five format options:

| Format | Output |
|---|---|
| **CSV** | `# File: <filename>` comment on line 1. Columns: Group, Parameter, Value. A group-label row (with empty Parameter/Value) introduces each section. |
| **JSON** | `{"file": "<filename>", "groups": {"GroupName": [{"parameter": …, "value": …}, …], …}}` |
| **HTML** | Self-contained styled page. Filename as `<h1>`. Each group as an `<h2>` heading followed by a two-column table. |
| **PDF** | Matplotlib-rendered table. Bold filename title above the table. Light-blue group header rows; alternating white/light-grey data rows. Figure height calculated from row count — no wasted whitespace. |
| **PNG** | Same as PDF, saved as a raster image at 150 dpi. |
