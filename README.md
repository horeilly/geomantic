# Sigil

[![Tests](https://github.com/horeilly/sigil/workflows/Tests/badge.svg)](https://github.com/horeilly/sigil/actions)
[![Coverage](https://codecov.io/gh/horeilly/sigil/branch/main/graph/badge.svg)](https://codecov.io/gh/horeilly/sigil)
[![PyPI](https://img.shields.io/pypi/v/sigil.svg)](https://pypi.org/project/sigil/)
[![Python](https://img.shields.io/pypi/pyversions/sigil.svg)](https://pypi.org/project/sigil/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## TL;DR
A Python package to approximate an irregular polygon as a set of circles, with optional auto-detection of optimal circle count. Accepts standard polygon input, produces both raw circle data and a visualization, and is engineered with DevOps hygiene suitable for portfolio/demo use.

## Installation

```bash
pip install sigil
```

## Quick Start

```python
from sigil import pack_polygon, visualize_packing

# Define a polygon (list of coordinate tuples)
polygon = [(0, 0), (2, 0), (2, 1), (0, 1)]

# Pack circles into the polygon (auto-detect optimal count)
circles = pack_polygon(polygon)

# Visualize the result
visualize_packing(polygon, circles)
```

**For geographic data (lat/lon coordinates):**

```python
# Use projection for geographic coordinates
circles = pack_polygon(
    polygon,
    n=5,  # Or None for auto-detection
    use_projection=True  # Ensures circles stay circular on Earth's surface
)
```

## Architecture

Sigil uses a modular architecture with four main components:

```
sigil/
├── core.py          # Main API: pack_polygon() orchestrates the pipeline
├── optimizer.py     # PyTorch-based optimization engine
├── projection.py    # Geographic coordinate transformations (WGS84 ↔ UTM)
└── visualization.py # Plotting and output utilities
```

**Key modules:**

- **`core.py`**: Main entry point with `pack_polygon()` function. Handles input parsing, projection setup, normalization, and result formatting.

- **`optimizer.py`**: Contains the differentiable rendering optimization:
  - `CircleModel`: PyTorch neural network with learnable circle positions and radii
  - `DifferentiableRenderer`: Rasterizes polygons to binary masks
  - `optimize_circles()`: Gradient descent optimization with IoU loss
  - Smart initialization samples circle positions inside the polygon
  - Containment and repulsion penalties prevent circles from escaping or collapsing

- **`projection.py`**: Coordinate transformation utilities:
  - `MetricProjector`: WGS84 ↔ UTM transformations with auto-zone detection
  - `MetricNormalizer`: Scales coordinates to [0,1] normalized space for optimization

- **`visualization.py`**: Visualization tools including `visualize_packing()` and `print_circle_summary()`

**Optimization pipeline:**
```
Input Polygon → [Projection to UTM] → Normalization →
PyTorch Optimization (IoU + Containment + Repulsion) →
Denormalization → [Projection back to WGS84] → Circle Results
```

## Goals

### Business Goals
- Demonstrate technical breadth as a Data Science leader, with publicly accessible, well-engineered projects.
- Build a reusable codebase for geometry/optimization problems.
- Showcase DevOps workflow, deployment automation, and code quality standards.

### User Goals
- Import and run the package for polygon-to-circle packing.
- Easily get both numerical results and visuals for use in demos, notebooks, or documentation.
- Optionally vary key parameters (like number of circles).

### Non-Goals
- No real-time web app for external users (yet).
- No billing, authentication, or commercial features.
- No support for highly performant large-scale geospatial datasets.

## User Stories
- As a Data Scientist, I want to fit circles to arbitrary polygons, so that I can demonstrate geometric optimization in presentations.
- As a developer, I want to import this as a Python library, so that I can integrate it into Jupyter notebooks or pipelines.
- As a hobbyist, I want to visualize the packing, so that I can experiment and tweak inputs for different shapes.

## Functional Requirements
- Core Algorithm (Priority: High)
  - Accept input polygon (list of coordinates or GeoJSON).
  - Accept optional n; otherwise autodetect using suitable metric (e.g., elbow method).
  - Grid-based differentiable rasterization for circle fitting.
  - Compute suitable initial centroids.
- API/Interface (Priority: High)
  - Python function/class interface.
  - Output: Array of dictionaries, each with radius, centroid_x, centroid_y.
- Visualization (Priority: Medium)
  - Function to generate an image (matplotlib, etc.) showing polygon outline and circles.
- Packaging (Priority: High)
  - Installable via pip (or equivalent, e.g., Poetry support).
- Testing & Code Quality (Priority: High)
  - Unit tests for inputs/outputs, edge cases, and algorithm steps.
  - Automated linting and type-checking, e.g., with flake8/black/pylint and mypy.
- DevOps & Deployment (Priority: Medium)
  - GitHub Actions to run tests/linting on commit/push.
  - (Optional) Terraform/GCP scripts for potential future hosting.

## User Experience
- User installs via pip.
- Imports in notebook/script: from sigil import pack_polygon
- Calls main function, passing polygon and options.
- Receives output (list of circles), optionally calls viz function for output.
- Views resulting image in notebook or as file.

## Technical Considerations
- Python-first; minimal non-Python dependencies.
- Codebase ready for CI (GitHub Actions).
- Focus on modularity/testability: split main algorithm from I/O and viz.
- Type hints and docstrings throughout.
- Testing: Use pytest; test basic input validation, algorithm output, and (if feasible) some image output.

## Success Metrics
- All primary features covered by automated tests and CI.
- Can be imported and run in a clean virtualenv.
- Generates accurate results for at least 3 "typical" test polygons.
- README/docs clear enough for DS/engineering peers to use and understand.
