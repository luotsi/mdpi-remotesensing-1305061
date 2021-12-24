# vegcover-manuscript-12_2021
This code is to accompany the article manuscript "Density Estimates as Object Representations of Agricultural Fields in Remote Sensing".

The role of this repository is to assist in verification of authenticity of research, accompany article text, 
illustrate the data flow of the proposed framework, and to provide implementation details for the reader, 
not as code ready to be run in production.
The code is a Docker-enabled version of the code used for our study that is set up to run essential parts of the 
experiments to demonstrate the data flow of the pipeline including realistic data without requiring 
e.g. a Matlab license or high-power computing resources that we used for computing Stan/MCMC results for LGPDE.
This code is provided as is, with no guarantees or liabilities. 
Proper operation requires input data in appropriate format, and adjustment of the data paths in the configuration files.

## Note on data
Complying with the EU General Data Protection Regulation (GDPR) and a data usage license granted by the Finnish Food Authority, 
we have anonymized the derived dataset of this example in directory ```vegcover-manuscript-12_2021/data/```, 
i.e. we have run a salted SHA-256 irreversible hash on parcel identifiers 
and removed all geometry information to make individual parcels unidentifiable. 


## Hardware & software requirements
- Linux or MacOS
- \>= 16GB RAM. 20GB Hard disk space.
- Docker
- For running outside of docker: git, conda and Python3

## Running example experiments on Docker for the data of our study:
- ```mkdir out```
- ```docker run -v (pwd)/out:/out -it luotsi/vegcover-manuscript-12_2021.latest ./sar_10_to_300.sh```
- or ```docker run -v (pwd)/out:/out -it luotsi/vegcover-manuscript-12_2021.latest ./msi_sar_fusion_6500.sh```


## Running the pipeline outside of docker
- Requires conda
- ```git clone https://github.com/luotsi/vegcover-manuscript-12_2021.git```
- ```cd vegcover-manuscript-12_2021```
- ```conda create -n vegcover-manuscript-12_2021 python=3.7```
- ```conda activate vegcover-manuscript-12_2021```
- ```conda install numpy scikit-learn pandas pyyaml bzip2 pystan rasterio shapely fiona```
- in config.yml, adjust pipeline / output_root_dir to a suitable output directory
- run e.g. ```python -u -t sar_10_to_300.yml```

## Generating your own data
The software accepts
- .tiff rasters extracted from S1/S2 imagery. Required image channel order is indicated in config.yml .
- delineation polygons as ESRI .shp shapefiles in the same projection as the .tiffs. 


### Generating .tiff raster files from Sentinel-1/Sentinel-2 imagery

Construct .tiff files from Sentinel-1 and Sentinel-2 images using e.g. ESA SNAP and QGIS. Refer to config.yml for S2 band export order. S1 intensity band order is VH, VV. Use common projection for .tiff files as for .shp below. We used EPSG3067 (ETRS89/TM35FIN(E,N)).

### Polygon shapefiles 
Polygons in the ESRI .shp shapefiles are required to have the following property fields: parcelID (values: arbitrary string ids), split (values: train/test)

### Extracting the pixel sets per object: 

Once you have produced the raster .tifs and annotated .shp shapefiles and adjusted entries in config.yml / pixel_sampler, run make_datasets.sh . The code produces a train and a test dataset for SAR and MSI datasets, e.g. those referred to in the pipeline sample configurations:```train_s2_ndti_ndvi.pkl, test_s2_ndti_ndvi.pkl, train_s1_vh_vv.pkl, test_s1_vh_vv.pkl```
