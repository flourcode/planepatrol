# PlanePatrol ✈️

A real-time aircraft tracking web application with an immersive cockpit view mode. Track aircraft around your location or search for specific flights and airports worldwide. Disclaimer: This app uses free community APIs (airplanes.live, Iowa State weather radar) that aren't licensed for commercial use. It's meant for personal learning, tinkering, and plane spotting—not as a product or service. Please respect the data providers who make this possible.

![License](https://img.shields.io/badge/license-MIT-blue.svg)

## Features

- **Real-time Aircraft Tracking**: Live updates of aircraft positions using the ADS-B Exchange API
- **GPS Integration**: Automatically centers on your current location with high-accuracy positioning
- **Cockpit View Mode**: Immersive first-person view from selected aircraft with realistic engine sounds
- **Interactive Map**: Built on MapLibre GL with custom styling and smooth animations
- **Weather Integration**: Real-time weather data for your location
- **Search Functionality**: Find specific flights by callsign or search for airports
- **Customizable Range**: Track aircraft within 10-250 nautical mile radius
- **NEXRAD Radar Overlay**: Optional weather radar visualization
- **Mobile Optimized**: Full PWA support with touch gestures and offline capabilities
- **Audio Feedback**: Realistic jet, propeller, and helicopter engine sounds

## Demo

Simply open `app/index.html` in a modern web browser. For the best experience, allow location access when prompted.

## Quick Start

1. Clone this repository:
```bash
git clone https://github.com/yourusername/planepatrol.git
cd planepatrol
```

2. Open the application:
```bash
# Using Python
python -m http.server 8000

# Using Node.js
npx http-server

# Or simply open app/index.html in your browser
```

3. Visit `http://localhost:8000/app/` (or your chosen port)

4. Allow location access when prompted for the best experience

## Usage

### Main View

- **Map Navigation**: Click and drag to pan, scroll to zoom
- **Aircraft Selection**: Click any aircraft icon to view details
- **Search**: Use the search bar to find airports (e.g., "LAX", "JFK") or aircraft by callsign
- **Range Selection**: Click the range indicator to change tracking radius (10-250nm)
- **Weather Radar**: Toggle the radar button to show/hide NEXRAD overlay
- **Reset**: Click the reset button to re-center on your GPS location

### Cockpit View

Enter cockpit mode by clicking on an aircraft and then clicking "Enter Cockpit":

- **Immersive View**: First-person perspective following the selected aircraft
- **Real-time Data**: See altitude, speed, heading, and vertical speed
- **Engine Audio**: Realistic engine sounds (toggle sound on/off)
- **Automatic Following**: Map automatically centers on the aircraft
- **Exit**: Click the back button to return to main view

## Architecture

### Core Technologies

- **MapLibre GL JS 5.1.0**: Mapping library for interactive visualizations
- **ADS-B Exchange API**: Real-time aircraft position data
- **Open-Meteo API**: Weather data provider
- **Iowa State NEXRAD**: Weather radar imagery
- **Web Audio API**: Procedural engine sound generation
- **Geolocation API**: GPS positioning

### Application Structure

```
app/index.html
├── Styles (lines 45-333)
│   ├── CSS Variables & Theme
│   ├── HUD Components
│   ├── Modals & Overlays
│   └── Responsive Design
│
├── HTML Structure (lines 334-464)
│   ├── Main View (Map + HUD)
│   ├── Cockpit View
│   ├── Modals (Search, Settings, Confirm)
│   └── Overlays (Offline, Sleep)
│
└── JavaScript (lines 465-1630)
    ├── Configuration & State Management
    ├── Map Initialization
    ├── Aircraft Data Fetching & Processing
    ├── GPS Tracking
    ├── Weather Integration
    ├── Audio Engine System
    ├── Cockpit Mode Logic
    └── Event Handlers
```

## Key Components

### State Management

The application uses a centralized `state` object:

```javascript
const state = {
    map: null,              // MapLibre instance
    cockpitMap: null,       // Cockpit view map
    useGeo: true,           // GPS tracking enabled
    lat: 39.8283,          // Current latitude
    lon: -98.5795,         // Current longitude
    radius: 25,            // Search radius (nm)
    planesData: Map(),     // Aircraft data cache
    selectedHex: null,     // Selected aircraft ID
    mode: 'main',          // View mode: 'main' or 'cockpit'
    radarEnabled: false,   // Weather radar toggle
    soundEnabled: true     // Audio toggle
}
```

### Aircraft Data Flow

1. `fetchPlanes()`: Polls API based on current position and radius
2. `processPlanes(data)`: Validates and normalizes aircraft data
3. `updateMapMarkers()`: Creates/updates custom aircraft icons
4. `animatePlanes()`: Smoothly interpolates positions between updates

### Custom Aircraft Icons

Aircraft are rendered as custom SVG icons with:
- Direction-based rotation
- Type-specific styling (jet, prop, helicopter)
- Color coding for selection state
- Smooth position interpolation

### Audio System

The `engineAudio` object generates realistic engine sounds using Web Audio API:

```javascript
engineAudio = {
    ctx: null,              // Audio context
    nodes: {},              // Active audio nodes
    play(type),            // Start engine sound ('jet', 'prop', 'heli')
    stop(),                // Stop audio
    update(altitude, speed) // Adjust pitch based on flight parameters
}
```

## API Configuration

### ADS-B Exchange

```javascript
CONFIG.API_BASE = 'https://api.airplanes.live/v2'
```

Endpoints used:
- `/point/{lat}/{lon}/{radius}` - Aircraft within radius
- `/callsign/{code}` - Search by flight callsign

### Open-Meteo Weather

```javascript
CONFIG.WX_API = 'https://api.open-meteo.com/v1/forecast'
```

Parameters: `temperature_2m`, `cloud_cover`, `wind_speed_10m`

### NEXRAD Radar

```javascript
CONFIG.RADAR_URL = 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png'
```

## Customization

### Changing Map Style

Modify the `initMap()` function:

```javascript
style: 'https://tiles.basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json'
```

Alternative styles:
- Light theme: `positron-gl-style`
- OpenStreetMap: MapTiler, Maptiler, or custom styles

### Adjusting Update Intervals

```javascript
CONFIG.POLL_MS = 5000;     // Aircraft update interval (5 seconds)
CONFIG.WX_POLL_MS = 300000; // Weather update interval (5 minutes)
```

### Customizing Range Options

Edit the range selector HTML (lines 368-372):

```html
<div class="range-opt" data-range="10">10nm</div>
<div class="range-opt" data-range="25">25nm</div>
<!-- Add more options -->
```

### Theme Customization

Modify CSS variables (lines 46-65):

```css
:root {
    --bg: #0d0d0f;              /* Background color */
    --accent: #6e9fff;          /* Accent color */
    --text-primary: #f5f5f7;    /* Primary text */
    /* ... more variables */
}
```

## Browser Compatibility

- **Recommended**: Chrome 90+, Safari 15+, Firefox 88+, Edge 90+
- **Required Features**:
  - ES6+ JavaScript
  - CSS Grid & Flexbox
  - Geolocation API
  - Web Audio API
  - MapLibre GL JS support

## Performance Optimization

The application includes several performance optimizations:

- **Request Coalescing**: Prevents duplicate API calls
- **Data Caching**: Maintains aircraft state between updates
- **Smooth Interpolation**: Uses requestAnimationFrame for 60fps animations
- **Lazy Loading**: Weather and radar data fetched on-demand
- **Memory Management**: Automatic cleanup of stale aircraft data

## Privacy & Security

- GPS location data stays local in your browser
- No personal data is collected or transmitted
- Uses HTTPS for all API requests
- Google Analytics can be removed (lines 36-44)

## Known Limitations

- Requires internet connection for live data
- GPS accuracy depends on device capabilities
- Aircraft data coverage varies by region
- Some aircraft may not broadcast ADS-B data
- Audio playback requires user interaction (browser autoplay policies)

## Troubleshooting

### No aircraft showing up
- Check that location services are enabled
- Verify you're in an area with aircraft traffic
- Try increasing the search radius
- Check browser console for API errors

### GPS not working
- Ensure HTTPS connection (required for geolocation)
- Grant location permissions in browser settings
- Try the "Reset to GPS" button

### Audio not playing
- Click anywhere on the page first (browser autoplay policy)
- Check that sound is enabled in cockpit view
- Verify browser supports Web Audio API

### Poor performance
- Reduce search radius to limit aircraft count
- Disable weather radar overlay
- Close other browser tabs
- Use a modern browser with hardware acceleration

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues.

### Development Setup

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Make your changes
4. Test thoroughly in multiple browsers
5. Commit with clear messages: `git commit -m "Add feature description"`
6. Push to your fork: `git push origin feature-name`
7. Open a pull request

### Code Style

- Use meaningful variable names
- Comment complex logic
- Follow existing code structure
- Test on mobile devices
- Maintain accessibility features

## Roadmap

Potential future enhancements:

- [ ] Flight path history trails
- [ ] Multiple aircraft comparison view
- [ ] Augmented reality mode
- [ ] Offline caching with service workers
- [ ] Flight plan integration
- [ ] Aircraft photo integration
- [ ] Custom notification alerts
- [ ] Export flight data (CSV/JSON)

## Credits

- **Aircraft Data**: [ADS-B Exchange](https://www.adsbexchange.com/)
- **Weather Data**: [Open-Meteo](https://open-meteo.com/)
- **Radar Imagery**: [Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/)
- **Mapping**: [MapLibre GL JS](https://maplibre.org/)
- **Font**: [Space Mono](https://fonts.google.com/specimen/Space+Mono) by Colophon Foundry

## License

This project is licensed under the MIT License - see below for details:

```
MIT License

Copyright (c) 2024 PlanePatrol Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Support

If you encounter issues or have questions:

1. Check the [Troubleshooting](#troubleshooting) section
2. Search existing [Issues](https://github.com/yourusername/planepatrol/issues)
3. Open a new issue with detailed information

## Acknowledgments

Special thanks to the aviation community and open-source contributors who make projects like this possible.

---

**Note**: Remember to replace `yourusername` in GitHub URLs with your actual username, and remove or replace the Google Analytics tracking code (lines 36-44 in app/index.html) if you don't want analytics.
