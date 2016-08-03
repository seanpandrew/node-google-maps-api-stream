# google-maps-api-stream

A streaming, rate-limited, and caching interface to Google Maps APIs

```sh
npm install google-maps-api-stream --save
```

## Usage

### ES6 Syntax

```js
import {Geocoding} from 'google-maps-api-stream';

const geocoder = new Geocoding({
  googleMaps: {
    key: 'your-api-key'
  },
  cacheFile: 'geocache.db'
});

geocoder.on('data', data => console.log(data));
geocoder.on('end', () => console.log('Done.'));

geocoder.write('Hamburg, Germany');
geocoder.write('Las Vegas');
geocoder.end();
```

### ES5 Syntax

```js
var mapsApi = require('google-maps-api-stream');
var Geocoding = mapsApi.Geocoding;

var geocoder = new Geocoding({
  googleMaps: {
    key: 'your-api-key'
  },
  cacheFile: 'geocache.db'
});

geocoder.on('data', data => console.log(data));
geocoder.on('end', () => console.log('Done.'));

geocoder.write('Hamburg, Germany');
geocoder.write('Las Vegas');
geocoder.end();
```

### Response Format

All interfaces share the following response format:

```js
{
  input: '', // input data, before the accessor was applied
  query: '', // input data, after the accessor was applied
  stats: {current: 1},
  response: ... // response format depends on the interface
  error: false,
  cached: false
}
```

## API Documentation

All interfaces are NodeJS transform streams. Input data can be passed in by writing or piping to them. They emit the `data`, and `end` events. All interfaces accept an options object upon initialisation. The following options may be set:

**options.googleMaps:** Type: `<Object>`, default: `{}`. Options passed to the `googlemaps` module. You *must* set either a `key` property, containing a Google Maps API key, or a `clientId` and `privateKey`. Consult the [googlemaps](https://www.npmjs.com/package/googlemaps) package for other options.

**options.queriesPerSecond:** Type: `<Number>`, default: `35`. Maximum number of queries per second.

**options.cacheFile:** Type: `<String>`, default: `null`. Path to a file for caching queries. A cache will not be used if this path is set to `null`.

**options.accessor:** Type: `<Function>`, default: `function(data) { return data; }`. This function can be used to transform data written to the stream before it is passed to the API.

**options.stats:** Type: `<Object>`, default: `{current: 0}`. A stats object which will be attached to every result. The value of `stats.current` is incremented with every query.

The Google Maps API stream module currently provides streaming interfaces to the following Google Maps APIs:

### Directions

```js
var directionsInterface = new mapsApi.Directions(options);
```

Required query parameters:

```js
const query = {
  origin: 'address || lat,lng',
  destination: 'address || lat,lng'
};
```

Refer to [Request Parameters](https://developers.google.com/maps/documentation/directions/intro#RequestParameters) for optional parameters.

Response format:  
[Google Directions Response](https://developers.google.com/maps/documentation/directions/intro#DirectionsResponses)

### Geocoding

```js
var geocoder = new mapsApi.Geocoding(options);
```

Required query parameters:

```js
const query = {
  address: 'Some address, Sometown, Earth'
};
```

or

```js
const query = 'Some address, Sometown, Earth';
```

Refer to [Request Parameters](https://developers.google.com/maps/documentation/geocoding/intro#geocoding) for optional parameters.

Response format:  
[Google Geocoding Response](https://developers.google.com/maps/documentation/geocoding/start#geocoding-request-and-response-latitudelongitude-lookup)

### Static Maps

```js
var staticMaps = mapsApi.staticMaps;
var staticMapsImageURLs = new staticMaps.URL(options);
var staticMapsImages = new staticMaps.Image(options);
```

The static maps interface offers two response formats. The `URL` interface simply sets the response field to a URL to a static maps image. The `Image` interface sets the response field to a binary blob containing the image in PNG format.

Required query parameters:

```js
const query = {
  center: 'lat,lng',
  zoom: number
  size: '{width}x{height}'
};
```

Refer to [URL Parameters](https://developers.google.com/maps/documentation/static-maps/intro#URL_Parameters) for optional parameters.

Response format:  
URL string or binary blob
