---
layout: post
title: Deck.gl - Data Visualization
date: '2017-03-15 18:08:58'
---

Hi everyone. Today is the last time I’ve worked at feels. I decided to spend whole weekend to do experiment which I haven’t done before.

I remember to [deck.gl](https://uber.github.io/deck.gl/#/), popular opensource published by Uber. Why don’t we get a try ? 😉

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_28.jpg)

I tried installing Deck.gl, MapBox, implement some code, take a deep understand in GLSL. Tried to visualize VisaH1B static, Flight record, Taxi route. All datasets are available on Kaggle which under CC license.

And now, it’s time to write blog for sharing my experiment.

Hope you enjoy it :]

## 1. Data Visualization

Data Visualization is quite fun. Perhaps when you see the term “Data Visualization”, you might remember to ugly Microsoft Excel’s spreadsheet chart. In real world, there are many situations we need to visualize the data.

- If you’re student, before going to big final semester presentation, you would like to be stand out. What would you do?
- If you’re founder, I believe you had one time that shows off your growth user over the world to potential investors, and you want to make impression on the first sight. What do you do?

I believe we’re using the same chart like below. It’s so common, trivial.

![](https://i0.wp.com/www.excel-easy.com/data-analysis/images/charts/column-chart.png?w=850)

There are millions of people did that. If you would like to be stand out. Please don’t do the same way with hundred people did before. 😎

#### Brave world

![](https://i2.wp.com/s-media-cache-ak0.pinimg.com/originals/22/32/22/22322282dac474a138666b7e79cd5bd1.jpg)
![](https://i0.wp.com/data-ink.com/wp-content/uploads/2016/12/Cancer-Deaths-by-County.png)

As you can see, data visualization is really catch-eye.

### Props

- It can be accessed quickly by a wider audience.
- It conveys a lot of information in a small space.
- It makes your report more visually appealing.

### Cons

- You must put more **effort**.

It’s boring if we keep doing every time. I would be better if we put myself into a dangerous zone, try somethings new, push to boundary.

## 2. Roadmap

This experiment will show you how to visualize million of records in world-map.

You will understand

- How to use vector-based Mapbox SDK, and able to custom map with own style.
- How to use Deck.gl, a WebGL-powerful framework for visual exploratory data analysis of large datasets.
- Visualize the location of 1 million trees in New York city with ScreenGridLayer
- Experiment on Heatmap
- Visualize the flight record data by using FlightLayer
- Combination: GLSL shaders and Deck.gl to achieve dynamic-map.

Here are screenshots.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_1.jpg)
**683.788** trees in NY by ScreenGridLayer, run smoothly at 60FPS

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_2.jpg)

The square, brighter green colors, means more trees round up.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_4.jpg)

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_3.jpg)

Heat-map for better look

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/Data-visualization-thumbnail.jpeg)

Flight record data with Flightlayer (Static)

<iframe width="560" height="315" src="https://www.youtube.com/watch?v=kgcJ0rftIUA" frameborder="0" allowfullscreen></iframe>

Dynamic visualization with GLSL shader

## 3. Technologies

### 3.1 React-redux

Before writing actually code. It’s better if I cover and mention all technologies we’re using.

[**IMPORTANT**] I assume you’ve already had experience on [React](https://facebook.github.io/react/) and [Redux](https://github.com/reactjs/redux). If not, don’t be shy to follow tutorials on google.

### 3.2 MapBox

[Mapbox](https://www.mapbox.com) is a large provider of custom online maps for websites such as Foursquare, Pinterest, Evernote, the Financial Times, The Weather Channel and Uber Technologies.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_5.jpg)

Mapbox offers a million way to custom your map. Fit with many contextures: Drones, Finance Government, Logistics Media Natural resources, Outdoors, Real estate, Security, Transportation, Travel, … Each kind of maps have difference look

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_6.jpg)

Mapbox also provides amazing studio, then you can create owner map as well as.

Instead of using mapbox.js officially, I intend to use [react-map-gl](https://github.com/uber/react-map-gl). It’s React friendly API wrapper around MapboxGL JS. It’s one of popular library was published by Uber recently with 2.2k ⭐️.

### 3.3 Deck.gl

Beside react-map-gl. Uber also publish [deck.gl](https://uber.github.io/deck.gl) tool.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_7.jpg)

There are 3 highlight factors you might consider.

1. Organize the complex data by following Layered Approach. Makes it easy to package and share new visualizations as reusable layers.
2. High-Precision computations in the GPU: By emulating 64-bit floating point computations in the GPU. Deck.gl renders datasets with unparalleled accuracy and performance.
3. React and Mapbox GL Integrations: Great match with React, supporting efficient WebGL rendering under the Reactive programming paradigm.

[Deck.gl](https://github.com/uber/deck.gl) deserves more than 1.8k ⭐️ on [Github](https://github.com/uber/deck.gl) 💯

### 3.4 Kaggle

It’s incompliance if we don’t have much data for visualization. [Kaggle](https://www.kaggle.com) is the best place to get huge data. They offer open datasets on everything from government, health, and science to popular games and dating trends. It’s really valuable treasure for data mining or training model for deep learning machine 🤘

As part of this blog. I used [2015 Tree Census in NewYork city](https://www.kaggle.com/nycparks/tree-census) and  [2015 Flight Delay and Cancellation](https://www.kaggle.com/usdot/flight-delays). All of them are released under [CC0: Public Domain License](https://creativecommons.org/publicdomain/zero/1.0/). So you can read/write, modify, distribute the data whatever you like. It’s cool 👍

### 3.5 GLSL Shader

I also cover a bit about GLSL Shader. If you’re learned OpenGL, you must be familiar with this term. I will keep it simple as possible because I don’t have time to explain deeper.

Basically, GLSL has a syntax similar to C, which is executed directly by the graphics pipeline. There are two types — **Vertex Shaders** transforms shape positions to real 3D drawing coordinates. And **Fragment Shaders** helps to render colors and other attributes.

By using GLSL, we can achieve high performance with millions of data on map 😎


## 4. Time for coding

### 4.1 Stater kit

For saving time, I won’t show you how to install Node, deck.gl or react-map-gl. It’s waste of time. If you’re patient, feels free to start new project by yourself. I recommend cloning [react-redux-starter-kit](https://github.com/davezuko/react-redux-starter-kit) project. It includes React, Redux, React-router too as basic starter kit.

To begin, please [download starter pack](https://github.com/NghiaTranUIT/data-visualization-deck-gl/tree/starter-pack-updated) which I prepared. It contains base React-Redux, LayerInfo, MapSelection, react-map-gl, tween, luml.gl, as well as data from Kaggle. … all you need are ready 🤘

After clone starter-pack. Please make sure you run

npm install npm start

It opens *localhost* automatically, and notice the control at left-bottom. It’s all we do now 👍

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_17.jpg)

Before going through the tutorial, we should take a look at project structure in detail.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_18.jpg)

We have project structure here.

- **./info:** LayerInfo layer
- **./modules:** Include action.js and reducer.js of redux
- **overlays:** flight_overlay.js, tree_heatmap_overlay, tree_screengrid_layer, taxi_overlay.js. All of them are layer which we implement.
- **main.js:** The logic of this example. We will fill render() func to show map and overlay layer off. 👍
- **map-selection**: MapSelection control.

#### constants.js

Define constants, such as MapBoxAccessToken, data source name.

```js
export const MapMode = {
  NONE:             'NONE',
  TREES:            'TREES',
  TREES_HEATMAP:    'TREES_HEATMAP',
  FLIGHT:           'FLIGHT',
  TAXI:             'TAXI'
}
 
export const MAPBOX_ACCESS_TOKEN = '<insert-your-mapbox-public-token>'
 
export const FLIGHT_DATA = './example/data/flight-data.csv'
 
export const AIRPORT_DATA = './example/data/airports.csv'
 
export const TREE_DATA = './example/data/tree_census.csv'
 
export const TAXI_TRIP_DATA = './example/data/taxi-trip.json'
```

#### INITIAL_STATE

```js
const INITIAL_STATE = {
  mapViewState: {
    latitude: NY_LOCATION.latitude,
    longitude: NY_LOCATION.longitude,
    zoom: 11.5,
    pitch: 0,
    bearing: 0
  },
  flightArcs: null,
  airports: null,
  trees: null,
  taxi: null,
  mapMode: MapMode.NONE,
};
```

INITIAL_STATE defines the main variables. It’s self-explanatory. When dealing with map, we usually encounter bunch of map context, such as Latitude, longitude, zoom, pitch, bearing. If you don’t know one of them. Please do short search on google. 😉

In addition, flightArcs, airports, tress, text and mapMode are main variables which we’re storing data from CSV or JSON later.

#### render()

Navigate to bottom of main.js, you will see the render function.
```js
render() {
  const { flightArcs, trees, mapMode, airports } = this.props
  const layerInfoProps = {
    numberFlights: this._getLength(flightArcs),
    numberTrees: this._getLength(trees),
    numberAirport: this._getLength(airports),
    mode: mapMode,
  }
  const mapSelectionProps = {
    mapMode: this.props.mapMode,
    selectModeFunc: this._handleSelectMode,
    stopTimerFunc: this._handleStopTimer,
  }
  return (
    <div>
      { this._renderMap() }
      <div className='overlay-contol-container'>
        <MapSelection {...mapSelectionProps}/>
        <LayerInfo {...layerInfoProps}/>
      </div>
    </div>
  )
},
```
render() is simply render the map as well as MapSelection and LayerInfo.

#### To be implemented
```js
_renderMap() {
  // Implement here
  return null
},
```
![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_19.jpg)

Here are 4 files we need to write real code. Please open each file, and look at temporary functions which I prepared for you.
```js
/* Flight layer */
export function _renderFlightOverlay(param) {
  // Implement here
  return null
}
 
function _renderFlightLayer(param) {
  return []
}
 
/* Tree layer */
export function _renderTreesHeatMapOverlay(param) {
  // Implement here
  return null
}
 
export function _renderTreesOverlay(param) {
  // Implement here
  return null
}
 
/* Tree heatmap layer */
function _renderTreeLayer(props) {
  return []
}
```

Don’t worry about this too much right now. I’ve covered all in next chapter 👍

### 4.2 Data Source’s structure

#### Trees in New York

You can download [tree_new_york.csv](https://www.kaggle.com/nycparks/tree-census)** **source from [Kaggle](https://www.kaggle.com/nycparks/tree-census).
```
tree_id,block_id,borough,latitude,longitude,latin_name,common_name,location,status,health,tree_diameter,stump_diameter,root_stone,root_grate,root_other,trunk_wire,trunk_light,trunk_other,branch_light,branch_shoe,branch_other
277269,100002,Manhattan,40.70237278,-74.01143532,Metasequoia glyptostroboides,Dawn Redwood,On Curb,Alive,Fair,1,0,No,No,No,No,No,No,No,No,No
```
The size is around 80Mb, contains ~700.000 records. The structure of **tree_new_york.csv** is really simple. But today, we should focus on **latitude** and **longitude**

#### Flight Delay and Cancellation

Download [flight_record.csv](https://www.kaggle.com/usdot/flight-delays) and [airport.csv](https://www.kaggle.com/usdot/flight-delays) from Kaggle too. It’s largest file, around 192Mb zipped, 600Mb after extraction.
```
IATA_CODE,AIRPORT,CITY,STATE,COUNTRY,LATITUDE,LONGITUDE
ABE,Lehigh Valley International Airport,Allentown,PA,USA,40.65236,-75.44040
```
```
YEAR,MONTH,DAY,DAY_OF_WEEK,AIRLINE,FLIGHT_NUMBER,TAIL_NUMBER,ORIGIN_AIRPORT,DESTINATION_AIRPORT,SCHEDULED_DEPARTURE,DEPARTURE_TIME,DEPARTURE_DELAY,TAXI_OUT,WHEELS_OFF,SCHEDULED_TIME,ELAPSED_TIME,AIR_TIME,DISTANCE,WHEELS_ON,TAXI_IN,SCHEDULED_ARRIVAL,ARRIVAL_TIME,ARRIVAL_DELAY,DIVERTED,CANCELLED,CANCELLATION_REASON,AIR_SYSTEM_DELAY,SECURITY_DELAY,AIRLINE_DELAY,LATE_AIRCRAFT_DELAY,WEATHER_DELAY
2015,1,1,4,AS,98,N407AS,ANC,SEA,0005,2354,-11,21,0015,205,194,169,1448,0404,4,0430,0408,-22,0,0,,,,,,
```
In flight_record.csv, they didn’t include lat/long. So we need to write helper code to map departure/arrival airport’s coordination.

The valuable is departure_delay, distance, air_time field. It brings to us an important point. Useful for calculating the color of flight route (depend on how long departure_delay), velocity, and airplane position in the sky real-time (we do by GLSL later).

Maybe take a second to mull that over. By understanding the data structure, we could process and manipulate the data easier.

![](https://i0.wp.com/koenig-media.raywenderlich.com/uploads/2016/03/iThinkIveGotThis.png)

### 4.3 Mapbox

The first goal we should achieve is figured out the way to present the map. According to my mention before, we use MapBox through this tutorial. Please navigate to **_renderMap()** in main.js, and implement below code
```js
_renderMap() {
  const { mapViewState, mapMode } = this.props
  const { width, height } = this.state
  const isActiveOverlay = mapMode !== MapMode.NONE
  return (
    <MapboxGLMap
      mapboxApiAccessToken={MAPBOX_ACCESS_TOKEN}
      width={width}
      height={height}
      mapStyle='mapbox://styles/mapbox/dark-v9'
      perspectiveEnabled
      { ...mapViewState }
      onChangeViewport={this._handleViewportChanged}>
      {isActiveOverlay && this._renderVisualizationOverlay()}
      <FPSStats isActive/>
    </MapboxGLMap>
  );
},
```
It’s really straight-forward, we pass width, height, mapStyle, and mapViewState into **<MapboxGLMap>**. You can get further info at [mapbox’s documentations](https://www.mapbox.com/mapbox-gl-js/api/). The important point is isActiveOverlay. It will render the VisualizationOverlay if we choice any mode from MapSelection.
```js
_renderVisualizationOverlay() {
  const { flightArcs, airports, mapMode, trees } = this.props
 
  // wait until data is ready before rendering
  if (flightArcs === null|| airports === null || trees === null) {
    return []
  }
 
  const param = {
    props: this.props,
    state: this.state,
    onWebGLInitialized: this._onWebGLInitialized,
    effects: this._effects,
  }
 
  return (
    <div>
      { mapMode === MapMode.TREES && _renderTreesOverlay(param) }
      { mapMode === MapMode.TREES_HEATMAP && _renderTreesHeatMapOverlay(param) }
      { mapMode === MapMode.FLIGHT && _renderFlightOverlay(param) }
      { mapMode === MapMode.TAXI && _renderTaxiOverlay(param) }
    </div>
  )
},
GHT && _renderFlightOverlay(param) } { mapMode === MapMode.TAXI && _renderTaxiOverlay(param) } </div> ) },
```
I wrote it for you. It automatic switches and render individually overlay depend on mapMode we’re selecting.

**_handleViewportChanged** will be triggered whenever the viewport changed, and update the viewState as well as rendering the overlay again. If we don’t re-render overlay layer with new state of map, the layers are still same old state. It’s incorrect behavior.
```js
_handleViewportChanged(mapViewState) {
  if (mapViewState.pitch > 60) {
    mapViewState.pitch = 60
  }
  this.props.dispatch(updateMap(mapViewState))
},
```
#### Mapbox Public Token

Don’t forget to get your Mapbox Public Token from [Mapbox’s Dashboard](https://www.mapbox.com/studio/)

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_11.jpg)

Feels free to spent 5 mins for experiment [Mapbox’s map editor](https://www.mapbox.com/studio/styles/). It’s amazing tool, allow you to edit/modify the layout of map depends on your purpose.

### 4.4 Visualize trees in New York

#### Parsing tree_new_york.csv

```js
_loadData() {
  
  // Load Tree
  this._loadCsvFile(TREE_DATA, (data)=>{this.props.dispatch(loadTrees(data))})
},
```

_loadCsvFile is helper func I wrote for you. It reads csv file and parsing into array. Then dispatching **loadTrees** action to ReduxStore

```js
case 'LOAD_TREES': {
    let trees = action.data.map((item)=>{
      const lat = item.latitude
      const long = item.longitude
      return {
        position: [Number(long), Number(lat)]
      }
    })
    return {...state, trees}
}
```

The purpose of our tutorial is representing the distribution of tree. So we ignore unnecessary fields, we only care long/lat. It’s why I .map() to transform items into position object. Then returning the result into mainState. It’s not hard If you’re familiar with React+Redux.

#### TreeScreenGridOverlay.js

ScreenGridLayer is built-in Deck.gl. It’s similar with Heatmap. The ScreenGridLayer takes in an array of latitude and longitude coordinate points, aggregates them into histogram bins and renders as a grid.

Please navigate to **tree_screengrid_overlay.js** in **overlay** folder.
```js
export function _renderTreesOverlay(param) {
    const { mapViewState, trees } = param.props
    const { width, height } = param.state
    return (
      <DeckGL
        id="default-deckgl-overlay"
        width={width}
        height={height}
        debug
        {...mapViewState}
        onWebGLInitialized={param.onWebGLInitialized}
        layers={_renderTreeLayer(param.props)}
        effects={param.effects}
      />
    )
}
```
It’s perfect time to implement **_renderTreesOverlay()** func. Initialize DeckGL with specific width/height. The layer’s parameter is passed from **_renderTreeLayer()**. Be care here. We return ScreenGridLayer associate with trees model.
```js
function _renderTreeLayer(props) {
  const { trees } = props
  return [
    new ScreenGridLayer({
      id: 'gird',
      data: trees,
      minColor: [0, 0, 0, 0],
      unitWidth: 10,
      unitHeight: 10,
    })
  ];
}
```
#### Results

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_12.jpg)

Run on terminal.
```bash
yarn start
```
The result works like a charm. Despite 683.788 trees, Deck.gl still handles perfectly, around 40-60 FPS.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_14.jpg)

The aggregation is done in screen space, so the data prop needs to be reaggregated by the layer whenever the map is zoomed or panned. This means that this layer is best used with small data set, however, the visuals when used with the right data set can be quite effective.

The frame will be dropped gradually when zooming in/out, but it’s acceptable result 💯

ScreenGirdLayer is perfect choose if you would like to visualize the distribution of user/object on a map. 🤘

### 4.5 Heat-map

[Heatmap](https://github.com/vicapow/react-map-gl-heatmap-overlay) is a graphical representation of data where the individual values contained in a [matrix](https://en.wikipedia.org/wiki/Matrix_(mathematics) "Matrix (mathematics)") are represented as colors.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_15.jpg)

It’s extremely useful to present the large data over the world. There are many different [color schemes](https://en.wikipedia.org/wiki/Color_scheme "Color scheme") that can be used to illustrate the heatmap, with perceptual advantages and disadvantages for each.

It’s time to bring heatmap. Come back at _renderTreesHeatMapOverlay in **tree_heatmap_overlay.js** and put it down.

```js
export function _renderTreesHeatMapOverlay(param) {
    const { mapViewState, trees } = param.props
    const { width, height } = param.state
    return (
      <HeatmapOverlay
        locations={trees}
        {...mapViewState}
        width={width}
        height={height}
        lngLatAccessor={(tree) => [tree['position'][0], tree['position'][1]]}
        />
    )
}
```

**lngLatAccessor** : Data accessors can be provided if your data doesn’t fit the expected form `{longitude, latitude}`.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_16.jpg)

I admit react-map-gl-heat overlay handled performance for 700.00 is worse.
```js
gradientColors={Immutable.List(['blue', 'red'])}
```
React-map-gl-heat also provides gradientColors, you can customize your look easier. [ColorBrewer](http://colorbrewer2.org/#type=sequential&scheme=OrRd&n=3) offer GUI to pick color-scheme.

###  4.6 Conclusion

In section 4, I mention two of layers which built-in Deck.gl. They also have Arc Layer, Choropleth Layer, Line Layer, Scatterplot Layer. Trying all of layers is better idea 👻

## 5. FlightLayer

### 5.1 Overall

It’s time for the exciting part. We’re going to extend deck.gl’s Layer, customize it for representing Flight Route. Because of deck.gl don’t offer FlightLayer, so the one way is to implement it by our hand.

We’re dealing with GLSL shader, please confirmed you’re installed **glslify** correctly. If now, please run below code.
```bash
npm install --save glslify
```
### 5.2 Extend deck.gl’s Layer.

FlightLayer is a curve from **sourcePoint** to **tartgetPoint**. As you can see the screenshot below, it’s actually [parabola](https://en.wikipedia.org/wiki/Parabola) we’ve learn in Secondary school right 😉

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_20.jpg)

Look closer. I admit it doesn’t look like flight route. Because the flight routes are extremely complexity in real-life. We assume it’s simply parabola.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_21.jpg)

So the FlightLayer takes 3 parameters: **sourcePosition**, **targetPosition** as well as a **color**.

### 5.3 Processing flight record data

Before visualizing, we need to process the flight data to fit with our requirement.

Load flight-data.csv, airport.csv
```js
// Load Flight Data
this._loadCsvFile(AIRPORT_DATA, (data)=>{
  this.props.dispatch(loadAirport(data))
  this._loadCsvFile(FLIGHT_DATA, (data)=>{this.props.dispatch(loadFlightDataPoints(data))})
});
```
in reducer
```js
case 'LOAD_FLIGHT_POINT': {
  const flightArcs = action.points.map((item)=>{
    const originalAirport = item.ORIGIN_AIRPORT
    const destinationAirport = item.DESTINATION_AIRPORT
    return {
      sourcePosition: state.airports[originalAirport],
      targetPosition: state.airports[destinationAirport],
    }
  })
  return {...state, flightArcs}
}
 
case 'LOAD_AIRPORT': {
  let airports = {}
  action.airports.forEach((item, index)=>{
    airports[item.IATA_CODE] = [Number(item.LONGITUDE), Number(item.LATITUDE)]
  })
 
  return {...state, airports}
}
```
Please take a notice at **LOAD_AIRPORT** action, I convert airport array to hash-map. Because time complexity for searching key in generally is O(1). It’s really useful for **LOAD_FLIGHT_POINT** action, when mapping coordinate for original/destination airport.

### 5.4 Creating custom layer: Flight layer

![](https://i1.wp.com/media.giphy.com/media/d3mlE7uhX8KFgEmY/source.gif)

Time for hacking your brain 😉

#### Layer life-cycle

Below is graph I summarize layer’s lifecycle.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_22.jpg)

Fully documentation [here](https://uber.github.io/deck.gl/#/documentation/creating-custom-layers/layer-lifecycle) . Because of we extend from Layer, so we only override Layer.initializeState(), Layer.draw({uniform}) and Layer.updateState() (optional).

- **Layer.initializeState()**: This method is called only once for each layer (as defined by the id property), to set up the initial state for that layer. deck.gl will already have created the state object at this time, and added the gl context and the attributeManager context.
- **Layer.draw({uniform})**: Allows a layer to “render” or generate one or more deck.gl Layers passing in its own state as props. The layers will be rendered after the rendering layer, but before the next layer in the list. renderLayers will be called on the new layers, allowing the decomposition of the drawing of a complex data set into “primitive” layers.
- **Layer.updateState()**: Called when a new layer has been matched with a layer from the previous render cycle (resulting in new props being passed to that layer), or when context has changed and layers are about to be drawn.

#### Implement FlightLayer

Please navigate to **../scr/layers/core/flight-data/flight-data.js** I prepared all for saving time 🤗

in flight-data.js
```js
updateState({props, changeFlags: {dataChanged}}) {
  // Implement here
}
 
initializeState() {
  // Implement here
}
 
draw({uniforms}) {
  // Implement here
}
 
_createModel(gl) {
  // Implement here
}
 
calculateInstanceSourcePositions(attribute) {
  // Implement here
}
 
calculateInstanceTargetPositions(attribute) {
  // Implement here
}
 
calculateInstanceSourceColors(attribute) {
  // Implement here
}
 
calculateInstanceTargetColors(attribute) {
  // Implement here
}
 
getShaders() {
  return {
    vs: readFileSync(join(__dirname, './flight-layer-vertex.glsl'), 'utf8'),
    fs: readFileSync(join(__dirname, './flight-layer-fragment.glsl'), 'utf8')
  };
}
```
All we need are here.

#### Create gl model
```js
_createModel(gl) {
  let positions = [];
  const NUM_SEGMENTS = 50;
  for (let i = 0; i < NUM_SEGMENTS; i++) {
    positions = [...positions, i, i, i];
  }
 
  return new Model({
    gl,
    ...assembleShaders(gl, this.getShaders()),
    geometry: new Geometry({
      drawMode: GL.LINE_STRIP,
      positions: new Float32Array(positions)
    }),
    isInstanced: true
  });
}
```
**NUM_SEGMENTS** is a number of small segment we draw parabola. The reason is, In OpenGL world, we can’t draw arc line, we need to draw each segment and line it together. As more as **NUM_SEGMENTS** is, the flight route is more smoothly.

Finally, we create Model and Geometry internally, **GL.LINE_STIP** and position as input agreement.
```js
positions = [...positions, i, i, i];
```
What’s it? It’s placeholder array for representing index of segment. We passed <i> 3 times because we store vec3. It’s using in **vertex.glsl** to determine which index of segments 👍

Don’t forget to take a look at **this.getShader()**. I wrote for you. It’s simply to read shader files in same directory.
```js
getShaders() {
  return {
    vs: readFileSync(join(__dirname, './flight-layer-vertex.glsl'), 'utf8'),
    fs: readFileSync(join(__dirname, './flight-layer-fragment.glsl'), 'utf8')
  };
}
```
#### initializeState()

initializeState() is one method you must implement to create OpenGL resource you need to render layer. Deck.gl looks for the variable **model** on your state, and if set expects it to be an instance of a [luma.gl] Model class.

So we passed model we created before to this.state. According to [Deck.gl’s attributeManager recommendation](https://uber.github.io/deck.gl/#/documentation/creating-custom-layers/attribute-management), we should define the attribute for vertex.glsl file.

We defined

- instanceSourcePositions, instanceSourcePositions is **[vector3]** as x,y,z.
- instanceSourceColors, instanceTargetColors is **[vector4]** as r,g,b,a and unsigned_byte.

The complete code is below.
```js
initializeState() {
  const {gl} = this.context;
  this.setState({model: this._createModel(gl)});
 
  const {attributeManager} = this.state;
  attributeManager.addInstanced({
    instanceSourcePositions: {size: 3, update: this.calculateInstanceSourcePositions},
    instanceTargetPositions: {size: 3, update: this.calculateInstanceTargetPositions},
    instanceSourceColors: {
      type: GL.UNSIGNED_BYTE,
      size: 4,
      update: this.calculateInstanceSourceColors
    },
    instanceTargetColors: {
      type: GL.UNSIGNED_BYTE,
      size: 4,
      update: this.calculateInstanceTargetColors
    }
  });
}
```
#### Calculate position vec3

It’s time for implementing helper function to calculate source/target position and color as well.
```js
calculateInstanceSourcePositions(attribute) {
  const {data, getSourcePosition, getTargetPosition} = this.props;
  const {value, size} = attribute;
  let i = 0;
  for (const object of data) {
    const sourcePosition = getSourcePosition(object);
    value[i + 0] = sourcePosition[0];
    value[i + 1] = sourcePosition[1];
    value[i + 2] = 0;
    i += size;
  }
}
 
calculateInstanceTargetPositions(attribute) {
  const {data, getSourcePosition, getTargetPosition} = this.props;
  const {value, size} = attribute;
  let i = 0;
  for (const object of data) {
    const targetPosition = getTargetPosition(object);
    value[i + 0] = targetPosition[0];
    value[i + 1] = targetPosition[1];
    value[i + 2] = 0;
    i += size;
  }
}
```
It’s not easy to understand if you’re not familiar WebGL or OpenGL in general. The main idea is to create array for each attribute (in vertex shader file)
```js
const sourcePosition = getSourcePosition(object);
value[i + 0] = sourcePosition[0];
value[i + 1] = sourcePosition[1];
value[i + 2] = 0;
```

We get source position. get longitude and latitude which stand for sourcePosition[0] and sourcePosition[1], then passing to value array. Please z = 0, because it’s on earth ground.

It’s hacky solution because we can’t pass our object from javascript directly into vertex.glsl. The common approach is using array to pass long/lat indirectly and processing those data later. Actually, we could pass struct, but at this time, I won’t mention it.

#### Calculate colors vec4
```js
calculateInstanceSourceColors(attribute) {
  const {data, getSourceColor} = this.props;
  const {value, size} = attribute;
  let i = 0;
  for (const object of data) {
    const color = getSourceColor(object) || DEFAULT_COLOR;
    value[i + 0] = color[0];
    value[i + 1] = color[1];
    value[i + 2] = color[2];
    value[i + 3] = isNaN(color[3]) ? DEFAULT_COLOR[3] : color[3];
    i += size;
  }
}
 
calculateInstanceTargetColors(attribute) {
  const {data, getTargetColor} = this.props;
  const {value, size} = attribute;
  let i = 0;
  for (const object of data) {
    const color = getTargetColor(object) || DEFAULT_COLOR;
    value[i + 0] = color[0];
    value[i + 1] = color[1];
    value[i + 2] = color[2];
    value[i + 3] = isNaN(color[3]) ? DEFAULT_COLOR[3] : color[3];
    i += size;
  }
}
```
We do same philosophy with color as well.

#### Layer.draw()
```js
draw({uniforms}) {
  const {gl} = this.context;
  const {trailLength, currentTime, timestamp} = this.props;
  console.log(currentTime);
  this.state.model.render(Object.assign({}, uniforms, {
    trailLength,
    currentTime,
    timestamp
  }));
}
```
Draw layer and passing our data uniform into shader file is self-explanatory. We also pass trailLength, currentTime, timestamp as uniform too. We will use it later, for animation 🤗

### 5.5 GLSL (OpenGL Shading Language)

If you don’t have basic knowledge about GLSL, feels free to skip this section.

GLSLis a high-level shading language with a syntax based on the C programming language. Give developers more direct control of the graphics pipeline without having to use ARB assembly language or hardware-specific languages.

#### flight-layer-vertex.glsl
```js
#define SHADER_NAME flight-layer-vertex-shader
 
const float N = 49.0;
 
attribute vec3 positions;
attribute vec4 instanceSourceColors;
attribute vec4 instanceTargetColors;
attribute vec3 instanceSourcePositions;
attribute vec3 instanceTargetPositions;
attribute vec3 instancePickingColors;
 
uniform float opacity;
uniform float renderPickingBuffer;
uniform float currentTime;
uniform float trailLength;
uniform float timestamp;
 
varying float vTime;
varying vec4 vColor;
 
 
float paraboloid(vec2 source, vec2 target, float ratio) {
 
  vec2 x = mix(source, target, ratio);
  vec2 center = mix(source, target, 0.5);
 
  float dSourceCenter = distance(source, center);
  float dXCenter = distance(x, center);
  return (dSourceCenter + dXCenter) * (dSourceCenter - dXCenter);
}
 
void main(void) {
 
  // 1
  vec2 source = preproject(instanceSourcePositions.xy);
  vec2 target = preproject(instanceTargetPositions.xy);
  
  // 2
  float segmentRatio = smoothstep(0.0, 1.0, positions.x / N);
 
  // 3
  float vertex_height = paraboloid(source, target, segmentRatio);
  if (vertex_height < 0.0) vertex_height = 0.0;
  vec3 p = vec3(
    // xy: linear interpolation of source & target
    mix(source, target, segmentRatio),
    // z: paraboloid interpolate of source & target
    sqrt(vertex_height)
  );
 
  // 4
  // the magic de-flickering factor
  float _timestamp = (positions.x * 15.0) / 2000.0;
  vec4 shift = vec4(0., 0., mod(_timestamp, trailLength) * 1e-4, 0.);
  gl_Position = project(vec4(p, 1.0)) + shift;
 
  // Color
  vColor = mix(instanceSourceColors, instanceTargetColors, segmentRatio) / 255.;
 
  // 5
  vTime = 1.0 - (currentTime - _timestamp) / trailLength;
}
```
1. [preproject](https://uber.github.io/deck.gl/#/documentation/advanced-topics/coordinate-systems)**: **All positions must be passed through the preproject function (available both in JavaScript and GLSL) to convert non-linear web-mercator coordinates to linear Mercator “world” or “pixel” coordinates, that can be passed to the projection matrix. At this time, we convert long/lat coordinate to pixel in our webview.
2. [smoothstep](https://www.khronos.org/registry/OpenGL-Refpages/gl4/html/smoothstep.xhtml) is built-in method in OpenGL. perform Hermite interpolation between two values. The segmentRatio depends on index of segment. So it’s perfect time to get value from **positions** we created before.
3. We implement paraboloid to calculate the height of parabola.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_25.jpg)

Illustration each segment in parabola curve

4. Calculate the shift depend on currentTime, then passing to gl_Position.

5. vTime is varying variable will pass to fragment file. We calculate the time has been passed.

#### glLineWidth problem

Do you notice the width of flightLayer is too slim? I tried to put **gl.widthLine(100)** in draw() func. It didn’t work unfortunately. It takes me few hours to understand why **gl.lineWidth** didn’t work. Here is [problem](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/lineWidth).

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_23.jpg)

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_24.jpg)

We have backup plan to implement the width line, by drawing a rectangle with height is width line. I won’t cover it.

#### flight-layer-fragment.glsl

After finished vertex.glsh, we should move to fragment. The fragment is essential file, the main purpose is handling all color mapping for each vertex.
```js
#define SHADER_NAME flight-layer-fragment-shader
 
#ifdef GL_ES
precision highp float;
#endif
 
varying float vTime;
varying vec4 vColor;
 
void main(void) {
  if (vTime > 1.0 || vTime < 0.0) {
    discard;
  }
  gl_FragColor = vec4(vColor.rgb, vColor.a * vTime);
}
```
Please notice, vTime is passed from vertex.glsl. At this time, we will turn it by mutipling with color’s alpha.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/deck.gl_27.jpg)

Look cool right? 😍

#### Tween timer

If you try running example at this time, you only see the fully parabola over the map. Now we’re adding timer to achieve animation. Navigate to startTweenTimer() in main.js
```js
startTweenTimer() {
  this._tween = new TWEEN.Tween({time: 0})
        .to({time: 3600}, 120000)
        .onUpdate((data)=>{
          this.setState({
            time: data
          })
        })
        .repeat(Infinity).start()
  this.animate()
},
 
animate(time) {
  window.requestAnimationFrame(this.animate)
  TWEEN.update();
},
```
then
```
componentWillReceiveProps: function(nextProps) {
  const { mapMode } = this.props
  const { isStartedTimer } = this.state
 
  if ((nextProps.mapMode === MapMode.FLIGHT || nextProps.mapMode === MapMode.TAXI ) &&
      nextProps.mapMode !== mapMode && isStartedTimer === false) {
    this.startTweenTimer()
    this.setState({
      isStartedTimer: true
    })
  }
},
```
It triggers tweenTimer automatically whenever we select new mapMode. I also have if condition to prevent duplicated fired.

#### One more thing

Notify attributeManager in flight-layer.js to invalidate all vertex, and re-render with new currentTime attribute.
```js
updateState({props, changeFlags: {dataChanged}}) {
   if (dataChanged) {
     this.state.attributeManager.invalidateAll();
   }
 }

```
### 5.6 Finally

Time for going back to terminal and type holy spell.
```bash
# To make sure you've installed packages correctly 
yarn install 
yarn start
```

<iframe width="560" height="315" src="https://www.youtube.com/watch?v=kgcJ0rftIUA" frameborder="0" allowfullscreen></iframe>

Feels free to switch between each map mode. I belive you will be impressed what we’ve done 💯

## 6. Source code

I published all source in my github account. Feels free to clone and create contribution if you have any great idea 😉

[Data Visualization – Github](https://github.com/NghiaTranUIT/data-visualization-deck-gl)

## 7. What things are we forgetting?

If you have bright eyes, you might notice that all flights were flew and land at destination airport at the same time, whatever the distance of airport is far away or near. It looks unreal, right? 🤔

Yeah, I did it on my purpose, it’s time for your experiment, your exercise 🤗
```
Given flight_data.csv with air_time field. Assumption the velecity of airplain is 999km/h.
How do we improve flight-layer-vertex.glsl to simulate it ?
 
// Hint: Take a look at
// vec4 shift = vec4(0., 0., mod(_timestamp, trailLength) * 1e-4, 0.).
// It's all you need 👍
```

## 8. Where’s go from now?

If you like and have impression how we visualize the huge data into the map. Don’t be hostile to take a look at [Deck.gl](http://deck.gl). Pick your favorite data at Kaggle.

Learning and understanding GLSL is really useful if you want to take directly to GPU.

Hope you have greate time to ignite your lazy brain  👻


