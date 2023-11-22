# Instance Segmentation of Buildings in Satellite Imagery
These notebooks take a GeoTIFF satellite image and a GeoJSON file containing building footprints and trains a U-Net model to segment the buildings in the image. The model is then used to predict the building footprints when given tiles from a new satellite image.

## Pre-processing
The first notebook, `preprocessing.ipynb`, takes a GeoTIFF satellite image and a GeoJSON file containing building footprints and creates training and validation datasets made up of image tiles and corresponding 3 channel RBG Footprint, Boundary, Contact (FCB) masks. The FCB masks are used to train the model to segment the buildings in the image. This is done by drawing the training and validation polygon areas using `geojson.io`, and then using `supermercado` from a bash terminal to convert these areas into a collection of square tile polygons. `rio-tiler` and `rasterio` are used to get the building outline polygons within each tile polygon and `solaris` is used to burn FCB masks from these tiles. The tiles and masks are then saved as PNG files using a naming convention that allows the tiles and masks to be matched up later.

## Training
The second notebook, `training.ipynb`, takes the training and validation datasets created in the first notebook and uses transfer learning to train a U-Net model to segment the buildings in the image. The model is trained on a remote GPU using the `fastai` library, which is built on top of `PyTorch`. Amazon EC2 on AWS is used to create a remote Linux machine which is accessed using ssh.