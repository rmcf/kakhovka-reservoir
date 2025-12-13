## Web GIS application for monitoring Kakhovka reservoir changes based on Copernicus data

![diagram_GEE-web-GIS_architecture_en](https://github.com/user-attachments/assets/d4f14767-0c15-4de1-a931-6804ef3d5ed6)
Object of investigation and context. The Kakhovka dam in Ukraine (`lat: 46.77517, lng: 33.37254`) was destroyed in June 2023, which caused a huge volume of water to flow away and exposed the bottom of the water reservoir. Since that time, new ecosystems have started their development. That is why the territory of the former Kakhovka water reservoir is becoming increasingly interesting for scientists who study fluvial processes, vegetation cover formation, etc. Despite several existing scientific studies, none of them provides visualization of analytic layers at the highest possible scale level for Copernicus Sentinel-2 – 10 meters resolution.

The idea was to develop a web GIS application for visual and/or synchronous observation and comparison of the Kakhovka reservoir territory annually: before the dam destruction (2021) and every year after (2023, 2024, 2025) based on Sentinel-2 satellite imagery from the Copernicus program.

For integration of analytical layers as a series of geoTIFF images into a self-hosted web GIS application, this general data flow was used:
- **Google Earth Engine:** data analysis masked by area of interest (vector polygon), export layers to geoTIFF files
- **GDAL transformations:** conversion of regular large geoTIFF files into cloud-optimized geoTIFFs (COG geoTIFF)
- **Cloud object storage:** creating buckets, uploading COG geoTIFF files, configuring public access, CORS headers, etc.
- **Web GIS application:** rendering COG geoTIFF with OpenLayers.js and custom styles for each type of layer

The most challenging stages:
- **GDAL transformations** – for optimization of regular geoTIFF files for use in the web application, it required conversion, transformation, and compression: `EPSG:4326 => EPSG:3857`; `geoTIFF => COG geoTIFF`; `64-bit => 8-bit`; `tile size = 512`; `compression "deflate"`
- **Rendering COG geoTIFFs** – despite the native support of COG geoTIFF files by the OpenLayers.js library, the styling of these layers must take into account: 8-bit images (0-255 values), available bands, data min/max ranges, filtering of "no data" values or making them transparent, etc.

The main features of the web GIS app:
- **Two-panel synchronous observation mode:** for sharing the same view across two maps with different layer configurations 
- **Custom complex layer management:** ability to combine layers and visually compare different analytical layers for each of 4 years 
- **Rendering true color imagery and analytical layers** interactively at the highest available resolution for Sentinel-2 (10 meters)
