# GeoPackage Extension for Simple Features Geodata

## Introduction

An RDBMS container store with SQL access to simple features with geometry is desired to manage (create, update, delete as well as search and retrieve) both geospatial foundation data for multiple types of features, and newly collected feature observation data. Support is provided for the basic simple feature geometry types – Geometry, Point, LineString, LinearRing, Polygon, GeometryCollection, MultiPoint, MultiLineString, and MultiPolygon.Fortunately there are applicable international specifications that have standardized practices for the storage, access and use of vector geospatial features and geometries via SQL in relational databases.  The original Simple Features for SQL specification OGC 99-049 [15] and its successors OGC 06-103r4 13] and 06-104r4 [14] (ISO 19125) describe the common architecture for simple feature geometry, define Well Known Binary (WKB) and Well Known Text (WKT) encodings for geometries, and define a standard Structured Query Language (SQL) schema that supports storage, retrieval, query and update of feature collections via the SQL Call-Level Interface (SQL/CLI) [5]. They standardize:
- Names and geometric definitions of the SQL Types for Geometry- Names, signatures and geometric definitions of the SQL Routines for GeometryThe first component of the standard SQL schema for simple features in a GeoPackage is the spatial_ref_sys table defined in clause x.x.x of [GeoPackage Core].
> NOTE:  The SF-SQL[15][13][14] and SQL-MM for Geospatial [16] set of standards has been a guiding force in the standardization of spatial RDMBS system capabilities across multiple commercial and open source spatial RDMBS and GIS implementations.  Before these standards were adopted and widely implemented, identical spatial queries with spatial predicates on the same data sets returned different results from almost every available spatial database and GIS implementation. These standards are cited here to avoid repetition of that experience on mobile / handheld platforms.> NOTE:  See clause x.x.x for the definition of optional minimal runtime SQL functions in a GeoPackage, some of which are defined in the standards above.> NOTE:  See clause x.x.x for the definition of optional spatial indexes on geometries stored in a GeoPackage.## File Format
Simple features geodata is stored in a file format as specified in [GeoPackage Core].

## Data Format


## Global Tables

