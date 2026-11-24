# 🚁 DroneFlightPlanner

**A modular Python library that turns a 2D survey polygon into a 3D, terrain-following drone flight plan, then simulates the photogrammetric image acquisition over real topography.**

By combining Digital Elevation Models (DEMs) with camera sensor physics, DroneFlightPlanner generates lawnmower-style flight paths, drapes them over the terrain, evaluates Ground Sampling Distance (GSD) and image overlap on uneven ground, and renders everything in an interactive 3D scene with satellite-textured terrain.

> Final project for the *Geospatial Processing* course. Built by students, for students (and anyone who wants to plan a UAV survey without heavy dependencies).

---

## ✨ Features

- **Automatic DEM retrieval** for a given area of interest (AOI), or bring your own DSM/DTM. Falls back to Copernicus GLO-30 / GLO-90 when no local data is provided.
- **2D coverage path planning** using the Boustrophedon (lawnmower) algorithm with Boolean vector clipping.
- **Terrain-following 3D flight plans**: waypoint elevations are sampled from the DEM via bilinear interpolation, keeping a constant height above ground.
- **Photogrammetric quality metrics**: per-image GSD, forward overlap, sidelap and number of images covering each ground point.
- **Interactive 3D visualization** (PyVista): textured terrain, hovering flight trajectory and oriented, translucent camera frustums (pyramids).
- **GIS-ready exports**: 3D flight vectors as `.gpkg` and coverage/GSD statistics as `.csv`.
- **Low dependency footprint**: no machine learning stack required.

---

## 🧭 How it works

The planner runs as a simple pipeline:

| Step | Description |
|------|-------------|
| **0. Obtain DEM** | Fetch (or load) the DEM that covers the input polygon (ROI). |
| **1. 2D flight plan** | Compute the lawnmower trajectory over the polygon, ignoring terrain. |
| **2. Add Z coordinate** | Use the DEM to define flight altitude and georeference the plan. |
| **3. Compute metrics** | Average GSD per image, overlaps, number of images per grid point. |
| **4. Visualize** | Plot the flight plan and camera pyramids in 3D over the terrain. |

### Geospatial algorithms & foundations

- **Computational geometry (Boustrophedon)**: parallel sweep lines are generated across the polygon's bounding envelope and clipped against the survey polygon with `shapely` to build an optimized back-and-forth trajectory.
- **Bilinear raster interpolation**: `scipy.interpolate.RegularGridInterpolator` samples continuous ground elevation from the discrete DEM grid at arbitrary (X, Y) waypoint locations.
- **Vector topology & photogrammetry**: headings (azimuths) between consecutive waypoints orient the rectangular camera footprints, while GSD, forward overlap and sidelap are computed from the local height above ground.
- **CRS warping & UV texture mapping**: Web Mercator (EPSG:3857) satellite tiles are reprojected into the local projected CRS (e.g. UTM) and draped as RGB texture over a triangulated PyVista surface mesh.

---

## 📥 Inputs & 📤 Outputs

### Inputs

| Input | Details |
|-------|---------|
| **Survey area (AOI)** | Vector polygon (`.gpkg` / `.geojson`) |
| **Elevation data** | User-supplied DSM/DTM (GeoTIFF) for the AOI, otherwise automatic fallback to GLO-30 / GLO-90 |
| **Camera parameters** | Focal length (principal distance), sensor size (physical), sensor size in pixels |
| **Flight design parameters** | Relative flight height, longitudinal (forward) overlap, lateral overlap (sidelap), line spacing, photo interval |

### Outputs

- 🌍 Interactive PyVista 3D scene (textured terrain, flight trajectory, oriented camera frustums)
- 🗺️ 3D GIS flight vectors (`.gpkg`)
- 📊 Coverage and GSD quality statistics (`.csv`)

---

## ⚠️ Assumptions & limitations

- A DEM must be available for the area. If no DEM service can be queried automatically, the solution is restricted to a predefined DEM and a specific zone.
- The flight area is assumed to be **free of cables and other obstacles that are not visible in the DSM**. Obstacle avoidance is the operator's responsibility.
- This is an academic project and **not a certified flight-planning tool**. Always follow local UAV regulations and perform a proper on-site safety check.

---

## 🚀 Installation

> ⚠️ Early stage: the source files are still empty stubs and there is no `requirements.txt` yet.

```bash
git clone https://github.com/JoseVilaseca/DroneFlightPlanner.git
cd DroneFlightPlanner
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install shapely numpy scipy rasterio geopandas pyproj pyvista
```

**Main dependencies:** `shapely`, `numpy`, `scipy`, `rasterio`, `geopandas`, `pyproj`, `pyvista`

---

## 📁 Project structure

```
DroneFlightPlanner/
├── docs/
│   └── Drone Flight Planner - Project Outline.pdf   # project concept, inputs, processing steps
├── source/
│   ├── fligh_plan.py     # 2D lawnmower plan, Z computation, metrics (stub)
│   ├── terrain.py        # DEM retrieval, Z sampling, georeferencing (stub)
│   ├── visualization.py  # PyVista scene, texture mapping, frustums (stub)
│   └── test-polygons/    # sample AOIs (GeoJSON): Belvedere, Forni, Lys, Miage glaciers
├── LICENSE
└── README.md
```

---

## 🗺️ Roadmap

- [x] Project outline and algorithm selection (see `docs/`)
- [x] Test polygons (four Alpine glaciers)
- [x] Source module structure (stubs)
- [ ] DEM retrieval for an input polygon
- [ ] 2D lawnmower coverage planner
- [ ] Terrain-following Z computation and georeferencing
- [ ] GSD, overlap and images-per-point metrics
- [ ] 3D visualization with textured terrain and camera pyramids
- [ ] GPKG / CSV export
- [ ] Documentation, examples and `requirements.txt`

---

## 👥 Authors


- Alfonso Mijangos <a href="https://github.com/Mijanguitos"><img src="https://cdn.simpleicons.org/github/808080" height="16" alt="GitHub"></a> <a href="https://www.linkedin.com/in/alfonso-mijangos/"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linkedin/linkedin-original.svg" height="16" alt="LinkedIn"></a>
- Andrea Ardu <a href="https://github.com/arduandrea"><img src="https://cdn.simpleicons.org/github/808080" height="16" alt="GitHub"></a> <a href="https://www.linkedin.com/in/andreaardu/"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linkedin/linkedin-original.svg" height="16" alt="LinkedIn"></a>
- Jose Luis Vilaseca <a href="https://github.com/JoseVilaseca"><img src="https://cdn.simpleicons.org/github/808080" height="16" alt="GitHub"></a> <a href="https://www.linkedin.com/in/jose-luis-vilaseca/"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linkedin/linkedin-original.svg" height="16" alt="LinkedIn"></a>

## 📄 License

Released under the [MIT License](LICENSE).