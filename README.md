Automation of the Analysis of Differences between ALKIS and OSM Data with Geopandas

This project involves comparing geospatial data from OpenStreetMap (OSM) and the Real Estate Cadastre System (ALKIS) for buildings (Gebäude) in the city of Bielefeld. The structure of the analysis was inspired by Florian Lohoff.

Key Steps:

    Data Acquisition:
        Data was obtained from OSM and ALKIS through GeoFabrik and BBBike.
        Filtering and adjustment of data were performed using QGIS.

    Data Processing and Analysis:
        Map Algebra was used to adjust attributes according to the analysis criteria.
        Visual analysis and comparison of attributes between ALKIS and OSM were conducted.

    Filtering:
        Coordinates were adjusted using "Re-project Layer" in QGIS.
        Data was filtered to determine the percentage of overlap between ALKIS and OSM.

    Comparison:
        Visual comparison of building geometries between ALKIS and OSM.
        Assignment of colors indicating the level of match.

    Script for OSM to ALKIS Automation:
        Development of a Python script using GeoPandas to automate the analysis.
        This script needs further testing and adaptation as necessary.

Code Usage:

    The ALKIS_OSM.py script performs the comparison and generates visual results.
    The monitor_geojson.py script monitors the creation of new .geojson files and automatically executes the main script.

Usage Instructions:

    Install dependencies using pip install -r requirements.txt.
    Configure environment variables as needed.
    Run python monitor_geojson.py to start monitoring.

Acknowledgments:

    The project structure was inspired by Florian Lohoff.

Important Note:
This project contains sensitive ALKIS data from the Bielefeld city government. Ensure to remove or replace sensitive data before publicly sharing.

[Include additional information such as the project license, specific requirements, and any other relevant details.]

Source Code:

```python

# Importing necessary libraries
import geopandas as gpd

# Definition of a function for calculating, coloring, and saving the data
def calculate_color_and_save():
    """
    This function calculates the difference between ALKIS and OSM data, assigns colors to the data, and saves them in separate GeoJSON files.
    """

    # Absolute paths to the input GeoJSON files
    alkis_file_path = "C:/yourpath"
    osm_file_path = "C:/yourpath"

    # Loading GeoJSON data from ALKIS and OSM
    alkis_gdf = gpd.read_file(alkis_file_path)
    osm_gdf = gpd.read_file(osm_file_path)

    # Calculating the difference between ALKIS and OSM data
    difference_gdf = gpd.overlay(alkis_gdf, osm_gdf, how='difference')

    # Calculating the overlap percentage
    difference_gdf['Overlap Percentage'] = (1 - (difference_gdf['geometry'].area / alkis_gdf['geometry'].area)) * 100

    # Assigning colors based on the overlap percentage
    def assign_color(row):
        if row['Overlap Percentage'] < 10:
            return 'green'  # Green for low overlap
        elif 10 <= row['Overlap Percentage'] < 90:
            return 'yellow'  # Yellow for moderate overlap
        else:
            return 'red'   # Red for high overlap

    difference_gdf['Color'] = difference_gdf.apply(assign_color, axis=1)

    # Saving the layers in separate GeoJSON files
    green_gdf = difference_gdf[difference_gdf['Color'] == 'green']
    yellow_gdf = difference_gdf[difference_gdf['Color'] == 'yellow']
    red_gdf = difference_gdf[difference_gdf['Color'] == 'red']

    green_gdf.to_file("green.geojson", driver="GeoJSON")
    yellow_gdf.to_file("yellow.geojson", driver="GeoJSON")
    red_gdf.to_file("red.geojson", driver="GeoJSON")

    # Saving the resulting GeoJSON with colors
    difference_gdf.to_file("difference_.geojson", driver="GeoJSON")

    # Displaying completion messages
    print("The difference between the geometries of ALKIS and OSM has been calculated and saved as 'difference_.geojson' with colors based on the overlap percentage.")
    print("The geometries have been divided into three layers based on colors and saved as 'green.geojson', 'yellow.geojson', and 'red.geojson'.")

# Calling the function to calculate, color, and save the data
calculate_color_and_save()
```



Author:
Andersson Barbosa 
