# SRTM to Mapbox Terrain-RGB
'Documenting' converting SRTM .dt2 files to Mapbox's Terrain RGB Raster DEM format for 3D views in Mapbox JS v2 / Mapbox Android v10 without using Mapbox's servers

To make it extra painful this was done on Windows 10.

# Need
- Python
- GDAL
- Rasterio
- Rio-RBGify (its broken, use the fork or fix it https://github.com/tmnnrs/terrain-rgb/issues/3)

# Process

C:\Program Files\GDAL has the utilities needed cmd needs to be run from here or this added to path.

Get list of dt2 files

`dir /b /s *.dt2 >dt2_list.txt`

Build virtual set

`gdalbuildvrt -input_file_list "C:\Users\user\Desktop\Raster DEM\dt2_list.txt" "C:\Users\user\Desktop\Raster DEM\uk.vrt"`

Translate virtual set to geotiff

`gdal_translate -of GTiff "C:\Users\user\Desktop\Raster DEM\uk.vrt" "C:\Users\user\Desktop\Raster DEM\translated.tif"`

Warp WGS 84 EPSG 4326 to web mercator

`gdalwarp -s_srs EPSG:4326 -t_srs EPSG:3857 -srcnodata -32767 -dstnodata 0 -r lanczos -wo SOURCE_EXTRA=1000 -co COMPRESS=LZW "C:\Users\user\Desktop\Raster DEM\translated.tif" "C:\Users\user\Desktop\Raster DEM\translated_mercator.tif"`

Create mbtiles with RGB

`rio rgbify -b -10000 -i 0.1 --max-z 12 --min-z 0 --format png "C:\Users\user\Desktop\Raster DEM\translated_mercator.tif" "C:\Users\user\Desktop\Raster DEM\uk-rgb-dem.mbtiles"`

# Helpful
- Installing Python and GDAL https://github.com/PratyushTripathy/python_gdal_automated_windows
- Installing Rasterio and GDAL https://github.com/mapbox/rasterio/issues/1963
- Most of the end script https://github.com/tmnnrs/terrain-rgb
- The first person to get it working https://github.com/mapbox/rio-rgbify/issues/19
