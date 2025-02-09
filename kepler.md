3️⃣ Add Kepler.gl to your React app

Modify src/App.js:

import React from "react";
import KeplerGl from "@kepler.gl/components";
import { createStore, combineReducers } from "redux";
import { Provider } from "react-redux";
import keplerGlReducer from "@kepler.gl/reducers";
import { applyMiddleware, compose } from "redux";
import thunk from "redux-thunk";

// Create Redux store
const reducers = combineReducers({
  keplerGl: keplerGlReducer
});
const store = createStore(reducers, {}, compose(applyMiddleware(thunk)));

function App() {
  return (
    <Provider store={store}>
      <KeplerGl
        id="map"
        mapboxApiAccessToken={null} // Disable Mapbox
        width={window.innerWidth}
        height={window.innerHeight}
      />
    </Provider>
  );
}

export default App;

4️⃣ Use OpenStreetMap Instead of Mapbox

Kepler.gl requires a **custom map provider** when not using Mapbox. Modify `src/App.js` to configure OpenStreetMap:

```js
import { setMapStyle } from "@kepler.gl/actions";
import { useEffect } from "react";
import { useDispatch } from "react-redux";

const App = () => {
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(
      setMapStyle({
        styleType: "osm",
        topLayerGroups: {},
        visibleLayerGroups: {},
        mapStyles: {
          osm: {
            id: "osm",
            label: "OpenStreetMap",
            url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
            icon: "https://upload.wikimedia.org/wikipedia/commons/b/b0/OpenStreetMap_logo.svg"
          }
        }
      })
    );
  }, [dispatch]);
};
```

Step 3: Create a Dockerfile

Now, create a Dockerfile in your project:

```Dockerfile
# Use Node.js base image
FROM node:18.18.2-alpine

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json package-lock.json ./
RUN npm install

# Copy the rest of the application
COPY . .

# Build the React app
RUN npm run build

# Install and configure a simple web server
RUN npm install -g serve

# Expose the port
EXPOSE 3000

# Start the web server to serve the built React app
CMD ["serve", "-s", "build", "-l", "3000"]
```

Step 4: Build and Run the Container

1️⃣ Build the Docker Image

```sh
docker build -t kepler-app .
```

2️⃣ Run the Container

```sh
docker run -d -p 3000:3000 --name kepler-container kepler-app
```

Now, your Kepler.gl app is running with OpenStreetMap! 🎉
Access it at `http://your-server-ip:3000` 🌍
