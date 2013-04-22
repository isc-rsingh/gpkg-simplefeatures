# GeoPackage Extension for Simple Features Geodata

## 5 Introduction

An RDBMS container store with SQL access to simple features with geometry is desired to manage (create, update, delete as well as search and retrieve) both geospatial foundation data for multiple types of features, and newly collected feature observation data. Support is provided for the basic simple feature geometry types – Geometry, Point, LineString, LinearRing, Polygon, GeometryCollection, MultiPoint, MultiLineString, and MultiPolygon.

Fortunately there are applicable international specifications that have standardized practices for the storage, access and use of vector geospatial features and geometries via SQL in relational databases.  The original Simple Features for SQL specification OGC 99-049 [15] and its successors OGC 06-103r4 13] and 06-104r4 [14] (ISO 19125) describe the common architecture for simple feature geometry, define Well Known Binary (WKB) and Well Known Text (WKT) encodings for geometries, and define a standard Structured Query Language (SQL) schema that supports storage, retrieval, query and update of feature collections via the SQL Call-Level Interface (SQL/CLI) [5]. They standardize:

- Names and geometric definitions of the SQL Types for Geometry
- Names, signatures and geometric definitions of the SQL Routines for Geometry

The first component of the standard SQL schema for simple features in a GeoPackage is the spatial_ref_sys table defined in clause x.x.x of [GeoPackage Core].

> NOTE:  The SF-SQL[15][13][14] and SQL-MM for Geospatial [16] set of standards has been a guiding force in the standardization of spatial RDMBS system capabilities across multiple commercial and open source spatial RDMBS and GIS implementations.  Before these standards were adopted and widely implemented, identical spatial queries with spatial predicates on the same data sets returned different results from almost every available spatial database and GIS implementation. These standards are cited here to avoid repetition of that experience on mobile / handheld platforms.

> NOTE:  See clause x.x.x for the definition of optional minimal runtime SQL functions in a GeoPackage, some of which are defined in the standards above.

> NOTE:  See clause x.x.x for the definition of optional spatial indexes on geometries stored in a GeoPackage.

## 6 File Format

Simple features geodata is stored in a file format as specified in [GeoPackage Core].

## 7 Data Format

### 7.1 Geometry Columns Table

A component of the standard SQL schema is a table or updateable view specified in OGC 06-104r4 [14] section 7.1.3.1 that identifies the geometry columns in tables that contain data representing simple features.  This table or updateable view SHALL contain one record for each geometry column in each vector feature data table (clause 6.3.6.1) in a GeoPackage.

**Table 7-1** - `geometry_columns` Table or View Definition

|Column Name       |Column Type|Column Description|Key|
|------------------|-----------|------------------|---|
|f_table_name      |text       |Name of the table containing the geometry column|PK,FK|
|f_geometry_column |text       |Name of a column in the feature table that is a Geometry Column|PK|
|geometry_type     |integer    |Code from OGC 06-104r4 Table 4, except those for polyhedral surfaces: 0=geometry (2D), 1=point (2D)…| |
|coord_dimension   |integer    |Number of ordinates, +1 if geometry_type includes an “M” Measure dimension| |
|srid              |integer    |Spatial Reference System ID: spatial_ref_sys.id|FK|

The FK on geometry_columns.srid references the PK on spatial_ref_sys.srid to ensure that geometry columns are only defined in feature tables for defined spatial reference systems.  This table or view may include additional columns to meet the requirements of implementation software or other specifications.

See Annex B: Table Definition SQL clause B.6 geometry_columns.

|Requirement|Core|
|-----------|----|
|URI        |http://www.opengis.net/spec/GPKG/1.0/req/ext/simplefeatures/geopackage_columns_table|
|REQ 1      |A GeoPackage SHALL include a table or updateable view named `geometry_columns` that at a minimum includes the columns and foreign key constraints defined in Table 7-1|

### 7.2 Geometry

A GeoPackage SHALL store all geometry SQL BLOBs using the OptimizedGeoPackageBinary format specified in this clause.


```C
OptimizedGeoPackageBinary {
  byte[3] magic = 0x47504230; // 'GPB'
  byte flags;                 // see flags layout below
  unit32 srid;
  double[] envelope;          // see flags envelope contents indicator code below
  WKBGeometry geometry;       // per OGC 06-103r4 clause 8
}

```

**TABLE 7-2** - Geometry Binary Format flags layout

|bit|7|6|5|4|3|2|1|0|
|---|-|-|-|-|-|-|-|-|
|**use**|V|V|V|V|E|E|E|B|
|use: v=version number (4-bit unsigned integer) 0 = version 1|||||||||


**Table 7-3** - Envelope contents indicator code (3-bit unsigned integer)

|code value|description                             |envelope length (bytes)|
|----------|----------------------------------------|-----------------------|
|0         |no envelope (space saving slower indexing option)|0|
|1         |envelope is [minx, maxx, miny, maxy]|32|
|2         |envelope is [minx, maxx, miny, maxy, minz, maxz]|48|
|3         |envelope is [minx, maxx, miny, maxy, minm, maxm]|48|
|4         |envelope is [minx, maxx, miny, maxy, minz, maxz, minm, maxm]|64|
|B: byte order for header values (1-bit Boolean) |||
|	0 = Big Endian (most significant bit first) |||
|	1 = Little Endian (least significant bit first) |||


## 8 Global Tables

This extension defines no additional global tables beyond those specified in [GeoPackage Core].

## 9 User Data Tables

### 9.1 Vector Feature Data Tables

Another component of the standard SQL schema for simple features described in clause 5 [introduction] above are tables that contain data representing simple features and geometries. A feature is an abstraction of a real-world object. Feature attributes are columns in a feature table. Features are rows in a feature table. The Geometry of a feature is stored in a Geometry Column whose type is drawn from a set of SQL Geometry Types.

GeoPackage implementations shall implement feature tables and geometry columns according to the "SQL implementation using Geometry Types" architecture as defined in section 6.2 of OGC 06-104r4 [11].

All geometry types and relational operators implemented by a GeoPackage SHALL conform to the geometry architecture model specified in section section 6.2.6 and Figure 4 “SQL Geometry Type Hierarchy” of OGC 06-104r4[14].   At a minimum a GeoPackage SHALL support the GEOMETRY, POINT, LINESTRING, POLYGON, GEOMETRYCOLLECTION, MULTIPOINT, MULTILINESTRING and MULTIPOLYGON geometry types.

|Requirement|Core|
|-----------|----|
|URI        |http://www.opengis.net/spec/GPKG/1.0/req/ext/simplefeatures/geometry_types|
|REQ 2      |A GeoPackage SHALL encode GEOMETRY, POINT, LINESTRING, POLYGON, GEOMETRYCOLLECTION, MULTIPOINT, MULTILINESTRING and MULTIPOLYGON geometry types in feature tables as specified in OGC 06-104r4|

> NOTE: SQLite does not support user-defined types, but does allow arbitrary type names to be used when declaring columns in a table. The declared type of a geometry column shall match one of the geometry type names specified above. The internal data type shall be a BLOB. The binary encoding of geometries is specified in clause 6.2.1 above.

GeoPackage implementations SHALL assure that the geometry values stored in a geometry column of a feature table are of a geometry type that is compatible with (the same as or a subclass of) the geometry type specified for the column in the geometry_columns.geometry_type value for the geometry column, and are of the SRID specified for the geometry column in the geometry_columns.srid for the column by implementation of the feature geometry validation triggers specified in clause 7.3.5." NOTE2:  A feature type may be defined to have 0..n geometry attributes, so the corresponding feature table may contain from 0..n geometry columns.

> NOTE:  A feature type may be defined to have 0..n raster attributes, so the corresponding feature table may also be a raster table that contains from 0..n raster columns.  See clause 6.3.4.2 below.

Every feature table in a GeoPackage SHALL have aprimary key defined on one integer column so that row level metadata records may be linked to the feature records in it by rowid as described in clause 6.3.2.4.3 above.  

> NOTE:  A GeoPackage is not required to contain any feature data tables.

> NOTE:  Feature data tables in a GeoPackage may be empty. 
