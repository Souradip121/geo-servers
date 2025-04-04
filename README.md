# COG Download and Processing API

A Flask server for downloading and processing Cloud Optimized GeoTIFFs (COGs) based on an area of interest.

## Features

- Download single-band COGs from TiTiler
- Download multi-band COGs with RGB bands
- Stack multiple COGs into a single multi-band image
- Apply min/max rescaling based on URL parameters
- Crop COGs to a specific area of interest (AOI)
- Output in TIFF or PNG format

## Installation

### Using Conda (Recommended)

1. Clone this repository
2. Create and activate the conda environment:

```bash
# Create the environment from the environment.yml file
conda env create -f environment.yml

# Activate the environment
conda activate geo-cog-server
```

### Using pip

1. Clone this repository
2. Install required dependencies:

```bash
pip install -r requirements.txt
```

## Running the Server

```bash
python app.py
```

The server will run on port 5000 by default.

## API Endpoints

### 1. Download COG

`POST /download-cog`

Downloads a COG from TiTiler based on an Area of Interest (AOI).

#### Request Body

```json
{
  "url": "https://titiler.example.com/cog/viewer?url=https://example.com/cog.tif",
  "aoi": {
    "type": "Polygon",
    "coordinates": [[[x1, y1], [x2, y2], [x3, y3], [x1, y1]]]
  },
  "format": "tiff"  // Optional, defaults to "tiff". Can also be "png"
}
```

Or using bounding box coordinates:

```json
{
  "url": "https://titiler.example.com/cog/viewer?url=https://example.com/cog.tif",
  "aoi": [minx, miny, maxx, maxy],
  "format": "tiff"  // Optional, defaults to "tiff"
}
```

#### Response

The endpoint returns the processed COG file as an attachment.

### 2. Stack Multiple COGs

`POST /stack-cogs`

Downloads multiple COGs and stacks them into a single multi-band GeoTIFF.

#### Request Body

```json
{
  "urls": [
    "https://titiler.example.com/cog/viewer?url=https://example.com/cog1.tif&rescale=0,100",
    "https://titiler.example.com/cog/viewer?url=https://example.com/cog2.tif&rescale=0,100",
    "https://titiler.example.com/cog/viewer?url=https://example.com/cog3.tif&rescale=0,100"
  ],
  "aoi": {
    "type": "Polygon",
    "coordinates": [[[x1, y1], [x2, y2], [x3, y3], [x1, y1]]]
  },
  "format": "tiff"  // Optional, defaults to "tiff". Can also be "png"
}
```

#### Response

The endpoint returns the stacked COG file as an attachment.

## Examples

### 1. Download a Single-Band COG

```bash
curl -X POST "http://localhost:5000/download-cog" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://titiler.example.com/cog/viewer?url=https://example.com/cog.tif&rescale=0,100",
    "aoi": [10.0, 20.0, 11.0, 21.0]
  }'
```

### 2. Download a Multi-Band COG

```bash
curl -X POST "http://localhost:5000/download-cog" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://titiler.example.com/cog/viewer?url=https://example.com/cog.tif&r=1&g=2&b=3&rescale_range.1.0=0&rescale_range.1.1=100&rescale_range.2.0=0&rescale_range.2.1=100&rescale_range.3.0=0&rescale_range.3.1=100",
    "aoi": [10.0, 20.0, 11.0, 21.0]
  }'
```

### 3. Stack Multiple COGs

```bash
curl -X POST "http://localhost:5000/stack-cogs" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      "https://titiler.example.com/cog/viewer?url=https://example.com/cog1.tif&rescale=0,100",
      "https://titiler.example.com/cog/viewer?url=https://example.com/cog2.tif&rescale=0,100",
      "https://titiler.example.com/cog/viewer?url=https://example.com/cog3.tif&rescale=0,100"
    ],
    "aoi": [10.0, 20.0, 11.0, 21.0]
  }'
```

### 1. Download a Single-Band COG as PNG

```bash
curl -X POST "http://localhost:5000/download-cog" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://titiler.example.com/cog/viewer?url=https://example.com/cog.tif&rescale=0,100",
    "aoi": [10.0, 20.0, 11.0, 21.0],
    "format": "png"
  }'
```
