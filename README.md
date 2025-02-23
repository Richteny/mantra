[![DOI](https://zenodo.org/badge/495761275.svg)](https://zenodo.org/badge/latestdoi/495761275)

# MANTRA
Welcome to the home of MANTRA, the "MountAiN glacier Transient snowline Retrieval Algorithm" -- a Google Earth Engine (GEE) toolset to process and evaluate glacier surface classifications and Transient Snowline Altitudes (TSLA) from Landsat and Digital elevation model data.

The repository contains a "Parallel Processing Tool" for comprehensive TSLA datasets and the "Evaluate Tool" to visualize classification and TSLA results for individual glaciers. 

<img src="https://github.com/cryotools/mantra/blob/main/supplement/mantra-evaluate-screenshot.png" alt="Screenshot of the MANTRA Evaluate Tool">

Please note that results may be flawed, e.g. by cloud cover. For analyses at individual glaciers, it is advised to check the results in the Evaluate Tool by comparing the classification with the Landsat image by toggling layers before. For larger data sets filtering based on quality metrics is mandatory before further analyses.

**Features**
- Classify surface materials based on Landsat band-ratios (snow, ice, debris/rock, clouds)
- Create a catalogue of relevant Landsat scenes from multiple missions (Landsat 4 to 8)
- Merge scenes taken at the same date that are covered by the glacier's outline
- Mask pixels with insufficient illumination
- Calculate metrics and statistics
- Visualize the classification and save it to a geodcoded image file in Google Drive (Evaluate Tool only)
- Write the results to a CSV file in Google Drive (parallel process)
- Do all this in parallel for (very) many glaciers and Landsat scenes.


# Setup

1. Log in to your [GEE account](https://code.earthengine.google.com/).
2. Head over to the "Assets" tab and add a RGI6 shapefile (e.g. the one provided in the "RGI" directory in this repository).
3. Go back to the "Scripts" tab and create two new files called "proc_parallel", "evaluate" and "core" or similar.
4. Copy the contents of "proc_parallel.js", "evaluate.js" and "core.js" from this repo into the respective files in your GEE environment.
5. Edit the parallel processing and evluate scripts so that the variables "rgi_path" and "core" point at the correct files in your GEE environment (rgi_path referring to the RGI6 shapefile you just loaded to the Assets). 

That's it, you should now be ready to ride the MANTRA.

# Running the MANTRA Evaluate Tool
1. In the "evaluate" script, edit the variables `glacier_list`, `start` and `finish` to meet your study subject:
    - For `rgi_id` enter, well, the RGI ID of the glacier. In case you used the RGI shapefile provided with this repo, the initial values should work just fine.
    - `start` and `end` refer to period of time to investigate. Make sure to keep it short (max. ~six months) to avoid GEE stalling.
2. Click "Save" and "Run".
3. When GEE has finsihed loading, the dates for which adequate Landsat scenes are available can be selected (combo box on the top right of the map window). Details of the Landsat scenes can be investigated in the "Console" tab.
4. After selecting a date from the list, GEE will start the processing (may take a while).
5. After processing has finished, the classification results will be shown in the map, statistics in the pane on the right-hand side.
6. Optionally, you may save the result as geocoded image file to Google Drive. To do so, head over to the "Tasks" tab and click "run" next to the prepared job. The resulting image will have five classes coded as: 
    - (0) all non-classified pixels including those off-glacier, affected by Landsat scan-line errors or shadows, 
    - (1) snow-covered pixels, 
    - (2) pixels classified as ice, 
    - (3) pixels classified as debris, and 
    - (4) pixels that do not belong to any of the previous categories, particularly cloud cover. 

# Running the MANTRA Parallel Processing Tool
1. In the "parallel processing" script, edit the variables `package_number`, `rgi_id`, `start` and `finish` to meet your study subject:
    - `package_number` can be chosen freely. It will be used as identifier for the job and file name of the result file. 
    - The `glacier_list` must be a list of RGI IDs contained in the RGI v6 shapefile in the Assets, e.g. `['RGI60-13.53885', 'RGI60-13.53885']`. The list may include hundreds or thousands of glaciers (please consider the recommendations below). 
    - `start` and `end` refer to period of time to investigate.
2. Click "Save" and "Run". The number of glaciers in the job should be displayed in the Console tab.
3. Switch to the Tasks tab. The should be displayed there; click "Run" to send it to the processing queue.
4. Have a coffee. Processing may take a while (up to days for large jobs). Monitor the status in the Tasks tab.
5. After the job has finished, the results will be stored as CSV file in the Google Drive of the active user.

# Recommendations for parallel processing
- Do not make processing packages too big to avoid time-outs. Splitting your lists of RGI IDs into smaller packages also makes it possible to process several packages in parallel (usually 5 to 20 in our experience, depending availability of processing resources for GEE).
- Manage the glacier lists of comprehensive processing tasks using a spreadsheet or similar.
- The processing time is strongly influenced by the size of the studied glaciers. When creating lists of RGI IDs, consider the area of the glaciers at hand, e.g. using a small (Python or similar) script with an threshold for the summed up area per package.
- Start with few glaciers and a short period of time and test your setup thoroughly before moving on to full-scale processing (may save you nerves and the environment a lot of wasted electricity).
- When jobs fail, check the error message provided in the Tasks tab. Typical reasons are, that a package contained too many glaciers or that an individual scene/glaciers failed. Both cases can be treated by braking down the glacier list into mutliple smaller lists (use the speadsheet from second tip) and process these again. Iterate as necessary until all jobs succeed or the failing glacier/scene is identified.

# Citing MANTRA
If you publish work based on MANTRA, please cite as:

David Loibl (2022): MountAiN glacier Transient snowline Retrieval Algorithm (MANTRA) v0.8.3, doi: [10.5281/zenodo.8044734](https://doi.org/10.5281/zenodo.8044734)

# Acknoledgements
MANTRA was developed within the research project "TopoClimatic Forcing and non-linear dynamics in the climate change adaption of glaciers in High Asia" (TopoClif). TopoCliF and David Loibl's work within the project were funded by [DFG](https://gepris.dfg.de/gepris/projekt/356944332) under the ID LO 2285/1-1.

The analysis of spectral charateristics, development of band-ratios and identification of adequate thresholds was supported by [Inge Grünberg](https://orcid.org/0000-0002-5748-8102). Inge's contribution to this project was supported by [Geo.X](https://www.geo-x.net/), the research network for Geosciences in Berlin and Potsdam.

The feature to save geocoded images of classification results was added to the Evaluate Tool by [Niklas Richter](https://orcid.org/0000-0002-8833-0420).

