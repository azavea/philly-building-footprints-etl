# philly-building-footprints-etl

An ETL job for a blog post about tiling vector data with PostGIS, QGIS, and QTiles.

## Requirements

* Docker
* Rake
* cURL
* GDAL
* QGIS

## Setup

To ingest building footprint data for Pennsylvania into PostGIS and create a table
of Philadelphia building footprint data, make sure Docker is running and then run:

```sh
rake build
```

## Rake tasks

| Task | Description |
| ---- | ----------- |
| build | Create database, import Pennsylvania building data, create Philadelphia building data table |
| clean | Remove Pennsylvania building footprint data files |
| psql | Use `psql` on running PostGIS container |

