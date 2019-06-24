
# Ibis 🌀

According to legend, the ibis is the last sign of wildlife to take shelter before a hurricane hits and the first to reappear once the storm has passed.

This one gets hurricane GIS data from the [National Hurricane Center](1) in geoJSON format.

## Why? 

The NHC provides an easy-to-access RSS feed of its GIS products that is updated regularly during an active storm. This tool allows for automating the fetching process in addiition to converting the `.shp` ZIP files into array of geoJSON FeatureCollections.

Ibis attempts to be as unopinionated as possible. It doesn't change any properties or names of the GIS data. It only assumes you want a spefific shapefile.

It uses asynchronous functions to limit how often it has to download new files. You can frequently check shapefiles for updated data without downloading the file every time.

## Install
__npm__
```
npm install node-ibis
```
__Yarn__
```
yarn add node-ibis
```

## Usage

__Active Storm(s)__

```js
import Ibis from 'node-ibis'

async function getForecast() {
  const ibis = new Ibis();
 
  const forecast = await ibis.get.forecast(); 

  const data = await forecast.fetchGIS();
}
```

## API

### Ibis

#### Parameters

- `options <Object>`
  - `options.name <String>`: Optionally get data for specific storm by name if it exists
  - `options.basin <String>`: Specify basin. `'at'` for Atlantic, `'ep'` for Eastern Pacific or `'cp'` for Centeral Pacific. (Optional, default `'at'`)
  - `options.exampleData <Boolean>`: Fetch data from the example RSS feed for testing. (Optional, default `false`)

If no options are passed, it will fetch all active storms in the Atlantic basin.

#### Example

```js
import Ibis from 'node-ibis'

async function getBestTrack() {
  // Example storm, known name
  const ibis = new Ibis({
    name: 'Andrea',
    exampleData: true
  });

  const bestTrack = await ibis.get.bestTrack();
  /** returns 
   * {
      name: [String],
      date: [pubDate],
      fetchGIS: [Function]
    }
  */

  const data = await bestTrack.fetchGIS();
  /** returns geoJSON array of FeatureCollections:
   * [ 
      { type: 'FeatureCollection',
    features: [ [Object], [Object] ],
    fileName: 'al012013.002_5day_lin',
    pubDate: 'Thu, 06 Jun 2013 02:32:31 GMT' },
    { type: 'FeatureCollection',
    features: [ [Object], [Object] ],
    fileName: 'al012013.002_5day_pgn',
    pubDate: 'Thu, 06 Jun 2013 02:32:31 GMT' },
    ...
    ]
  */
}
```

#### get

All methods are asynchronous, returning a `<Promise>`.

**Methods**
- `forecast()`: Forecast Track, Cone of Uncertainty, Watches/Warnings.
- `bestTrack()`: Track, Points, and Wind Swath.
- `windField()`: Initial and Forecast Surface Winds.
- `stormSurge()`: Probabilistic Storm Surge 5ft

Returns `<Object>`:
```
{
  name: <String> - Storm name for the shapefile,
  date: <String> - Last published date for the shapefile
  fetchGIS: <Function> @returns geoJSON array of FeatureCollections
}
```
#### fetchGIS
Asynchronously fetches ZIP file from RSS feed and converts geoJSON.

Returns `Promise<Object>`: Array of FeatureCollections with `fileName` and `pubData`.

#### Example
```js
async function getStormSurge() {
  const bestTrack = await ibis.get.stormSurge();

  const jsonData = await bestTrack.stormSurge();
}
```




[1]: https://www.nhc.noaa.gov/