# Kepler.gl with OpenStreetMap in a React Application

## Introduction
This guide walks you through setting up a React application that integrates Kepler.gl for geospatial visualization using OpenStreetMap (OSM) instead of Mapbox.

## Prerequisites
Ensure you have the following installed:
- Node.js (>= 14)
- npm or yarn
- Basic knowledge of React and Redux

## Step 1: Create a React App
First, create a new React application:
```sh
npx create-react-app kepler-map
cd kepler-map
```

## Step 2: Install Dependencies
Install Kepler.gl and required packages:
```sh
npm install kepler.gl react-map-gl redux react-redux
```

## Step 3: Configure Kepler.gl to Use OpenStreetMap
Modify the `src/App.js` file to configure Kepler.gl to use OpenStreetMap tiles instead of Mapbox.

### Code Implementation
```jsx
import React from "react";
import KeplerGl from "kepler.gl";
import { createStore, combineReducers } from "redux";
import { Provider } from "react-redux";
import keplerGlReducer from "kepler.gl/reducers";

// Create Redux store
const reducers = combineReducers({
  keplerGl: keplerGlReducer
});

const store = createStore(reducers);

function App() {
  return (
    <Provider store={store}>
      <div style={{ height: "100vh" }}>
        <KeplerGl
          id="map"
          width={window.innerWidth}
          height={window.innerHeight}
          mapboxApiAccessToken={null} // No Mapbox token needed
          mapStyles={{
            default: {
              id: "osm",
              label: "OpenStreetMap",
              url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
            }
          }}
        />
      </div>
    </Provider>
  );
}

export default App;
```

## Step 4: Run the Application
Start the React app with:
```sh
npm start
```
This will open the application in your default browser.

## Notes
- This configuration removes the dependency on Mapbox.
- You can enhance OpenStreetMap support by integrating MapLibre GL.
- Ensure that the OSM tile source URL is properly configured in your network settings.

## Conclusion
By following this guide, you have successfully set up a Kepler.gl-based geospatial visualization tool using OpenStreetMap in a React application. 🚀

