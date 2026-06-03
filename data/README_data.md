# Data

## Primary Seismic Catalog

The seismic event catalog used in this study was compiled and 
curated by the authors from the USGS FDSN Event Web Service 
and represents original data curation work. It is not 
redistributed in this repository.

Researchers may reproduce an equivalent catalog directly from:

**USGS FDSN Event Web Service:**  
https://earthquake.usgs.gov/earthquakes/search/

Use the following parameters:
- Event type: earthquake
- Minimum magnitude: 2.5
- Start time: 1900-01-01
- End time: 2026-01-01
- All geographic regions

## Geophysical Context Layers

All three geophysical context layers are openly and freely 
available from their original providers:

**GEM Global Active Faults Database**  
Styron and Pagani (2020)  
https://github.com/GEMScienceTools/gem-global-active-faults

**PB2002 Tectonic Plate Boundary Dataset**  
Bird (2003)  
https://github.com/fraxen/tectonicplates

**GSHAP Global Seismic Hazard Map**  
Giardini et al. (1999)  
http://gmo.gfz-potsdam.de/

## Synthetic Sample for Pipeline Testing

A 100-event synthetic dataset (`sample_events.csv`) is 
provided in this folder for testing the code pipeline 
without the full catalog.

Columns: time, latitude, longitude, depth, mag

This sample contains randomly generated values and does 
not represent real seismic events. It is provided solely 
to verify that the code runs correctly.

## Data Statement

In compliance with the Computers & Geosciences Option C 
data sharing policy, the processed seismic catalog is not 
publicly redistributed as it represents original data 
curation work by the authors. All raw source data are 
freely accessible from the providers listed above. 
Processed data are available upon reasonable request 
to the corresponding author at abassamrajbd65@gmail.com