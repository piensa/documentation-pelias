# Pelias: Finding places

Geocoding is the process of matching an address or other text to its corresponding geographic coordinates.

All Pelias requests share the same format:

https://api-pelias.piensa.co/

```
   https://api-pelias.piensa.co/v1/search?text=Kingston
   \___/   \__________________/\__/\_____/\_________/
     |            |             /     |        |
  scheme       domain        version  path     query
```

## Search the world

In the simplest search, you can provide only one parameter, the text you want to match in any part of the location details. To do this, build a query where the `text` parameter is set to the item you want to find.

For example, if you want to find a [YMCA](https://en.wikipedia.org/wiki/YMCA) facility, here's what you'd need to append to the base URL of the service.

> [/v1/search?__text=Montero%20Bay__](https://api-pelias.piensa.co/v1/search?text=Montero%20Bay)

Note the parameter values are set as follows:

| parameter | value |
| :--- | :--- |
| `text` | Montero Bay |

Clicking the link above will open a browse containing the best matching results for the text `Montero Bay`. You will notice the data is in a computer-friendly format called [GeoJSON](http://geojson.org/), which may be hard for humans to read in some browsers.

You can install a plug-in for your browser to display JSON in a more formatted manner. You can search the web store for your browser to find and install applicable products.

Spelling matters, but not capitalization when performing a query with Pelias. You can type `montero bay`, `MONTERO BAY`, or even `mONtero bAy`. See for yourself by comparing the results of the earlier search to the following:

> [/v1/search?__text=mONtero%20bAy__](https://api-pelias.piensa.co/v1/search?text=mONtero%20bAy)

Note that the results are spread out throughout the world because you have not given your current location or provided any other geographic context in which to search.

## Set the number of results returned

By default, Pelias returns up to 10 results. If you want a different number, set the `size` parameter to the desired number. This example shows returning only the first result.

| parameter | value |
| :--- | :--- |
| `text` | Montero Bay |
| `size` | 1 |

> [/v1/search?text=Montero%20Bay&__size=1__](https://api-pelias.piensa.co/v1/search?text=Montero%20Bay&size=1)

If you want 25 results, you can build the query where `size` is 25.

> [/v1/search?text=Montero%20Bay&__size=25__](https://api-pelias.piensa.co/v1/search?text=Montero%20Bay&size=25)
## Narrow your search

If you are looking for places in a particular region, or country, or only want to look in the immediate vicinity of a user with a known location, you can narrow your search to an area. There are different ways of including a region in your query. Pelias supports three types: country, rectangle, and circle.

### Search within a particular country

Sometimes your work might require that all the search results be from a particular country or a list of countries. To do this, you can set the `boundary.country` parameter value to a comma separated list of alpha-2 or alpha-3 [ISO-3166 country code](https://en.wikipedia.org/wiki/ISO_3166-1).

Now, you want to search for YMCA again, but this time only in Great Britain. To do this, you will need to know that the alpha-3 code for Great Britain is GBR and set the parameters like this:

> [/v1/search?text=Port&__boundary.country=JAM__](https://api-pelias.piensa.co/v1/search/?Fboundary.country=JAM&text=Port)


| parameter | value |
| :--- | :--- |
| `text` | Port |
| `boundary.country` | JAM |


### Search within a rectangular region


To specify the boundary using a rectangle, you need latitude, longitude coordinates for two diagonals of the bounding box (the minimum and the maximum latitude, longitude).

For example, to find a Park within the state of Texas, you can set the `boundary.rect.*` parameter to values representing the bounding box around Kingston: min_lon=-76.8377872909 min_lat=17.9514709186 max_lon=-76.7522999252 max_lat=18.0149849645

Tip: You can look up a bounding box for a known region with this [web tool](http://boundingbox.klokantech.com/).

-76.8377872909,17.9514709186,-76.7522999252,18.0149849645


> [/v1/search/__?boundary.rect.min_lat=17.9514709186&boundary.rect.min_lon=-76.8377872909&boundary.rect.max_lat=18.0149849645&boundary.rect.max_lon=-76.7522999252__&text=Park](https://api-pelias.piensa.co/v1/search/?boundary.rect.min_lat=17.9514709186&boundary.rect.min_lon=-76.8377872909&boundary.rect.max_lat=18.0149849645&boundary.rect.max_lon=-76.7522999252&text=Park)

| parameter | value |
| :--- | :--- |
| `text` | Park |
| `boundary.rect.min_lat` | 17.9514709186 |
| `boundary.rect.min_lon` | -76.8377872909 |
| `boundary.rect.max_lat` | 18.0149849645 |
| `boundary.rect.max_lon` | -76.7522999252|


### Search within a circular region


Sometimes you don't have a rectangle to work with, but rather you have a point on earth&mdash;for example, your location coordinates&mdash;and a maximum distance within which acceptable results can be located.

In this example, you want to find all YMCA locations within a 35-kilometer radius of a location in Ontario, Canada. This time, you can use the `boundary.circle.*` parameter group, where `boundary.circle.lat` and `boundary.circle.lon` is your location in Ontario and `boundary.circle.radius` is the acceptable distance from that location. Note that the `boundary.circle.radius` parameter is always specified in kilometers.

> [/v1/search?text=Park&__boundary.circle.lon=-76.7522999252&boundary.circle.lat=18.0149849645&boundary.circle.radius=35__](https://api-pelias.piensa.co/v1/search?text=Park&boundary.circle.lon=-76.7522999252&boundary.circle.lat=18.0149849645&boundary.circle.radius=35)

| parameter | value |
| :--- | :--- |
| `text` | Park |
| `boundary.circle.lat` | 18.0149849645 |
| `boundary.circle.lon` | -76.7522999252|
| `boundary.circle.radius` | 35 |


### Search within a parent administrative area

Pelias has a powerful understanding of relationships between places. In particular, it has a concept called the administrative hierarchy: each record in Pelias is listed as belonging to a parent neighbourhood, city, region, country, and other regions. This has many uses, including filtering. The Pelias global id (`gid`) of any record can be used with the `boundary.gid` filter to return only records with a given parent.

For example, finding Parks in [Kingston](https://en.wikipedia.org/wiki/Kingston,_Jamaica) with only a bounding box would be challenging: the bounding box would include much of nearby areas, possibly leading to incorrect results.

With `boundary.gid`, this query can return accurate results.

> [/v1/search?text=Park&__boundary.gid=whosonfirst:region:421186515__](https://api-pelias.piensa.co/v1/search/?boundary.gid=whosonfirst:region:421186515&text=Park)

In the query above, `whosonfirst:region:421186515`, is the Pelias `gid` for Oklahoma, USA. Currently, all parent records come from the [Who's on First](https://whosonfirst.org/) project. `gid`s for records can be found using either the [Who's on First Spelunker](http://spelunker.whosonfirst.org/), a tool for searching Who's on First data, or from the responses of other Pelias queries. 

### Specify multiple boundaries

If you're going to try using multiple boundary types in a single search request, be aware that the results will come from the intersection of all the boundaries. So, if you provide regions that don't overlap, you'll be looking at an empty set of results.

## Prioritize results by proximity

Many use cases call for the ability to promote nearby results to the top of the list, while still allowing important matches from farther away to be visible. Pelias allows you to prioritize results within geographic boundaries, including around a point, within a country, or within a region.

### Prioritize around a point

By specifying a `focus.point`, results will be sorted in part by their proximity to the given coordinate. All else being equal, results closest to the point will show up higher. 

> [/v1/search?text=Park&__focus.point.lat=18.0149849645&focus.point.lon=-76.7522999252__](https://api-pelias.piensa.co/v1/search/?focus.point.lat=18.0149849645&focus.point.lon=-76.7522999252&text=Park)

| parameter | value |
| :--- | :--- |
| `text` | Park |
| `focus.point.lat` | 18.0149849645 |
| `focus.point.lon` | -76.7522999252|

Looking at the results, you can see that the few locations closer to this location show up at the top of the list, sorted by distance. You also still get back a significant amount of remote locations, for a well balanced mix. Because you provided a focus point, Pelias can compute distance from that point for each resulting feature.

## Combine boundary search and prioritization

Now that you have seen how to use boundary and focus to narrow and sort your results, you can examine a few scenarios where they work well together.

### Prioritize within a country

You can combine that same focus point and the country boundary like this.

| parameter | value |
| :--- | :--- |
| `text` | Park |
| `focus.point.lat` | 18.0149849645 |
| `focus.point.lon` | -76.7522999252|
| `boundary.country` | JAM |

### Prioritize within a circular region

If you are looking for the nearest YMCA locations, and are willing to travel no farther than 50 kilometers from your current location, you likely would want the results to be sorted by distance from current location to make your selection process easier. You can get this behavior by using `focus.point` in combination with `boundary.circle.*`. You can use the `focus.point.*` values as the `boundary.circle.lat` and `boundary.circle.lon`, and add the required `boundary.circle.radius` value in kilometers.

> [/v1/search?text=Park&focus.point.lat=17.9514709186&focus.point.lon=-76.8377872909&__boundary.circle.lat=18.3149849645&boundary.circle.lon=18.3149849645&boundary.circle.radius=10__](https://api-pelias.piensa.co/v1/search?text=Park&focus.point.lat=17.9514709186&focus.point.lon=-76.8377872909&boundary.circle.lat=18.3149849645&boundary.circle.lon=-76.7522999252&boundary.circle.radius=10)

| parameter | value |
| :--- | :--- |
| `text` | Park |
| `focus.point.lat` | 17.9514709186|
| `focus.point.lon` |  -76.8377872909 |
| `boundary.circle.lat` | 18.3149849645 |
| `boundary.circle.lon` | -76.7522999252 |
| `boundary.circle.radius` | 25 |



## Filter your search

Pelias brings together data from multiple open sources and combines a variety of place types into a single database, allowing you options for selecting the dataset you want to search.

With Pelias, you can filter by:

* `sources`: the originating source of the data
* `layers`: the kind of place you want to find

### Filter by data source
The search examples so far have returned a mix of results from all the data sources available to Pelias. Here are the sources being searched:

| source | name | short name |
|---|---|---|
| [OpenStreetMap](http://www.openstreetmap.org/) | `openstreetmap` | `osm` |
| [OpenAddresses](http://openaddresses.io/) | `openaddresses` | `oa` |
| [Who's on First](https://whosonfirst.org) | `whosonfirst` | `wof` |
| [GeoNames](http://www.geonames.org/) | `geonames` | `gn` |

If you use the `sources` parameter, you can choose which of these data sources to include in your search. So if you're only interested in finding a Green in data from OpenAddresses, for example, you can build a query specifying that data source.

> [/v1/search?text=road&__sources=oa__](https://api-pelias.piensa.co/v1/search?sources=oa&text=Green)

| parameter | value |
| :--- | :--- |
| `text` | Green |
| `sources` | oa |


If you wanted to combine several data sources together, set `sources` to a comma separated list of desired source names. Note that the order of the comma separated values does not impact sorting order of the results; they are still sorted based on the linguistic match quality to `text` and distance from `focus`, if you specified one.

> [/v1/search?text=Green&__sources=osm,gn__](https://api-pelias.piensa.co/v1/search?sources=osm,gn&text=Green)

| parameter | value |
| :--- | :--- |
| `text` | Green |
| `sources` | osm,gn |

Each of the data sources supported by Pelias can have different properties, licenses, and strengths. You can learn more on the [data sources for Pelias](data-sources.md) page.

### Filter by data type
In Pelias, different types of results are given different `layer` values. This helps us differentiate, for example, an address from a point of interest from a country.

The Pelias layers are derived from the hierarchy created by the gazetteer [Who's on First](https://github.com/whosonfirst/whosonfirst-placetypes/blob/master/README.md) and can be used to help you filter for just the types of results you want.

Here's a list of the types of places you could find in the results, sorted by granularity:

|layer|description|
|----|----|
|`venue`|points of interest, businesses, things with walls|
|`address`|places with a street address|
|`street`|streets,roads,highways|
|`neighbourhood`|social communities, neighbourhoods|
|`borough`|a local administrative boundary, currently only used for New York City|
|`localadmin`|local administrative boundaries|
|`locality`|towns, hamlets, cities|
|`county`|official governmental area; usually bigger than a locality, almost always smaller than a region|
|`macrocounty`|a related group of counties. Mostly in Europe.|
|`region`|states and provinces|
|`macroregion`|a related group of regions. Mostly in Europe|
|`country`|places that issue passports, nations, nation-states|
|`coarse`|alias for simultaneously using all administrative layers (everything except `venue` and `address`)|
|`postalcode`|postal code used by mail services|

> [/v1/search?text=Green&__layers=venue,address__](https://api-pelias.piensa.co/v1/search?layers=venue,address&text=Green)

| parameter | value |
| :--- | :--- |
| `text` | Green |
| `layers` | venue,address |

## Available search parameters

| Parameter | Type | Required | Default | Example |
| --- | --- | --- | --- | --- |
| `text` | string | yes | none | `Union Square` |
| `focus.point.lat` | floating point number | no | none | `48.581755` |
| `focus.point.lon` | floating point number | no | none | `7.745843` |
| `boundary.rect.min_lon` | floating point number | no | none | `139.2794` |
| `boundary.rect.max_lon` | floating point number | no | none | `140.1471` |
| `boundary.rect.min_lat` | floating point number | no | none | `35.53308` |
| `boundary.rect.max_lat` | floating point number | no | none | `35.81346` |
| `boundary.circle.lat` | floating point number | no | none | `43.818156` |
| `boundary.circle.lon` | floating point number | no | none | `-79.186484` |
| `boundary.circle.radius` | floating point number | no | 50 | `35` |
| `boundary.gid` | Pelias `gid` | no | none | `whosonfirst:locality:101748355` |
| `sources` | string | no | all sources: osm,oa,gn,wof | openstreetmap,wof |
| `layers` | string | no | all layers: address,venue,neighbourhood,locality,borough,localadmin,county,macrocounty,region,macroregion,country,coarse,postalcode | address,venue |
| `boundary.country` | string | no | none | `GBR,FRA` |
| `size` | integer | no | 10 | 20 |
