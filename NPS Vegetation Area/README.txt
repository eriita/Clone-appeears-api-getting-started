TABLE OF CONTENTS
-----------------
                                               Section
Request Parameters ................................. 1
Request File Information ........................... 2
AppEEARS Area Sample Extraction Process ............ 3
AppEEARS Area Naming Convention .................... 4
Data Quality  ...................................... 5
  Moderate Resolution Imaging
    Spectroradiometer (MODIS) .................... 5.1
  NASA MEaSUREs Web Enabled
    Landsat Data (WELD) .......................... 5.2
  NASA MEaSUREs Shuttle Radar Topography
    Mission (SRTM) Version 3 (v3) ................ 5.3
  Gridded Population of the World (GPW)
    Version 4 (v4) ............................... 5.4
  Suomi National Polar-orbiting Partnership (S-NPP)
	NASA Visible Infrared Imaging Radiometer Suite
	(VIIRS)....................................... 5.5
  Soil Moisture Active Passive (SMAP)............. 5.6
Documentation ...................................... 6
Sample Request Retention ........................... 7
Citation ........................................... 8
Feedback ........................................... 9


1. Request Parameters
   ------------------
Request Name:   NPS Vegetation Area
Date Completed: 2018-04-09T15:48:53.213000
Id:             7dffacbc-0ac3-41d9-a453-fa62918b00a2

Details:
    Number of Vector Features: 1
    Start Date:                07-01-2017
    End Date:                  07-31-2017
    Layers:
                               LST_Day_1km (MOD11A2.006)
                               LST_Night_1km (MOD11A2.006)
                               Lai_500m (MCD15A3H.006)
                               Band1 (SRTMGL1.003)
    Output Projection:         Geographic
                               Datum:  WGS84
                               EPSG:   4326
                               PROJ.4: "+proj=longlat +datum=WGS84 +no_defs"
    Output Format:             geotiff

Version:
    This request was processed by AppEEARS version 2.6


2. Request File Information
   ------------------------
Supporting Files:
    NPS-Vegetation-Area-MCD15A3H-006-metadata.xml
    NPS-Vegetation-Area-MOD11A2-006-metadata.xml
    NPS-Vegetation-Area-SRTMGL1-003-metadata.xml
    NPS-Vegetation-Area-granule-list.txt
    NPS-Vegetation-Area-request.json
    MCD15A3H-006-FparLai-QC-lookup.csv
    MOD11A2-006-QC-Day-lookup.csv
    MOD11A2-006-QC-Night-lookup.csv
    SRTMGL1N-003-Band1-lookup.csv
    MCD15A3H-006-FparLai-QC-Statistics-QA.csv
    MOD11A2-006-QC-Day-Statistics-QA.csv
    MOD11A2-006-QC-Night-Statistics-QA.csv
    SRTMGL1N-003-Band1-Statistics-QA.csv
    MCD15A3H-006-Statistics.csv
    MOD11A2-006-Statistics.csv
    SRTMGL1-003-Statistics.csv

Data Files:
    Number of Extracted Data Files:     32
    Total Size of Extracted Data Files: 15.97 MB


3. AppEEARS Area Sample Extraction Process
   ---------------------------------------
When an area sample extraction request is successfully submitted, AppEEARS
implements a series of tools and services to extract the exact data the user is
interested in; or rather, extracts data from specific data layers that
intersect with the input vector file's features. AppEEARS first uploads the
input vector file and reprojects it to the source projection of the data layer
of interest. The PROJ.4 definitions for each data collection available through
AppEEARS are listed below.

MODIS (TERRA, AQUA, & Combined):
"+proj=sinu +lon_0=0 +x_0=0 +y_0=0 +a=6371007.181 +b=6371007.181 +units=m +no_defs"

WELD (CONUS):
"+proj=aea +lat_1=29.5 +lat_2=45.5 +lat_0=23 +lon_0=-96 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"

WELD (Alaska):
"+proj=aea +lat_1=55 +lat_2=65 +lat_0=50 +lon_0=-154 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"

SRTM v3 (30m & 90m):
"+proj=longlat +datum=WGS84 +no_defs"

MODIS Snow Products (TERRA & AQUA):
"+proj=sinu +lon_0=0 +x_0=0 +y_0=0 +a=6371007.181 +b=6371007.181 +units=m +no_defs"

GPW v4:
"+proj=longlat +datum=WGS84 +no_defs"

S-NPP NASA VIIRS:
"+proj=sinu +lon_0=0 +x_0=0 +y_0=0 +a=6371007.181 +b=6371007.181 +units=m +no_defs"

SMAP - Global
"+proj=cea +lon_0=0 +lat_ts=30 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"

SMAP - Northern Hemisphere
"+proj=laea +lat_0=90 +lon_0=0 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"

Following the reprojection of the vector file, a bounding box for each feature
in the vector file is determined. Each corner point is first determined using
the minimum and maximum latitude and longitude values. An additional 1 pixel
buffer is applied to each corner point to create the feature bounding box. See
below for details on how the bounding box is determined.

Feature Bounding Box Calculation
    UpperLeft   =   (maxLat + cellSize), (minLon - cellSize)
    LowerLeft   =   (minLat - cellSize), (minLon - cellSize)
    UpperRight  =   (maxLat + cellSize), (maxLon + cellSize)
    LowerRight  =   (minLat - cellSize), (maxLon + cellSize)

For each feature, a series of tools and services are used to determine which
spatial tiles intersect with the coordinates of the feature bounding box for
the data layer of interest. These tiles are extracted (from OPeNDAP) to a
server-side work environment where they are mosaicked into an image. Tile
extraction and processing is implemented for each requested composite period
(e.g., daily, weekly, 8-day, 16-day, monthly, seasonal, or annual) to create a
time series image stack. If the user chooses to have the output projection for
each extracted data file match, then the image stack is reprojected into the
user-requested projection using the PROJ.4 definition described above. The
image stacks are finally clipped to the input feature shape to only maintain
the data intersecting the feature shape. Data outside of the feature shape are
converted to a product-specific NODATA value. Each clipped image in the time
series image stack is saved as a CF-compliant NetCDF file or in a series of
Geospatial Tagged Image File Format (GeoTIFF) files with a unique name
following the naming conventions described in Section 4 of this README.

AppEEARS implements a strict procedure for reprojecting data outputs. Pixel size
and resampling methods are non-customizable in AppEEARS. Reprojected data are
produced using the Geospatial Data Abstraction Library (GDAL) gdalwarp function
in combination with the PROJ.4 string definition for the user-defined output
projection type. Reprojection is performed using nearest neighbor resampling.
If the projection units are the same between the native and output projections,
the native pixel size is used to reproject the image. If the projection units
between the native and output projections are different (e.g. sinusoidal (m) to
geographic (degrees), pixel size is calculated by reprojecting the center pixel
of the original image, calculating its width and height, and then squaring all
pixels. The latitude and longitude of the region of interest are maintained in
the conversion.

NOTE:
  - Requested date ranges may not match the reference date for multi-day
  products. AppEEARS takes an inclusive approach when extracting data for
  sample requests, often returning data that extends beyond the requested
  date range. This approach ensures that the returned data includes records for
  the entire requested date range.
  - If selected, the SRTM v3 product will be extracted regardless of the time
  period specified in AppEEARS because it is a static dataset. The date field
  in the data tables reflect the nominal SRTM date of February 11, 2000.
  - If the visualizations indicate that there are no data to display, proceed
  to checking the .csv output file. Data products that have both categorical
  and continuous data values (e.g. MOD15A2H) are not able to be displayed in
  the visualizations within AppEEARS.


4. AppEEARS Area Sample Naming Convention
   --------------------------------------
Output data files returned by AppEEARS have the following naming convention:

<ProductShortName>.<Version>_<LayerName>_doy<Year><JulianDate>_<AppEEARSFeatureID>.<FileFormat>

Example:
    MOD13Q1.006__250m_16_days_NDVI_doy2005193.aid0002.tif

    <ProductShortName> .......... MOD13Q1
    <Version> ................... 006
    <LayerName> ................. _250m_16_days_NDVI
    <Year> ...................... 2005
    <JulianDate> ................ 193
    <AppEEARSFeatureID> ......... aid0002
    <FileFormat> ................ tif

The AppEEARS Feature ID is assigned automatically by the system.


5. Data Quality
   ------------
When available, AppEEARS extracts and returns quality assurance (QA) data for
each data file returned regardless of whether the user requests it. This is
done to ensure that the user possesses the information needed to determine the
usability and usefulness of the data they get from AppEEARS. Most data products
available through AppEEARS have an associated QA data layer. Some products have
more than one QA data layer to consult. See below for more information
regarding data collections/products and their associated QA data layers.

 5.1 MODIS (Terra, Aqua, & Combined)
     -------------------------------
  All MODIS land products, as well as the MODIS Snow Cover Daily product,
  include quality assurance (QA) information designed to help users understand
  and make best use of the data that comprise each product.
  - See the MODIS Land
  Products QA Tutorials (https://lpdaac.usgs.gov/user_resources/
  e_learning) for more QA information regarding each MODIS land product
  suite.
  - See the MODIS Snow Cover Daily product user guide for information
  regarding QA utilization and interpretation.

 5.2 NASA MEaSUREs WELD (CONUS & Alaska)
     -----------------------------------
  Each WELD product contains a pixel saturation band and two cloud
  detection bands generated using the Landsat automatic cloud cover
  assessment (ACCA) algorithm and a decision tree based cloud detection
  approach.
  - See the WELD product documentation
  (https://lpdaac.usgs.gov/dataset_discovery/measures/
  measures_products_table) or Roy et al. 2010 (http://dx.doi.org/10.1016/
  j.rse.2009.08.011) for details regarding these bands.

 5.3 NASA MEaSUREs SRTM v3 (30m & 90m)
     ---------------------------------
  SRTM v3 products are accompanied by an ancillary "NUM" file in place of the
  QA/QC files. The "NUM" files indicate the source of each SRTM pixel, as well
  as the number of input data scenes used to generate the SRTM v3 data for that
  pixel.
  - See the user guide (https://lpdaac.usgs.gov/sites/default/files/
  public/measures/docs/NASA_SRTM_V3.pdf) for additional information regarding
  the SRTM "NUM" file.

 5.4 GPW v4
     ------
  The GPW Population Count and Population Density data layers are accompanied
  by two Data Quality Indicators datasets. The Data Quality Indicators were
  created to provide context for the population count and density grids, and to
  provide explicit information on the spatial precision of the input boundary
  data. The data context grid (data-context1) explains pixels with "0"
  population estimate in the population count and density grids, based on
  information included in the census documents. The mean administrative unit
  area grid (mean-admin-area2) measures the mean input unit size in square
  kilometers. It provides a quantitative surface that indicates the size of the
  input unit(s) from which the population count and density grids were created.

 5.5 S-NPP NASA VIIRS
     ----------------
  All S-NPP NASA VIIRS land products include quality information
  designed to help users understand and make best use of the data that comprise
  each product. For product-specific information, see the link to the S-NPP
  VIIRS products table provided in section 6.

  NOTE:
    - The S-NPP NASA VIIRS Surface Reflectance data products VNP09A1 and VNP09H1
    contain two quality layers: 'SurfReflect_State' and 'SurfReflect_QC'. Both
    quality layers are provided to the user with the request results. Due to changes
    implemented on August 21, 2017 for forward processed data, there are differences
    in values for the 'SurfReflect_QC' layer in VNP09A1 and 'SurfReflect_QC_500m'
    in VNP09H1.
    - Refer to the S-NPP NASA VIIRS Surface Reflectance User's Guide Version 1.1
    (https://lpdaac.usgs.gov/sites/default/files/public/product_documentation/vnp09_user_guide.pdf)
    for information on how to decode the 'SurfReflect_QC' quality layer for data
    processed before August 21, 2017. For data processed on or after August 21, 2017,
    refer to the S-NPP NASA VIIRS Surface Reflectance User's guide Version 1.6
	(https://lpdaac.usgs.gov/sites/default/files/public/product_documentation/vnp09_user_guide_v1.6.pdf).

 5.6 SMAP
     ----
  SMAP products provide multiple means to assess quality. Each data product
  contains bit flags, uncertainty measures, and file-level metadata that provide
  quality information. Results downloaded from AppEEARS and/or data directly
  requested via middleware services contain not only the requested pixel/data
  values, but also the decoded bit flag information associated with each pixel/data
  value extracted. For additional information regarding the specific bit flags,
  uncertainty measures, and file-level metadata contained in this product, refer
  to the Quality Assessment section of the user guide for the specific SMAP data
  product in your request (https://nsidc.org/data/smap/smap-data.html).

6. Documentation
   -------------
The documentation for AppEEARS can be found at https://lpdaacsvc.cr.usgs.gov/appeears/help.

Documentation for data products available through AppEEARS are listed below.
 MODIS Land Products(Terra, Aqua, & Combined)
  - https://lpdaac.usgs.gov/dataset_discovery/modis/modis_products_table

 MODIS Snow Products (Terra and Aqua)
  - https://nsidc.org/data/modis/data_summaries

 NASA MEaSUREs Web Enabled Landsat Data (WELD) (CONUS & Alaska)
  - https://lpdaac.usgs.gov/dataset_discovery/measures/measures_products_table

 NASA MEaSUREs SRTM v3
  - https://lpdaac.usgs.gov/dataset_discovery/measures/measures_products_table

 GPW v4
  - http://sedac.ciesin.columbia.edu/binaries/web/sedac/collections/gpw-v4/gpw-v4-documentation.pdf

 S-NPP NASA VIIRS Land Products
  - https://lpdaac.usgs.gov/dataset_discovery/viirs/viirs_products_table

 SMAP Products
  - http://nsidc.org/data/smap/smap-data.html


7. Sample Request Retention
   -----------------
AppEEARS sample request outputs are available to download for a limited amount of time after completion.
Please visit https://lpdaacsvc.cr.usgs.gov/appeears/help?section=sample-retention for details.


8. Citation
   --------
To cite data output from AppEEARS, please follow the example below.
The [PRODUCT] was (were) retrieved from the online Application for Extracting
and Exploring Analysis Ready Samples (AppEEARS), courtesy of the NASA EOSDIS
Land Processes Distributed Active Archive Center (LP DAAC), USGS/Earth
Resources Observation and Science (EROS) Center, Sioux Falls, South Dakota,
https://lpdaacsvc.cr.usgs.gov/appeears/.

Please also cite the data product(s) that were part of the request, using the
Digital Object Identifier (DOI) for the dataset, if applicable.
Producer], [Release Year], [Dataset]. [Version]. NASA EOSDIS [DAAC Name], [DAAC Location], [City, State] (DAAC website), accessed [MM DD, YYYY], at http:// dx.doi.org/[DOI].

9. Feedback
   --------
We value your opinion. Please help us identify what works, what doesn't, and
anything we can do to make AppEEARS better by submitting your feedback at
https://lpdaacsvc.cr.usgs.gov/appeears/feedback or to LP DAAC User Services at
https://lpdaac.usgs.gov/user_services.
