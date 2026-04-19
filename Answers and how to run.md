# Answers and how to run

## How to run

From this project folder:

```bash
python3 -m http.server 8000
```

Open in your browser: `http://localhost:8000/DSA301_P3_32417_Bozyel.html`

---

## Written reflections (same text as in `DSA301_P3_32417_Bozyel.html`)

### Task 1 — Part C (written analysis)

**Interactive web map (tile server projection)**  
For an interactive web map of NYC taxi data, the tile server should use **Web Mercator (EPSG:3857)**. Major basemap providers distribute square tiles in this projection, so adopting Mercator avoids costly reprojection, simplifies caching, and keeps the global tile pyramid standard. Users expect street layouts and north-up navigation familiar from everyday web maps. A taxi-density or KDE layer then aligns with the roads beneath it; at roughly 40.7°N local area inflation is moderate, so hotspot patterns remain readable even though areas are not strictly true-scale.

**Printed choropleth (per km²)**  
For a **printed choropleth** of pickup rates per km² across boroughs, I would change to **Albers equal-area conic** (or another equal-area projection for the Northeast). Choropleths invite area comparisons: equal-area guarantees polygon sizes on the page match geographic areas so per-km² values are comparable. Web Mercator would distort relative borough sizes and could mislead readers about which borough truly carries more activity per unit land.

### Task 1 — Part D (projection identification)

**Fig 3 — Projection A**  
(1) **Cylindrical** projection; it is **conformal** (not equal-area or equidistant in the global sense). (2) **Graticule clue:** meridians are evenly spaced, perfectly straight vertical lines and parallels are straight horizontals, but the vertical gap between parallels increases strongly toward the pole; the red Tissot shapes stay *round* while their radius grows dramatically with latitude—local angles preserved, areas inflated poleward (Mercator-type behaviour).

**Fig 4 — Projection B**  
(1) **Conic** projection; it is **equal-area** (not conformal). (2) **Graticule clue:** meridians are straight lines meeting at a common apex above the frame and parallels are curved arcs centred on that axis; Tissot figures are slightly elliptical yet similar in overall size across the map, signalling that *area* is held approximately constant while local shapes vary—typical of an equal-area conic (e.g., Albers-style).

### Task 2 — Part C (written reflection)

**C1 — Zoom behaviour and overlaps**  
When I zoom to level 14 over Midtown, two or three of the top-10 hotspots can still sit close enough on screen that their pill bounding boxes touch or slightly overlap after the four-slot search, especially where neighbouring grid cells share a street canyon. At zoom 12 the same anchors occupy fewer pixels apart relative to the map width, so overlaps become a bit more likely even though the algorithm re-runs on every pan and zoom: each label is re-anchored from fresh container coordinates, overlap tests against other pills and marker footprints are recomputed, and leader lines redraw when a fallback slot is chosen. Because offsets are fixed in pixels from the marker centre, the layout scales mechanically with the map rather than adapting typography to scale. The main weakness at low zoom is that we never collide-test against basemap lettering and we never hide low-rank labels. Useful upgrades would be zoom-dependent padding or font size, suppress ranks 8–10 below a zoom threshold, and optional opacity fade so the OSM layer reads as stable ground while our overlay reads as analytic figure.

**C2 — Figure–ground and hierarchy**  
The OSM basemap already acts as a dense typographic ground—streets, parks, and POIs compete for attention. Our semi-opaque white pills with dark rims and strong halos deliberately pop forward as figure, which helps the taxi counts read quickly but can feel visually loud where the under-map is already busy; the figure-ground tension is highest where teal streets cross high-contrast halos. To sharpen hierarchy I would lighten the pill fill slightly, reduce stroke weight, and thicken the halo only where contrast drops, while lowering overall saturation so basemap browns and greys stay dominant in peripheral vision. I might also switch rank text to a slightly smaller secondary weight and reserve bold solely for the trip count. Finally, a subtle drop shadow tuned to tile luminance (not a uniform white glow) would separate overlay from basemap labels without shouting over them.

### Task 3 — Part B (optimal radius, ≤80 words)

**Best setting: 25 px** (≈950 m at zoom 12 using 38 m/px). At **20 px** Midtown–Chelsea is sharp but speckle from sparse streets remains. **25 px** smooths noise yet keeps Midtown distinct from the Upper East Side ridge and preserves JFK as a separate warm patch. By **40–45 px** peaks merge into one elongated blob; **55–60 px** collapses Manhattan detail so only broad regional structure survives.

### Task 3 — Part C (spatial change across time windows, ≤100 words)

On this April 5 subset inside the bbox, **evening (18:00–21:59)** carries the most pickups; the heatmap stays Midtown-heavy but the centroid shifts slightly west toward Times Square / Theatre District intensity. **Morning (07:00–09:59)** is thinner overall; the warm core tightens and leans a bit south, consistent with fewer discretionary trips and more commuter-style pulls from residential corridors. **Midday (12:00–14:59)** sits between the two in count; spatially it resembles morning but with a touch more Midtown spread, reflecting lunch and business meetings rather than the broader evening entertainment peak.
