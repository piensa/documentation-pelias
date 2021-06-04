# Reverse geocoding

Reverse geocoding is used for finding places or addresses near a latitude, longitude pair&mdash;like clicking on a map to see what's there when the map doesn't show it otherwise. For example, picture a map showing building outlines but no labels, then clicking on a building and being shown the name of the business. That's reverse geocoding.

With reverse geocoding with Pelias, you can look up all sorts of information about points on a map, including:

* addresses
* points of interest (businesses, museums, parks, and so on)
* neighborhoods
* cities
* states
* postal areas
* countries

To get started with reverse geocoding, you need a latitude, longitude pair in decimal degrees specified with the parameters `point.lat` and `point.lon`, respectively.  For example, the Eiffel Tower in Paris, France, is located at `18.0149849645,-76.7522999252`. The reverse geocode query for this would be:

>[/v1/reverse?__point.lat=18.0149849645_&__point.lon=-76.752299925__](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925)

The output is the standard GeoJSON format.

## Reverse geocoding parameters

Like other queries with Pelias, reverse geocoding has optional, additional parameters you can use to refine results.

Parameter | Type | Required | Default | Example
--- | --- | --- | --- | ---
`point.lat` | floating point number | yes | none | `48.858268`
`point.lon` | floating point number | yes | none | `2.294471`
`boundary.circle.radius` | floating point number | no | 1 | `35`
`size` | integer | no | `10` | `3`
`layers` | comma-delimited string array | no | none (all layers) | `address,locality`
`sources` | comma-delimited string array | no | none (all sources) | `oa,gn`
`boundary.country` | comma separated list of <a href="https://en.wikipedia.org/wiki/ISO_3166-1" target="\_blank">ISO-3166 alpha-2 or alpha-3</a> | no | none | `FR,GBR`
`boundary.gid` | Pelias `gid` | no | none | `whosonfirst:locality:101748355`

### Size

A basic parameter for filtering is `size`, which is used to limit the number of results returned. To limit a reverse geocode to only the first result, pass the `size` parameter:


>[/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&___size=1___](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&size=1)

The default value for `size` is `10` and the maximum value is `40`. Specifying a value greater than `40` will override to `40` and return a warning in the response metadata.

### Filter by data source

By default, reverse geocoding returns results from any [data source](data-sources.md) available to Pelias. To filter results by source, specify one or more valid source names in a comma-delimited list using the `sources` parameter. For example, the following request returns only results from OpenStreetMap:

| source | name | short name |
|---|---|---|
| [OpenStreetMap](http://www.openstreetmap.org/) | `openstreetmap` | `osm` |
| [OpenAddresses](http://openaddresses.io/) | `openaddresses` | `oa` |
| [Who's on First](https://whosonfirst.org) | `whosonfirst` | `wof` |
| [GeoNames](http://www.geonames.org/) | `geonames` | `gn` |

>/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&___sources=osm___

### Filter by layers (data type)

Without specifying further, reverse geocoding doesn't restrict results to a particular type (street, venue, neighbourhood, and so on).  If your application is only concerned with, say, which city a latitude, longitude is closest to, then use the `layers` parameter.  For example, the following request returns only results that are localities (cities and towns):

> /v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&___layers=locality___

Here are all the supported layers and their meanings.

|layer|description|
|----|----|
|`venue`|points of interest, businesses, things with walls|
|`address`|places with a street address|
|`street`|streets,roads,highways|
|`country`|places that issue passports, nations, nation-states|
|`macroregion`|a related group of regions. Mostly in Europe|
|`region`|states and provinces|
|`macrocounty`|a related group of counties. Mostly in Europe.|
|`county`|official governmental area; usually bigger than a locality, almost always smaller than a region|
|`locality`|towns, hamlets, cities|
|`localadmin`|local administrative boundaries|
|`borough`| a local administrative boundary, currently only used for New York City|
|`neighbourhood`|social communities, neighbourhoods|
|`coarse`|alias for simultaneously using all administrative layers (everything except `venue` and `address`)|

### Filter by country

If you are performing a reverse geocode near a country boundary, and are only interested in results from one country and not the other, you can specify a country code. You can set the `boundary.country` parameter value to the alpha-2 or alpha-3 [ISO-3166 country code](https://en.wikipedia.org/wiki/ISO_3166-1). 

> /v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&___boundary.country=JAM___


## Distance and confidence scores for the results

Each result returned has a distance from the query point (in kilometers) and an associated confidence score. Confidence scores are calculated based on the distance from the result to the supplied `point.lat` and `point.lon`. Confidence scoring for reverse geocode results is likely to change with different data sources and layers.

Distance from `point.lat`/`point.lon` | Confidence score
--- | ---
&lt; 1m | 1.0
&lt; 10m | 0.9
&lt; 100m | 0.8
&lt; 250m | 0.7
&lt; 1km | 0.6
&gt;= 1km | 0.5

## Example requests

This section shows how the various parameters can be combined to form complex use cases.

### Only OpenStreetMap results near the specific point
[/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&sources=osm](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&sources=osm)

### Only street addresses near the specific point
[/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address)

### Only OpenStreetMap street addresses near the specific point
[/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address&sources=osm](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address&sources=osm)

### Only the first OpenStreetMap address near the specific point
[/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address&sources=osm&size=1](https://api-pelias.piensa.co/v1/reverse?point.lat=18.0149849645&point.lon=-76.752299925&layers=address&sources=osm&size=1)
