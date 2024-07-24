# Lusatia-Sentinel-2-RGB-2023

# Lusatia Sentinel-2 Analysis

This repository contains the Google Earth Engine (GEE) scripts and related files for analyzing Sentinel-2 surface reflectance data for the Lusatia region. The project focuses on visualizing RGB composites and exporting the data for further analysis.

## Overview

The primary objectives of this project are to:
1. Analyze Sentinel-2 surface reflectance data for the Lusatia region.
2. Visualize the RGB composites.
3. Export the RGB composite data.

## Data Sources

- **Sentinel-2 Level-2A (Surface Reflectance)**: Provides pre-calibrated surface reflectance values.

## Files

- `Lusatia_Administrative_Boundary_EPSG4326_Dissolved`: Administrative boundary of the Lusatia region.

## Google Earth Engine Script

The GEE script performs the following tasks:
1. Loads the Sentinel-2 surface reflectance images.
2. Clips the images to the Lusatia region boundary.
3. Creates a median composite of the images.
4. Adds the RGB composite layer to the map.
5. Prints the acquisition dates of the selected images.
6. Exports the RGB composite image to Google Drive.

## Usage

To run the script, follow these steps:

1. **Google Earth Engine Setup**:
   - Go to the [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
   - Create a new script and paste the GEE script from this repository.
   - Modify the paths to the assets if needed.

2. **Run the Script**:
   - Click the "Run" button in the GEE Code Editor to execute the script.
   - The script will load the Sentinel-2 images, perform the analysis, and export the results.

## Exported Data

The script exports the following data to Google Drive:
- RGB composite image of the Sentinel-2 data for the specified date range and region.

## Visualization

The script also visualizes the RGB composite data on the map with appropriate legends.
![image](https://github.com/user-attachments/assets/cd0a84fc-3fa9-4ae2-91f9-92cece107ddb)

## Acknowledgements

- **Google Earth Engine**: For providing a powerful platform for geospatial analysis.
- **ESA**: For providing Sentinel-2 data.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please fork this repository and submit pull requests.

## Code
```
// Define the study area using your uploaded boundary data
var boundary = ee.FeatureCollection('projects/ee-my-konlavach/assets/Lusatia_Administrative_Boundary_EPSG4326_Dissolved');

// Check if the boundary is correctly loaded
print('Boundary:', boundary);

// Define the time range
var startDate = '2023-07-01';
var endDate = '2023-08-31';

// Load Sentinel-2 Level-2A data (Surface Reflectance)
var sentinel2 = ee.ImageCollection('COPERNICUS/S2_SR')
  .filterBounds(boundary)
  .filterDate(startDate, endDate)
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 20))
  .select(['B4', 'B3', 'B2']); // Select RGB bands

// Print the dates of the selected datasets
sentinel2.aggregate_array('system:time_start').map(function(date) {
  return ee.Date(date).format('YYYY-MM-dd').cat(' ').cat(ee.Date(date).format('HH:mm:ss'));
}).evaluate(function(dates) {
  print('Selected dates:', dates);
});

// Create a median composite
var rgbComposite = sentinel2.median().clip(boundary);

// Define visualization parameters
var visParams = {
  min: 0,
  max: 3000,
  bands: ['B4', 'B3', 'B2']
};

// Add the RGB composite to the map
Map.centerObject(boundary, 10);
Map.addLayer(rgbComposite, visParams, 'Sentinel-2 RGB Composite');

// Print the list of images used in the composite
var imageList = sentinel2.toList(sentinel2.size());
print('Sentinel-2 images used:', imageList);

// Export the RGB composite to Google Drive
Export.image.toDrive({
  image: rgbComposite,
  description: 'Sentinel2_RGB_Composite',
  folder: 'GEE_Exports',
  fileNamePrefix: 'Sentinel2_RGB_Composite',
  region: boundary.geometry(),
  scale: 10,
  maxPixels: 1e13
});
```

## Contact

For any questions or feedback, please contact [your-email@example.com].
