## Using Google Earth Engine and R

### Setup

Google earth engine is a collection of remotely hosted satellite imagery and geospatial datasets that can be accessed and manipulated using
JavaScript. All new users will have to sign up to make their own algorithms, but this is not necessary to participate in this exercise.
https://earthengine.google.com/

### The Basics of Working with GEE
I am currently working with SAR, or synthetic aperture radar, data in hopes of identifying flood inundation
intervals in Nassau, Bahamas in the time before, during, and after Hurricane Dorian.

Before diving into R, let's take a look at the basics of Earth Engine. My Code Editor is public and can be accessed here:
https://code.earthengine.google.com/?accept_repo=users/jp0160/SARpractice.
First I need to choose my image, selecting from the SAR image collection, indicating an appropriate date, and the desired polarization.
```javascript
var preDorian = ee.ImageCollection('COPERNICUS/S1_GRD')
  .filterBounds(geometry)
  .filterDate('2019-07-22', '2019-07-28')
  .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
  ```
  
  
  
