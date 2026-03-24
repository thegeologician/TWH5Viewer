# TWH5Viewer v0.5.7

A PySide6-based (Qt for Python) HDF5 viewer application specialised on TOFWERK icpTOF data files, but usable with other (Tofwerk) instrument data files as well.

## Download

[:material-download: **Download TWH5Viewer (Windows .exe)**](https://github.com/thegeologician/TWH5Viewer/releases/latest/download/TWH5Viewer.exe){ .md-button .md-button--primary }

The standalone executable requires no Python installation. Download and run directly on Windows.
Releases and release notes are available on the [GitHub Releases page](https://github.com/thegeologician/TWH5Viewer/releases).

Many of the functionalities demonstrated here can be achieved with the library functions provided by [icpTOFpy](https://thegeologician.github.io/icpTOFpy/). This application is intended as a convenient GUI for users who prefer a graphical interface over scripting.

## Features

- **HDF5 Structure Browser**: Navigate the complete file hierarchy — Groups, Datasets (with shape, dtype, size), and Attributes.
- **Metadata Browser**: Organised, collapsible tree view of instrumental metadata with smart formatting, tooltips, and parameter descriptions.
- **Imaging Tool**:
    - Visualise Laser Ablation (LA) element maps with Circle and Rectangle spot shapes.
    - **Contrast Control**: Adjustable vmin/vmax with auto-percentile calculation, linear and logarithmic colour scaling.
    - **Derived Channels**: Ratio images and RGB false-colour composites with per-channel contrast.
    - **Export Image**: PNG, SVG, PDF, and TIFF with configurable DPI, size, and optional metadata embedding.
    - **Export Data**: Point data (one row per spot, CSV/JSON) or grid matrix (nearest-neighbour interpolation via `scipy.spatial.cKDTree`, configurable cell size).
    - **Clipboard**: Copy the current image to clipboard.
    - **Cursor Info**: Real-time coordinate and data value display in the status bar.
    - **Scale Bar** and **title customisation**.
    - Interactive pan/zoom with Reset. Dark-mode compliant.
- **Graphing Tool**:
    - Mass spectrum visualisation (Sum, Cursor TOF/Stick, time-averaged).
    - Time trace plotting with unit-aware dual y-axes, split-by-unit, and stacked subplot modes.
    - Time-resolved metadata from registered data sources (ICAP, TPS2, ODG, …).
    - Formatted Date/Time x-axis from POSIX timestamps.
    - **Data Plots**: Scatter, Heatmap (channels × time), Correlation (N×N pairplot), and Histogram/KDE — each with independent log/lin toggles and Reset X/Y buttons.
    - **Time Cursor**: Reads exact channel values and buf/write indices at cursor position.
    - **Export Data**: CSV or JSON from Spectrum, Time Trace, and Data Plot panels; relative or POSIX time column options.
- **File Info**: Grouped, collapsible metadata tree with smart value formatting and tooltips.
    - Right-click any item for context-sensitive actions: copy value, copy parameter name, copy file path, or copy an entire group's values (tab-separated).
    - Export a single group or all metadata to **CSV**, **JSON**, **HTML**, **PDF**, or **PNG** — all formats include the filename as a title and group headings.


## License

Proprietary / Internal Use.
