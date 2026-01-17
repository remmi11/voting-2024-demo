# 2024 Election Results Interactive Map

An interactive web map visualization of 2024 US election results using Mapbox GL JS and TopoJSON. Features zoom-based rendering for optimal performance when displaying results at state, county, and precinct levels.

![Map Preview](https://img.shields.io/badge/Mapbox-GL%20JS-blue) ![TopoJSON](https://img.shields.io/badge/TopoJSON-3.0-green)

## Features

- 🗺️ **Interactive Map**: Pan and zoom to explore election results
- 🎨 **Simple Color Coding**: Blue for Democrat wins, Red for Republican wins
- 🔍 **Zoom-Based Rendering**: Automatically displays appropriate detail level
  - Zoom 0-4: State-level results
  - Zoom 5-7: County-level results
  - Zoom 8+: Precinct-level results
- 📊 **Detailed Popups**: Click any region to see vote counts and margins
- ⚡ **Performance Optimized**: Handles large datasets without lag
- 📱 **Responsive**: Works on desktop and mobile devices

## Quick Start

1. **Clone this repository**
   ```bash
   git clone https://github.com/remmi11/voting-2024-demo.git
   cd voting-2024-demo
   ```

2. **Open the map**
   - Simply open `arizona-precinct-map.html` in your web browser
   - Or use a local server:
   ```bash
   python -m http.server 8000
   # Then visit http://localhost:8000/arizona-precinct-map.html
   ```

3. **Interact with the map**
   - Click on any precinct to see detailed results
   - Zoom in/out to see different detail levels
   - The current zoom level is displayed in the info panel

## Current Data

This demo currently includes:
- **Arizona precinct-level results** (1,694 precincts)
- Vote counts for Joseph Biden and Donald Trump
- Precinct-level margins

## File Structure

```
voting-2024-demo/
├── arizona-precinct-map.html          # Main map application
├── AZ-precincts-with-results.topojson # Arizona precinct data
└── README.md                           # This file
```

## How It Works

### Technologies Used

- **[Mapbox GL JS](https://docs.mapbox.com/mapbox-gl-js/)**: Renders interactive maps using WebGL
- **[TopoJSON](https://github.com/topojson/topojson)**: Efficiently encodes geographic data
- **Vanilla JavaScript**: No framework dependencies

### Data Format

Each feature in the TopoJSON file contains these properties:

```javascript
{
  "GEOID": "48079-0404",           // Precinct identifier
  "state": "Arizona",               // State name
  "votes_dem": 42,                  // Democratic votes
  "votes_rep": 100,                 // Republican votes
  "votes_total": 142,               // Total votes cast
  "pct_dem_lead": -40.8            // Margin (positive = Dem, negative = Rep)
}
```

### Color Scheme

- **Blue (#2E5BFF)**: Democratic win (pct_dem_lead > 0)
- **Red (#FF2E2E)**: Republican win (pct_dem_lead < 0)
- **Gray (#999)**: No data available

## Expanding to More States

The map is designed to easily scale to nationwide data. To add more states:

1. **Prepare TopoJSON files** for additional states:
   - `precincts-CA.topojson`
   - `precincts-TX.topojson`
   - etc.

2. **Update the `dataFiles` array** in the HTML file:

```javascript
const dataFiles = [
    {
        name: 'states',
        file: 'states.topojson',
        objectKey: 'states',
        minZoom: 0,
        maxZoom: 4.99,
        layerType: 'state'
    },
    {
        name: 'counties',
        file: 'counties.topojson',
        objectKey: 'counties',
        minZoom: 5,
        maxZoom: 7.99,
        layerType: 'county'
    },
    {
        name: 'AZ-precincts',
        file: 'AZ-precincts-with-results.topojson',
        objectKey: 'AZ',
        minZoom: 8,
        maxZoom: 22,
        layerType: 'precinct'
    },
    // Add more states here...
];
```

## Creating TopoJSON Files

If you have election data in CSV or GeoJSON format:

### From GeoJSON

```bash
# Install topojson-server
npm install -g topojson-server

# Convert GeoJSON to TopoJSON
geo2topo precincts=input.geojson > output.topojson

# Optional: Simplify geometry for better performance
npm install -g topojson-simplify
toposimplify -p 0.1 -f input.topojson > simplified.topojson
```

### Required Properties

Ensure your GeoJSON features have these properties before conversion:
- `GEOID` or `name`: Unique identifier
- `votes_dem`: Democratic vote count
- `votes_rep`: Republican vote count  
- `votes_total`: Total votes
- `pct_dem_lead`: Democratic margin percentage

## Mapbox Access Token

The demo uses a public Mapbox token for demonstration purposes. For production use:

1. Create a free account at [Mapbox](https://account.mapbox.com/)
2. Get your access token
3. Replace the token in `arizona-precinct-map.html`:

```javascript
mapboxgl.accessToken = 'YOUR_TOKEN_HERE';
```

## Performance Considerations

### Current Performance
- **Arizona precincts** (1,694 features, ~6MB): Loads in < 1 second
- Smooth interactions even with detailed geometry

### Scaling to 50 States
With proper data structure:
- **All states**: < 1MB, instant load
- **All counties**: 5-10MB, < 2 seconds
- **All precincts**: 20-50MB, < 5 seconds
- Zoom-based rendering ensures smooth performance

### Tips for Large Datasets
1. **Simplify geometry**: Use `toposimplify` to reduce file size
2. **Split data by state**: Load precinct data only for visible states
3. **Use zoom constraints**: Don't render precincts when zoomed out
4. **Enable gzip**: Compress files on your server

## Browser Support

- ✅ Chrome/Edge (recommended)
- ✅ Firefox
- ✅ Safari
- ✅ Mobile browsers (iOS/Android)

Requires WebGL support (available in all modern browsers).

## Customization

### Change Map Style

```javascript
const map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/dark-v11', // Try: light-v11, dark-v11, streets-v12
    center: [-98.5, 39.8],
    zoom: 4
});
```

### Adjust Zoom Thresholds

```javascript
minZoom: 8,  // Change when precincts appear
maxZoom: 22, // Change when precincts disappear
```

### Modify Colors

```javascript
function getColor(pctDemLead) {
    if (pctDemLead === null || pctDemLead === undefined) return '#999999';
    return pctDemLead > 0 ? '#YOUR_BLUE_COLOR' : '#YOUR_RED_COLOR';
}
```

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

### Ideas for Contributions
- Add more state data
- Implement county-level aggregation
- Add filtering by vote margin
- Create state-level summary statistics
- Add export functionality
- Implement time-series animation

## License

MIT License - feel free to use this for any purpose.

## Data Sources

Election data should be sourced from official state election boards. This demo uses fictional/sample data for demonstration purposes.

## Acknowledgments

- Built with [Mapbox GL JS](https://www.mapbox.com/mapbox-gljs)
- Data format powered by [TopoJSON](https://github.com/topojson/topojson)
- Inspired by election mapping projects from NYT, Washington Post, and others

## Questions or Issues?

Open an issue on GitHub or contact the maintainer.

---

**Note**: This is a demonstration project. For production use with real election data, ensure you have proper data licensing and attribution.
