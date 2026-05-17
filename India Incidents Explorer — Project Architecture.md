# India Incidents Explorer — Project Architecture

**ArcGIS Maps SDK for JavaScript 4.30 · Vite · Gemini AI · Groq AI**

---

## Project Overview

India Incidents Explorer is an interactive web GIS application that visualises major historical incidents across India — aviation accidents, natural disasters, terror attacks, industrial disasters, transport accidents, stampedes, and more. Built as a professional portfolio project demonstrating production-level ArcGIS Maps SDK development.

---

## Technology Stack

| Layer | Technology | Role |
|---|---|---|
| Mapping Engine | ArcGIS Maps SDK for JavaScript 4.30 | All map rendering, layers, widgets |
| Build Tool | Vite 5 | Bundling, tree-shaking, env vars |
| Primary AI | Google Gemini Flash 2.0 | Natural language + map control |
| AI Fallback | Groq Llama 4 Scout | Redundancy when primary is unavailable |
| Data Platform | ArcGIS Online FeatureService | Hosted, editable incident dataset |
| Reference Data | India Living Atlas (Esri India) | 10 contextual data layers |

---

## Source File Architecture

```
src/
├── main.js          Entry point — landing page wiring, lazy map load trigger
├── config.js        Single source of truth — all URLs, categories, panel IDs
├── map.js           Map + MapView creation, module orchestration
├── layers.js        Layer factories, renderer, popup template, label classes
├── widgets.js       All 11 SDK widget initialisations
├── panels.js        Panel toggle, close, accordion behaviour
├── filter.js        Client-side + server-side filter logic
├── dashboard.js     Reactive stat watchers, status bar updates
├── chat.js          AI chat UI — bubbles, input, chips, map action parser
├── aiService.js     API clients — Gemini Flash + Groq fallback
├── incidentData.js  Dataset loader + AI context builder
├── mapActions.js    AI-driven map control (zoom, filter, layer toggle)
└── style.css        All visual styles — zero inline styles in JS
```

---

## Module Dependency Graph

```
main.js
 ├── map.js            ← lazy-loaded only when user clicks "Open Dashboard"
 │    ├── layers.js
 │    ├── widgets.js
 │    ├── filter.js
 │    ├── dashboard.js
 │    └── mapActions.js
 ├── panels.js         ← loaded immediately, no ArcGIS dependency
 └── chat.js           ← loaded immediately, map-independent
      ├── aiService.js
      ├── incidentData.js
      └── mapActions.js  ← dynamic import() — resolves after map is ready
```

The map module is lazy-loaded so the landing page has zero ArcGIS dependency and loads instantly. `mapActions.js` is imported dynamically inside `chat.js` to avoid a circular dependency (chat → mapActions → map → chat).

---

## Layer Architecture

### Primary Layer — India Major Incidents

| Property | Value |
|---|---|
| Type | `FeatureLayer` (ArcGIS Online FeatureService) |
| URL | `services3.arcgis.com/kUMDRea48DPDJcqA/.../india_major_incidents/FeatureServer/0` |
| Geometry | Point |
| Initial View | Center `[78.9629, 20.5937]`, Zoom `5` |
| Default Basemap | `topo-vector` (overridden by BasemapGallery) |
| Renderer | Unique Value — one PNG icon per category |
| Fields | Incident_Name, Date, State, District, Incident_Category, Fatalities, Injured, Description |
| Capabilities | Query, Edit (add / update / delete), Filter |

**10 incident categories, each with a distinct icon:**
Aviation Accident · Fire Disaster · Industrial Disaster · Industrial Explosion · Infrastructure Failure · Natural Disaster · Stampede · Terror Attack · Transport Accident · Violence / Arson

### Reference Layers — India Living Atlas (10 layers)

All start hidden (`visible: false`). Users toggle them via the Layers panel. All are `MapServer` endpoints served as `MapImageLayer`.

| Layer | Title in App | Opacity | Server |
|---|---|---|---|
| Accidental Deaths by Age & Gender (2022) | `Accidental Deaths by Age & Gender (2022)` | 0.70 | `livingatlas.esri.in/server` |
| Violent Crimes by State (2010–2022) | `Violent Crimes by State (2010–2022)` | 0.65 | `livingatlas.esri.in/server1` |
| Cyber Crimes Against Women by State (2022) | `Cyber Crimes Against Women by State (2022)` | 0.65 | `livingatlas.esri.in/server1` |
| City-wise Suicide Incidence (2020–23) | `City-wise Suicide Incidence (2020–23)` | 0.90 | `livingatlas.esri.in/server` |
| District-Wise Forest Fire Detected | `District-Wise Forest Fire Detected` | 0.70 | `livingatlas.esri.in/server1` |
| India Flood Inventory 1967–2023 | `India Flood Inventory 1967–2023` | 0.65 | `livingatlas.esri.in/server1` |
| Road Accidents by State (Urban & Rural) | `Road Accidents by State (Urban & Rural)` | 0.70 | `livingatlas.esri.in/server` |
| Airports in India | `Airports in India` | 0.90 | `livingatlas.esri.in/server1` |
| Indian Smart Cities | `Indian Smart Cities` | 0.90 | `livingatlas.esri.in/server` |
| India State Boundary 2024 | `India State Boundary 2024` | 0.35 | `livingatlas.esri.in/server` |

> Layer stack order in `config.js` is bottom → top. The LayerList widget displays them top → bottom, so `India State Boundary 2024` (last in config) appears directly under the primary incident layer in the panel.

### Sketch Layer

`GraphicsLayer` — client-side only, `listMode: "hide"`, used exclusively by the Sketch widget for user annotations.

---

## SDK Widgets Used (11 total)

| Widget | Mounted In | Custom Behaviour |
|---|---|---|
| `Search` | Header bar | Geocoding — pan map to address/city |
| `LayerList` | Layers panel | Custom filter + label sub-panels injected per layer row |
| `Legend` | Legend panel | Auto-built from unique-value renderer |
| `BasemapGallery` | Basemap panel | `LocalBasemapsSource` with 10 curated basemaps |
| `Editor` | Edit panel | Coded-value domain injected at runtime → dropdown |
| `Sketch` | Sketch panel | Draws to dedicated `GraphicsLayer` |
| `DistanceMeasurement2D` | Measure panel | Programmatic clear on panel close |
| `AreaMeasurement2D` | Measure panel | Programmatic clear on panel close |
| `Zoom` | Left toolbar | Standard +/− zoom buttons |
| `Home` | Left toolbar | Driven programmatically — `home.go()` |
| `Locate` | Left toolbar | Driven programmatically — `locate.locate()` |

---

## Key Technical Implementations

### 1. Dual-Mechanism Filter System

Two filter mechanisms applied simultaneously to different targets:

```
User selects a category
        │
        ├── FeatureLayerView.filter = new FeatureFilter({ where })
        │   └── instant client-side — hides markers with no network request
        │
        └── FeatureLayer.definitionExpression = where
            └── server-side — ensures queries and AI context reflect filtered data
```

### 2. AI Map Control via Embedded Directives

The AI system prompt instructs the model to append a JSON action block at the end of any reply that should control the map:

```
AI reply text...  {"action":"zoom","incident":"Bhopal"}
                  {"action":"filter","category":"Terror Attack"}
                  {"action":"layer","name":"Forest Fire","visible":true}
```

`_parseAndExecuteAction()` in `chat.js` extracts and strips the JSON block, then delegates to `mapActions.js` via dynamic import. The user sees clean text; the map responds.

### 3. Reactive Dashboard

`view.watch()` subscribes to `center`, `zoom`, and `scale` property changes. The status bar updates in real time as the user pans and zooms. Incident counts use `queryFeatureCount()` with the current `view.extent` as a spatial geometry filter.

---

## Data Flow Summary

### Map Initialisation

```
Click "Open Dashboard"
  → dynamic import of map.js (ArcGIS SDK loads here)
  → esriConfig.apiKey set, IdentityManager patched
  → createIncidentLayer() + createRefLayers() + createSketchLayer()
  → layer.load() awaited → schema read from server
  → renderer + labels + popup + coded-value domain applied
  → new Map() + new MapView()
  → view.when() → widgets initialised, watchers attached, layers registered
```

### AI Chat Request

```
User message
  → loadIncidentDataset() (cached after first call)
  → buildIncidentContext() → compact JSON of all features + stats
  → askAssistant() → Gemini → or Groq fallback
  → _parseAndExecuteAction() → strip JSON block, execute map action
  → _stripMarkdown() → clean plain text
  → display reply + action confirmation
```

---

## Design Decisions

| Decision | Rationale |
|---|---|
| Lazy-load ArcGIS SDK | Landing page loads instantly; SDK (2–4 MB) only downloads on demand |
| CSS classes for panel visibility | Enables CSS transitions; keeps all visual rules in style.css; no inline styles |
| `config.js` as single source of truth | Adding a category or panel requires editing one file only |
| Dynamic `import()` for mapActions | Breaks chat → mapActions → map → chat circular dependency |
| Dual filter (client + server) | Client-side for instant UX; server-side for accurate counts and AI context |
| Markdown stripping in chat | AI returns markdown by default; plain text looks cleaner in a chat UI |
| Pure factory functions in layers.js | No side effects; each layer is created fresh from config; easy to test |
| Delegated event listeners for panels | One listener handles all 8 close buttons; new panels need only HTML markup |

---

*India Incidents Explorer · ArcGIS Maps SDK for JavaScript 4.30 · v10.0 · 2026*
