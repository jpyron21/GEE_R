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

Any code produced in Google Earth Engine is stored in a project repository. These are very limited git repositories and only allow for users to store source code. We can take a look at all available repositiories here:
https://earthengine.googlesource.com/

First I need to choose my image, selecting from the SAR image collection, indicating an appropriate date, and the desired polarization.
```javascript
var preDorian = ee.ImageCollection('COPERNICUS/S1_GRD')
  .filterBounds(geometry)
  .filterDate('2019-07-22', '2019-07-28')
  .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
  ```
 
 Typically more analysis would be done in GEE before R, in order to harness the power of the remote server, but for the scope of this course I will be doing the bulk of my processing in R.
 
 Now that I have pulled my desired images, I need to export them to Google Drive. This is a pretty "cookie cutter" operation and is done below.
 ```javascript
 Export.image.toDrive({
  image:preDorian.first().toFloat(),
  description:'preDorian',
  scale:30,
  region:geometry
});
```

### Downloading to a Local Drive
Now that we have the Google Earth Engine processing out of the way, we shoud be in familiar territory. We will pull the 3 images using Git Bash. Note that the folder in your drive has to be public to access these files.

```bash
wget --no-check-certificate 'https://drive.google.com/file/d/1sl8qWpzRLfXEw1o-oIACFIZElorzRrzd/view?usp=sharing' -O Dorian.tif
wget --no-check-certificate 'https://drive.google.com/file/d/1sl8qWpzRLfXEw1o-oIACFIZElorzRrzd/view?usp=sharing' -O preDorian.tif
wget --no-check-certificate 'https://drive.google.com/file/d/1sl8qWpzRLfXEw1o-oIACFIZElorzRrzd/view?usp=sharing' -O postDorian.tif
```
