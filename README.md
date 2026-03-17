# TWH5Viewer

A PySide6-based (Qt for Python) HDF5 Viewer application specialized for TOFWERK icpTOF data files.

## Features

- **HDF5 Structure Browser**: Navigate the complete file hierarchy — Groups, Datasets (with shape, dtype, size), and Attributes.
- **Metadata Browser**: Organized, collapsible tree view of instrumental metadata with smart formatting, tooltips, and parameter descriptions.
- **Imaging Tool**: 
    - Visualize Laser Ablation (LA) element maps with Circle and Rectangle spot shapes.
    - **Contrast Control**: Adjustable vmin/vmax with auto-percentile calculation, linear and logarithmic color scaling.
    - **Derived Channels**: Ratio images and RGB false-colour composites with per-channel contrast.
    - **Image Export**: PNG, SVG, PDF, and TIFF with DPI selection and **Adaptive Physical Anchor Layout** to prevent label cropping and ensure deterministic batch alignment.
    - **Numerical Export**: Export spot data as CSV or JSON in two modes — **point data** (one row per spot) or **grid matrix** (nearest-neighbour interpolation onto a user-defined raster via `scipy.spatial.cKDTree`, resolution independent of acquisition spacing).
    - **Clipboard**: Copy the current image to clipboard with one click.
    - **Cursor Info**: Real-time coordinate and data value display in the status bar.
    - **Scaling Modes**: Fit-to-window and 1:1 pixel scaling.
    - Interactive pan/zoom with "Reset" functionality.
    - Dark mode compliant plotting.
- **Graphing Tool**: 
    - Mass spectrum visualization (sum, cursor, and time-averaged).
    - Time trace plotting with unit-aware dual y-axes and **Ultra-fast dynamic min-max decimation** to seamlessly render massive datasets (>5M rows) without UI lag.
    - **Data Plots**: Scatter plot visualization of any parameter against any other parameters.
    - Time-resolved metadata from registered data sources.
    - Formatted Date/Time x-axis from POSIX timestamps.
    - Interactive **independent X/Y log-scale and view reset** overlay buttons.
    - **Numerical Export**: Export spectrum, time-trace, or data-plot data to CSV or JSON. Time columns export as relative seconds or full-precision POSIX timestamps.
- **File Info**: Grouped, collapsible metadata tree with smart value formatting and tooltips.
    - **Context menu**: Right-click any item to copy a value, parameter name, file path, or an entire group's values (tab-separated) to the clipboard.
    - **Export**: Export a single group or all metadata to CSV, JSON, HTML, PDF, or PNG. All formats include the filename as a title and group headings to structure the output.

## Installation

### Prerequisites

- Python 3.9+
- [icpTOFpy](https://github.com/thegeologician/icpTOFpy) (custom library)

### Install Dependencies

```bash
pip install -r requirements.txt
```

*(Note: `icpTOFpy` must be installed separately, usually from a local wheel or git repository)*

## Usage

```bash
python main.py
```

## Application Structure

- **main.py**: Entry point and main application logic (`HDF5Viewer` class).
- **ui_mainwindow.py**: Compiled Qt Designer UI file.
- **dialogs/**: Dialog windows — Plot Options, Export, Derived Channels.
- **utils/**: Utility modules — image export helpers.
- **widgets/**: Custom widgets — `MplWidget` for Matplotlib integration.

## Documentation

Full documentation can be built with MkDocs:

```bash
mkdocs build
```

Or served locally:

```bash
mkdocs serve
```

Current documentation is also available at: https://thegeologician.github.io/TWH5Viewer/


## License

Proprietary / Internal Use.
