# India Incidents Explorer

**Interactive web map of major historical incidents across India.**

Built with the ArcGIS Maps SDK for JavaScript 4.30.

---

## What is this app?

India Incidents Explorer is an open, public web application that lets you explore, filter, and analyse major incidents that have occurred across India — including aviation accidents, natural disasters, terror attacks, industrial explosions, transport accidents, and more.

Every incident is plotted on an interactive map with a distinct icon for its category. Click any point to see full details: name, date, state, district, fatalities, injuries, and a description.

---

## Features

### Interactive Map
- Smooth pan and zoom across all of India
- 10 incident categories, each with a unique icon symbol
- Click any incident marker to open a detailed popup
- Animated zoom to India's full extent with the Home button
- GPS locate button — centres the map on your current position

### Filter and Explore
- Filter the entire map to a single incident category with one click
- Filter updates the legend, dashboard, and AI assistant context simultaneously
- Clear filter to return to all incidents

### 10 Reference Layers (toggled via the Layers panel)

| Layer | Source |
|---|---|
| Accidental Deaths by Age & Gender (2022) | India Living Atlas |
| Violent Crimes by State (2010–2022) | India Living Atlas |
| Cyber Crimes Against Women by State (2022) | India Living Atlas |
| City-wise Suicide Incidence (2020–23) | India Living Atlas |
| District-Wise Forest Fire Detected | India Living Atlas |
| India Flood Inventory 1967–2023 | India Living Atlas |
| Road Accidents by State (Urban & Rural) | India Living Atlas |
| Airports in India | India Living Atlas |
| Indian Smart Cities | India Living Atlas |
| India State Boundary 2024 | India Living Atlas |

All reference layers start hidden. Enable them individually from the Layers panel. Each layer also exposes label controls so you can annotate any string field directly on the map.

### Live Dashboard
Real-time statistics update as you pan, zoom, and filter:
- Total incidents in the dataset
- Incidents currently visible in the map view
- Filtered incident count
- Active layer count
- Current coordinates, zoom level, and map scale

### Basemap Gallery
Switch between 10 base maps: Topographic, Streets, Navigation, Night, Light Gray, Dark Gray, Satellite, Hybrid, Terrain, Oceans.

### Measurement Tools
- **Distance** — click to draw a path and measure its length (km/miles)
- **Area** — click to draw a polygon and measure its area
- Clear measurements with one click

### Sketch and Annotations
Draw freehand shapes, lines, and polygons over the map — useful for marking regions of interest during analysis or presentation. Sketch graphics stay on screen until the page is refreshed.

### Edit Incidents
Add new incidents, edit existing ones, or delete records directly on the map. The Editor presents a structured form with a dropdown for incident categories — ensuring data consistency. Changes are saved to the hosted ArcGIS Online feature service.

### AI Assistant
Ask questions about the dataset in plain language and control the map with your words:

**Informational queries**
- "What are the deadliest incidents?"
- "How many terror attacks are in Maharashtra?"
- "List all industrial disasters with more than 100 fatalities"
- "Compare states by total incident count"

**Map control commands**
- "Zoom to the Bhopal Gas Tragedy" — the map animates to that location
- "Filter the map to show only Natural Disasters"
- "Show the Forest Fire layer"
- "Hide all reference layers"
- "Zoom back to India"

The assistant uses Google Gemini Flash (primary) or Groq Llama 4 (fallback).

### Address Search
Type any Indian city, state, or address into the search bar to instantly pan and zoom the map to that location.

---

## How to Use

1. Open the app — you will see the animated landing screen.
2. Click **Open Dashboard** to load the map.
3. The map opens centred on India, showing all incident markers.
4. Use the toolbar buttons (top-right) to open panels:
   - **Layers** — toggle reference layers on/off, filter incidents, set labels
   - **Basemap** — switch the background map
   - **Dashboard** — live statistics and map information
5. Use the left-side tool strip for navigation:
   - **Zoom in/out** — standard zoom controls
   - **Home** — zoom back to full India view
   - **Locate** — centre on your GPS location
   - **AI Assistant** — open the chat panel
   - **Legend** — see what each icon means
6. Use the circular tool buttons below the navigation strip:
   - **Measure** — distance or area measurement
   - **Sketch** — draw annotations
   - **Edit Incidents** — add or modify incident data

---

## Incident Categories

| Icon | Category |
|---|---|
| ✈ | Aviation Accident |
| 🔥 | Fire Disaster |
| 🏭 | Industrial Disaster |
| 💥 | Industrial Explosion |
| 🏗 | Infrastructure Failure |
| 🌊 | Natural Disaster |
| 👥 | Stampede |
| 💣 | Terror Attack |
| 🚌 | Transport Accident |
| 🔫 | Violence / Arson |

---

## Data

The primary dataset — **India Major Incidents** — is hosted on ArcGIS Online as an editable FeatureService. It covers incidents including aviation accidents, natural disasters (floods, earthquakes, cyclones, landslides), industrial disasters (Bhopal Gas Tragedy, factory fires), terror attacks (Mumbai 26/11, Parliament attack), train derailments, bus accidents, religious stampedes, and infrastructure failures.

Reference layers are sourced from the **India Living Atlas** (Esri India), which provides authoritative, regularly updated datasets about India's geography, crime, disasters, and infrastructure.

---

## Technology Stack

| Component | Technology |
|---|---|
| Mapping engine | ArcGIS Maps SDK for JavaScript 4.30 |
| Build tool | Vite 5 |
| Primary AI | Google Gemini Flash 2.0 |
| Fallback AI | Groq Llama 4 Scout |
| Reference data | India Living Atlas (Esri India) |
| Hosted data | ArcGIS Online FeatureService |
| Fonts | Google Fonts (Source Sans 3, Averia Serif Libre) |

---

## Privacy

This is a fully public application. No user data is collected or stored. The AI assistant sends your query text and the incidents dataset to Google or Groq's API for processing. No map coordinates or personal information are transmitted.

---

*India Incidents Explorer · ArcGIS Maps SDK for JavaScript 4.30 · v10.0 · 2026*
