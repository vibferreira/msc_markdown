# Exploration of one suggested indicator

## Background

- rationality

- why is this relevant for monitoring/detecting spatially-explicit evidence of humanitarian crisis?

## Study area

The study area used in this proof-of-concept implementation is located in north-western Syria, along the border to Turkey. Three adjacent Sentinel-2 granules (37SBA, 37SCA, 37SDA) cover an area of more than 30,000km2 (latitudes 36.01°-37.05°N; longitudes 35.67°-39.11°E), as depicted in Figure 1, and ultimately define the exact extent of the study area. These three granules are provided in the same projection (UTM zone 37N, EPSG: 32637). All Sentinel-2 L1C data for these three granules that are available on the Copernicus Open Access Hub are continuously included in the data cube, resulting in a dense time-series beginning June 28, 2015 until the most recent image. At the time of writing, data is included up to ##### ###, 2018, which results in ### Sentinel-2 images. These granules are captured by two Sentinel-2 relative orbits (78 and 121), resulting in temporally denser data where the orbits overlap (see Figure ##).

Data characteristics of the study area indicate suitability for optical time-series analyses. According to the Köppen-Geiger classification, the climate is mostly warm Mediterranean (Csa) in the western part of the study area transitioning into warm and semi-arid (BSh) towards the east (Peel, Finlayson, & McMahon, 2007). The annual average cloud cover percentage, extracted from ESA’s L1C metadata, also decreases from west to east. The majority of scenes acquired from May to October have a cloud cover percentage below 10%, while otherwise generally ranging between 20-40% from October to May.

![Overview of study area with Sentinel-2 relative orbits based on simplified acquisition swaths, showing an approximate orbit overlap in purple.](./figures/study_area_edit.png)

## Data

### Sentinel-2

#### Specifications

Copernicus is a European Earth observation program, previously known as Global Monitoring for Environment and Security (GMES). It owns the fleet of Sentinel satellites, currently which is connected to other sensors called “contribution missions” and operates downstream services. The Sentinel-2 satellites are equipped with a multi-spectral imager (MSI) observing 13 spectral bands (443 nm-2190 nm). Data is captured with a swath width (i.e. field of view) of approximately 290km and spatial resolution ranging from 10-60m: three visible bands and one near-infrared band at 10m; six red-edge/shortwave infrared bands at 20m; and three atmospheric correction bands at 60m (see Figure ##).

![Spectral comparison of Landsat 7 and 8 bands with Sentinel-2 (retrieved on 25 April 2018 from https://landsat.gsfc.nasa.gov/sentinel-2a-launches-our-compliments-our-complements/)](./figures/landsat_sentinel2.png)

Currently two Sentinel-2 satellites, known as Sentinel-2A and -2B, are continuously and systematically collecting observations. They were launched on June 23, 2015 and March 7, 2017, respectively, with Sentinel-2C and -2D already planned in the future to extend the longevity of Sentinel-2 observations. Looking at observations from both satellites together, the nominal average revisit time at the equator is every 5 days, with more frequent data capture towards the poles. Data are processed and provided by the European Space Agency (ESA) as level-1C (L1C), which includes radiometric calibration to top-of-atmosphere (TOA) reflectance and geometric corrections (e.g. orthorectification, spatial registration). L1C scenes are available as granules (i.e. tiles). These granules each cover approximately 100km by 100km and contain around 600MB of data, including all spectral bands, metadata and some quality indicators generated by ESA.



#### Data Used

- selected metadata in annex
- mention automated workflow described below

### Auxiliary data

- Irrigated Areas (GMIA or Irrmap)
- Syria Refugee Sites (<https://data.humdata.org/dataset/syria-refugee-sites>)
- Precipitation or drought data
- mention acquisition/ingestion, etc. or reference below



## Methods

- overview of workflow, covering what was programmed by author (focus on automation and big data)

The automated workflow encompasses downloading Sentinel-2 data, re-formatting, preliminary classification with SIAM™ (i.e. information layer creation), indexing images and ingesting information  layers into an implementation of the ODC (Figure 2). This process runs automatically every day for each of the three study area granules. The result is daily incorporation of the most recently available data ready for analysis including semantic queries. At the time of writing, 479 Sentinel-2 images from June 28, 2015 until January 31, 2018 have been processed.

### Indicator development

- reference water in Australia and apply/situate in framework referenced in section 2
- describe two indicators (water, vegetation ...)

### Automatic knowledge-based spectral categorisation

#### Theory

Image pre-classification is an initial classification of remotely sensed images for use in image understanding workflows. According to Marr (1982), human vision begins with a pre-attentive first stage. The output is a symbolic primal sketch, including both a raw and final version. The raw primal sketch is pure spectral differentiation of grey shades and colour tones and the final primal sketch groups similar shades and tones. Pre-classification is a primal sketch in the Marr sense, where semi-concepts are groups of spectrally similar pixels.

Semi-concepts are considered semi-symbolic in that they are an initial step in connecting sensory data (i.e. pixel values) to symbolic, semantic classes (Baraldi & Boschetti, 2012). They require further context, analysis, or additional information to classify pixels into symbolic classes, such as land cover classes. More general to more detailed semi-concepts may be considered a sort of multi-scale segmentation (Baraldi & Boschetti, 2012).

#### SIAM(TM)

This implementation uses multiple semantic semi-concept granularities generated by the Satellite Image Automatic Mapper™ (SIAM™, release 88v7) to enable semantic queries (Baraldi, 2018). Spectral-based image pre-classification, as implemented by SIAM™, divides the feature space of a multi-spectral image into semantic semi-concepts using a knowledge-based approach, in contrast to data-driven approaches (e.g. supervised classification) (Baraldi et al., 2010a; Baraldi, 2011, 2018). A physical model-based decision-tree using a priori knowledge of spectral profiles is the foundation applied to each pixel for the spectral categorisation. Assuming images are calibrated to a minimum of TOA reflectance, these semi-concepts are comparable and therefore transferable between multiple images and optical sensors without any additional user defined parametrisation (i.e. fully automatic).

### Hardware

- describe server

The hardware used for this implementation is a Red Hat Enterprise Linux 7 virtual machine, with 16 virtual central processing units (CPUs) at 2.5 GHz clocking, 31 GB random-access memory (RAM) and 3TB of generic, all-use storage.

### Software

#### Open-Source

- Python, virtual environments, etc.
- ODC
- Jupyter notebooks

##### Linux

##### Python

Two reproducible virtual conda environments for Python are used. Data download to processing with SIAM™ is automated using cron, Python scripts and a conda environment with Python 2.7.x, Scipy, Geospatial Data Abstraction Library (GDAL) and requests. Data cube indexing, ingestion, Python API access and resulting analysis are conducted with a conda environment recommended for ODC installations (Geoscience Australia, CSIRO, & NCI, 2017d). This Python 3.5.x environment includes the datacube, jupyter, matplotlib, Scipy, basemap and basemap-data-hires packages for working with existing Jupyter notebooks (CEOS-SEO, 2016/2017) and ones created by the authors.

##### Git

##### Open Data Cube (ODC)

The Open Data Cube (ODC) initiative evolved from the Australian Geoscience Data Cube (AGDC) with the objective to provide a means to store, manage and analyse large volumes of EO data. The initiative has three goals: (i) increase the impact of EO; (ii) build a community; and (iii) provide free and open software, including documentation (CEOS, 2017). Since CEOS is one of the founding members, the initiative seeks to align EO with overarching international agendas, such as the United Nations Sustainable Development Goals (SDGs). By fostering continental and global scale applications, the ODC initiative aims to improve the use of EO-based information by decision-makers.

By following the data cube paradigm, ODC is conceptually comparable to array databases such as rasdaman (Baumann et al., 1998) and SciDB (Stonebraker et al., 2013) or database extensions such as SciQL (Kersten et al., 2011; Zhang et al., 2011). Indexing generates a metadata database with the location and properties of EO data, while ingestion generates its own data storage as NetCDF files. In both cases, the logical view offered to the user is a multi-dimensional data cube. Access is provided either by a Python application programming interface (API), e.g. usable in a Jupyter notebook (Kluyver et al., 2016), or using the ODC Web-based user interface. While a detailed review goes beyond the scope of this paper, the ODC has been selected because the data cube paradigm is best suited for the application, the technology has been proven to be robust and scalable, and allows for transferable approaches through its open source license.

##### Jupyter Notebooks

##### Open Science Framework

#### Other

##### SIAM™

### Automated Workflow: Sentinel-2 Data Acquisition to ODC Ingestion

- (scripts in annex)
- big EO data
- scalable -- perhaps add some sort of benchmark


#### Accessing and Acquiring Sentinel-2 data

A command line interface (CLI) was implemented in Python, similar to the work of Olivier Hagolle (2015/2018), to query the Copernicus Open Access Hub's API. Temporal restraints are set based on start and end times for data acquisition or ingestion to the hub. Spatial restraints can be set based on a point,
polygon or granule name, the latter of which is based on an in-house API that returns 171 the centre point of any existing Sentinel-2 granule by name. If this option is used, only the identified granule is downloaded  from any matching results, including targeted extraction from older, multiple granule products in the archive from before December 6, 2016. Results are automatically unzipped, and any products already located in the target directory are not downloaded again.

The structure of products and metadata has seen a few modifications since the first images offered to the public in 2015. Most notably, Sentinel-2 products were served in packages of multiple granules prior to December 6, 2016, with sizes sometimes exceeding 6 GB.



#### Formatting data for SIAM™

Following complete and successful download, the necessary bands from each newly acquired Sentinel-2 image are automatically re-formatted. SIAM™ is sensor independent, but input data format requirements are based on Landsat for high-resolution data. Six bands, blue, green, red, near infrared and two medium infrared bands are used (i.e. 2, 3, 4, 8, 11, 12). Bands 11 and 12 are resampled from 20m pixels to 10m using SciPy. Sentinel-2's MSI does not collect a thermal band, so a constant is used to ignore thermal decision rules in SIAM™. Based on the assumption that pixels with a value of 0 in any of the input bands contain no data and not a measured value of 0 (see discussion), a no-data mask is generated. Finally, the six Sentinel-2 bands of TOA reflectance values are converted to an 8-bit range, stacked in ascending order and saved in the ENVI data format for SIAM™ using GDAL.

#### Generating information layers with SIAM™

Automated generation of four semi-concept granularities (i.e. 18, 33, 48 and 96 semi-concepts) and four additional information layers has been implemented. The additional layers are: (1) binary vegetation mask based on vegetation-related semi-concepts; (2) pentanary haze mask, a discretised continuous sub192
symbolic variable; (3) ratio greenness index, i.e. (NIR / R) + (NIR / MIR1) – (Vis / MIR1) (Baraldi et al., 2010b; Baraldi, 2018); and (4) panchromatic brightness image, a linear combination of all multi-spectral input bands. Processing of each Sentinel-2 image takes approximately 5-6 minutes.

#### Indexing images and information layers

A product description needs to be defined in the ODC implementation database to index data. Indexing links to externally stored data and is backed by PostgreSQL. Product descriptions identify metadata common to all datasets of that product (Geoscience Australia, CSIRO, & NCI, 2017b) and only need to be defined once.

Metadata necessary for indexing is automatically generated for each dataset. This has been implemented for Sentinel-2 and SIAM™ generated information layers by modifying existing Python scripts provided by ODC. This metadata includes spatio-temporal data extents, data format, projection, bands/layers, file paths relative to the metadata, and more. A copy of the source Sentinel-2 dataset’s metadata is included the information layer metadata to document provenance. Once metadata has been generated, indexing automatically follows using a Python script and the data cube’s API.

#### Ingesting information layers

Once data has been indexed, it can be ingested, meaning automated tiling of an indexed product into NetCDF files for more efficient access, creating a gridded time-series data cube (Geoscience Australia, CSIRO, & NCI, 2017c). The data cube API automatically creates a new product description, re-projects the data if necessary, tiles them accordingly, creates the necessary metadata and indexes them, with automatic checks to avoid duplication.

In this implementation, automated ingestion of information layers in 100km2 tiles (10km by 10km by one time-step) occurs, keeping the original projection (i.e. UTM zone 37N, EPSG: 32637). At the time of writing, 58,477 tiles of ingested information layers have been created, a total of 144 GB.

![Automated workflow overview from download to queries and indicator extraction, which utilises the Python API.](.\figures\Workflow_helvetica.png)

### ODC Python API

Once data has been indexed and ingested, they can be accessed using a Python API (Geoscience Australia, CSIRO, & NCI, 2017a). This API retrieves data from a given indexed or ingested product for a defined spatio-temporal extent as a Dataset object from the xarray Python package. This Python object is a multi-dimensional, in memory, array with dimension names and is used for further analysis (e.g. in Jupyter notebooks).

### Jupyter Notebook for Indicator Calculation

- Include screenshots


### Method of validation/agreement

- random samples and an external dataset