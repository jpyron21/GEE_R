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
 
var preDorRed = preDorian.reduce(ee.Reducer.median());
  ```
 
 Typically more analysis would be done in GEE before R, in order to harness the power of the remote server, but for the scope of this course I will be doing the bulk of my processing in R.
 
 Now that I have pulled my desired images, I need to export them to Google Drive. This is a pretty "cookie cutter" operation and is done below.
 ```javascript
Export.image.toDrive({
  image:preDorRed.toDouble(),
  description:'preDorian',
  scale:30,
  region:geometry
});
```

### Downloading to a Local Drive
Now that we have the Google Earth Engine processing out of the way, we shoud be in familiar territory. We will pull the 3 images using Git Bash. Note that the folder in your drive has to be public to access these files.

```bash
wget --no-check-certificate 'https://drive.google.com/open?id=1lo8C48inc7SUlvyM-3Sknk_U4tKztOxW' -O Dorian.tif
wget --no-check-certificate 'https://drive.google.com/open?id=18JjjGPFXY87A1SXHPJz72T1UDS7Pu-hC' -O preDorian.tif
wget --no-check-certificate 'https://drive.google.com/open?id=1Gy4sAljc4c-6gEnZKyEbIwO3bgByhs96' -O postDorian.tif
```

### Working in R
Now we can start working in R. Note that unlike in prior mapping lessons, we will be working with raster format, specifically a .tif. Additionally, geospatial objects are often subject to rigorous reprojection and transformation during analysis. GDAL, or Geospatial Data Abstraction Library, is used on the back-end of most programs to ensure correct formatting. This can all be done with the packages 'raster' and 'rgdal'. The package 'sf' will also be needed to work with shapefiles.

```R
library(raster)
library(rgdal)
library(sf)
```

Now let's load in the rasters.

```R
pre <- raster("preDorian.tif")
during <- raster("Dorian.tif")
post <- raster("postDorian.tif")
```

We can do some basic cell statistics to familiarize ourselves with the data that we are working with.
```R
cellStats(post, min)
cellStats(post, mean)
cellStats(post, max)
cellStats(post, range)
```

Now we can plot the imagery. A title can be added as "main."
```R
plot(pre, main = "Pre Dorian SAR Imagery")
```

We have successfully mapped the image, but since this is an island there is a lot of water around the landform that we do not want to account for when considering flood inundation. Let's try loading in a shapefile of the landmass so that we can mask the raster. When using this shapefile, we want to be mindful and it has the same projection as the raster.
```R
nassau <- st_read("new_providence.shp")
crs(post)
crs(nassau)
```

Now, we want to use the "R equivalent" of the extract by mask function. We can use this with R's function, "mask." Plotting these new masked areas, you can see that our study area is much more relevant and focused.
```R
pre_masked <- mask(x = pre, mask = nassau)
during_masked <- mask(x = during, mask = nassau)
post_masked <- mask(x = during, mask = nassau)
plot(pre_masked, main = "Pre Dorian SAR Imagery")
plot(during_masked, main = "During Dorian SAR Imagery")
plot(post_masked, main = "Post Dorian SAR Imagery")
```


