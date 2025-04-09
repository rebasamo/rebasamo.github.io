---
layout: single
title:  "World Data Map Generation"
date:   2025-04-02 00:00:00 +0100
categories: Template
author: Xavier Rebasa Moll
excerpt: "Project Template where the map is generated using real world data"
tags:
  - Programming
  - UE5
  - Ongoing
toc: true
toc_label: In This Page
toc_sticky: true

header:
  #image: /assets/PostsAssets/WorldMap/GeoProjectMallorca.jpg
  teaser: /assets/PostsAssets/WorldMap/GeoProjectMallorca.jpg

sidebar:
  - title: "Role"
    text: "Programmer"
  - title: "Language"
    text: "C++"
  - title: "Engine"
    text: "UE5"
  - title: "Platforms"
    text: "PC"
  - title: "Date"
    text: "2025-..."
---
<br>

# Context

This project goal is to create a plugin/template that will allow an easy integration of real world data on any project. Just enabling the plugin, and making sure the data servers are up, it will allow to render a map in an Unreal Engine scene and some basic props, like building or roads.

## Apis Used
- UE5:
  - Georeferencing
  - WebBrowser UI
  - HTTP
  - Procedural Mesh Component
  - JSON/XML

- Overpass API
  - [Custom Server](https://github.com/wiktorn/Overpass-API)
- Elevation Racemap
  - [Custom Server](https://github.com/racemap/elevation-service)

## Main Features
- Elevation Data
  - Elevation of the terrain giving a set of coordinates
- Overpass Data
  - Giving a set of coordinates any information can be requested inside the shape provided:
    - Buildings Layout
    - Streets, Highways, mountain trails, Etc ...
    - PoI (Points of Interest)
      - Works: Supermarket, Pharmacy, 
      - Turism: Monuments, Museums
      - Others
    - Nature:
      - Woods
      - Meadows
      - Beach
      - Rivers
    
# Project

## Setup

In order to be able to request all the info 2 servers need to be setup. One contains the elevation information, the other all the map data.

### Docker

Both servers can be setup using Docker containers. This setup is pretty easy and both can be setup using either the docker desktop or the CLI version.

#### Overpass

The overpass server can be setup with a single line of code

{% highlight powershell %}

docker run -e OVERPASS_META=yes -e OVERPASS_MODE=clone -e OVERPASS_DIFF=https://planet.openstreetmap.org/pbf/planet-latest.osm.pbf -v osm-data:/db -p 12346:80 -i -t --name overpass_world wiktorn/overpass-api

{% endhighlight %}

This command will setup the Overpass server, download the map data and start the container.
To change the options of the server, like checking for diff time, timeouts, mode, etc, check the [Wiki](https://github.com/wiktorn/Overpass-API) for the different config options.

If at any time the container stops you can start it again using 

{% highlight powershell %}
docker start [container name]
{% endhighlight %}

Where the container name is set using --name during the first command, **overpass_world** in the example above.

One error I encountered is that sometimes the permissions are not given to the /db/ folder, causing access errors.
To solve this issue run this commend to open a shell for the specific container

{% highlight powershell %}
docker exec -it [container-name] sh
{% endhighlight %}

and follow it by calling this command to give the necessary permissions to the db folder

{% highlight powershell %}
chmod og+rx /db
{% endhighlight %}

##### Request Format

To request information to the Overpass API server you can follow all the tutorials found in its [wiki](https://wiki.openstreetmap.org/wiki/Overpass_API/Overpass_API_by_Example) and can be easily tested using [Overpass Turbo](https://overpass-turbo.eu/) where you can setup your server and check different premade querys. It also contains an assistant which allows you to request specific querys and export the query format for later use.


#### Elevation

The elevation server can also be settup using a single command line but it need to have the data downloaded beforehand

To download the elevation data use:

{% highlight powershell %}
aws s3 cp --no-sign-request --recursive s3://elevation-tiles-prod/skadi [/path/to/data/folder]
{% endhighlight %}

The AWS services will need to be installed in order for it to work. Also, remember where the data is being stored as it will need to be specified on the following commands

After running this Docker command the server will be usable:

{% highlight powershell %}
docker run --rm -v [/path/to/data/folder]:/app/data -p3000:3000 racemap/elevation-service
{% endhighlight %}

One recommendation is to increase the maximum data to be sent if you intent to request large groups of coordinates, it can be achieved by adding a ENV variable value to the existing command
{% highlight powershell %}
docker run -e MAX_POST_SIZE=100Mb --rm -v /path/to/data/folder:/app/data -p3000:3000 racemap/elevation-service
{% endhighlight %}

In this case is set to 100Mb but it can be set to any value fitted to the needs of the app

##### Request Format

To request elevations there are 2 methods.

- 1 elevation
{% highlight powershell %} http://localhost:3000/?lat=51.3&lng=13.4 {% endhighlight %}
- Multiple elevatio request
{% highlight powershell %} # > [[lat, lng], ...] curl -d '[[51.3, 13.4], [51.4, 13.3]]' -XPOST -H 'Content-Type: application/json' http://localhost:3000 # < [ele, ...] {% endhighlight %}


The returned information will be 1 single value for the single elevation request and a set of elements for the multi coordinate request [val1, val2, val2, ...]

## Structure

### HTTP Request API

Using the HTTP library on Unreal we request 2 sets of info from the different servers


#### Elevation Request
{% highlight c++ %}

	TSharedRef<IHttpRequest, ESPMode::ThreadSafe> HttpRequest = FHttpModule::Get().CreateRequest();
	HttpRequest->SetURL("http://your-ip:3000");
	HttpRequest->SetVerb("POST");
	HttpRequest->SetHeader(TEXT("Content-Type"), TEXT("application/json"));

  FString JSONString = [[latitude, longitude], [set], [of], [coordinates]]

	HttpRequest->SetContentAsString(JSONString);
	HttpRequest->ProcessRequest();
{% endhighlight %}

#### Elevation Process

The return value on the HttpResponse is an array of values. Using the Unreal JSON parser is as easy as going through all the returned values.

{% highlight c++ %}

TArray<double> Elevations;

TArray<TSharedPtr<FJsonValue>> OutArray;
		auto Reader = TJsonReaderFactory<>::Create(HttpResponse->GetContentAsString());
		if (FJsonSerializer::Deserialize(Reader, OutArray))
		{
			for (TSharedPtr<FJsonValue>& JsonValue : OutArray)
			{
				double Value ;
				if (JsonValue->TryGetNumber(Value))
				{
					Elevations.Add(Value*100.0f);
				}
				else
				{
					UE_LOG(LogTemp, Warning, TEXT("Error Getting Double Value: %s"), *JsonValue->AsString());
				}
			}
		}
{% endhighlight %}

With the elevation values then is easy to generate a grid for a terrain mesh.


#### Map Data Request

This request is a little bit more complicated due to the Overpass API allowing to have filtered calls.

This request could either be a single "GetAll" call and then process the returned information or a more centered query for getting specific items, like getting only the streets or only buildings.

- Get All:

{% highlight text %}
[out:xml];
(
  way({{bbox}});
  node({{bbox}});
  <;
);
(._; >;);
out meta;
{% endhighlight %}

![image-center](/assets\PostsAssets\WorldMap\MallorcaAllNodes.png){: .align-right}
Query taken with Pollença inside the box

This query will request all the nodes and ways of the contained box (swap **box** for your set of coordinades) the **(._; >;);** part allows the query to also retrieve nodes from the outside of the box, meaning that if a street has a segment inside the box and some part outside, you will be able to retrieve all of those nodes.

- Get Filtered

{% highlight text %}

[out:json];

(
  // get cycle route relations
  relation[route=bicycle]({{bbox}});
  // get cycleways
  way[highway=cycleway]({{bbox}});
  way[highway=path][bicycle=designated]({{bbox}});
);

out body;
>;
out skel qt;
{% endhighlight %}

![image-center](/assets\PostsAssets\WorldMap\MallorcaCycleLanes.png){: .align-right}

This second query will only return nodes related to bicycle routes and ways to tagged as paths or cycleways highways


#### Map Data Process Response

To process the data returned by the query you will need to read the response depending on how it has been requested ([out:json]; or [out:xml];). This will define wich library to use.

In terms of the content, it can be separated in 3 different types

##### Nodes
Those are the base elements of the map. A node is a point on the map and those could be part of ways, relations or standalone nodes

For example, this is a standalone node from a supermarked. It contains a lot of different information related to what the node is.

{% highlight xml %}
 <node id="5030223984" lat="39.8763760" lon="3.0196732" version="3" timestamp="2022-11-27T02:05:41Z" changeset="129420345" uid="11725140" user="jmaspons">
    <tag k="brand" v="Eroski"/>
    <tag k="brand:wikidata" v="Q1361349"/>
    <tag k="brand:wikipedia" v="en:Eroski"/>
    <tag k="name" v="Eroski"/>
    <tag k="name:ca" v="Eroski"/>
    <tag k="opening_hours" v="Mo-Sa 09:00-22:00"/>
    <tag k="shop" v="supermarket"/>
  </node>
{% endhighlight %}

This second one is part of a way and only has the node information, no tags. The way from which is part of is explained in the next section

{% highlight xml %}
  <node id="5030223985" lat="39.8779584" lon="3.0204865" version="1" timestamp="2017-08-12T11:10:40Z" changeset="51055352" uid="1867863" user="Horst Hugo"/>
{% endhighlight %}

##### Ways
Ways are a set of nodes that form different structures, in this case it is the top-down view of a building. For buildings, the first and last nodes are repeated to form a closed area.

{% highlight xml %}
  <way id="514986549" version="2" timestamp="2020-07-06T07:51:29Z" changeset="87585085" uid="1948725" user="pedrojuan01">
    <nd ref="5030223996"/>
    <nd ref="5030223993"/>
    <nd ref="5030223988"/>
    <nd ref="5030223987"/>
    <nd ref="5030223985"/>
    <nd ref="5030223986"/>
    <nd ref="5030223989"/>
    <nd ref="5030223990"/>
    <nd ref="5030223991"/>
    <nd ref="5030223992"/>
    <nd ref="5030223994"/>
    <nd ref="5030223995"/>
    <nd ref="7686908527"/>
    <nd ref="5030223996"/>
    <tag k="building" v="yes"/>
  </way>
{% endhighlight %}

Other ways could be streets or highways. Those usually contain information on what type it is, the nodes that form the street, way of the traffic, type of asphalt, etc.

{% highlight xml %}
 <way id="4270702" version="25" timestamp="2016-06-24T11:05:23Z" changeset="40257151" uid="602634" user="matx17">
    <nd ref="100481968"/>
    <nd ref="4262396163"/>
    <nd ref="648984103"/>
    <tag k="highway" v="tertiary"/>
    <tag k="oneway" v="yes"/>
    <tag k="ref" v="Ma-2201"/>
  </way>
{% endhighlight %}

##### Relations

Relations are a set of ways and nodes that have something in common. This example has the border for the town of Pollença. With the node being the town info(shown below) and the ways being different delimitations, for example Coastlines or boundaries

{% highlight xml %}
  <relation id="347644" version="20" timestamp="2024-09-03T15:55:36Z" changeset="156148917" uid="11725140" user="jmaspons">
    <member type="node" ref="30417672" role="admin_centre"/>
    <member type="way" ref="54679963" role="outer"/>
    <member type="way" ref="54680269" role="outer"/>
    <member type="way" ref="54661091" role="outer"/>
    <member type="way" ref="54660967" role="outer"/>
    <member type="way" ref="54657106" role="outer"/>
    <member type="way" ref="54657579" role="outer"/>
    <member type="way" ref="54725354" role="outer"/>
    <member type="way" ref="32707836" role="outer"/>
    <member type="way" ref="54725461" role="outer"/>
    <member type="way" ref="54725608" role="outer"/>
    <member type="way" ref="32707838" role="outer"/>
    <member type="way" ref="162867281" role="outer"/>
    <member type="way" ref="45354764" role="outer"/>
    <member type="way" ref="45382538" role="outer"/>
    <member type="way" ref="45382367" role="outer"/>
    <member type="way" ref="53663142" role="outer"/>
    <member type="way" ref="54725357" role="outer"/>
    <member type="way" ref="54660969" role="outer"/>
    <tag k="admin_level" v="8"/>
    <tag k="border_type" v="municipi"/>
    <tag k="boundary" v="administrative"/>
    <tag k="idee:name" v="Pollença"/>
    <tag k="ine:municipio" v="07042"/>
    <tag k="name" v="Pollença"/>
    <tag k="name:ca" v="Pollença"/>
    <tag k="name:es" v="Pollensa"/>
    <tag k="population" v="17279"/>
    <tag k="population:date" v="2023"/>
    <tag k="ref:ine" v="07042000000"/>
    <tag k="source" v="BDLL25, EGRN, Instituto Geográfico Nacional"/>
    <tag k="type" v="boundary"/>
    <tag k="wikidata" v="Q601112"/>
    <tag k="wikipedia" v="ca:Pollença"/>
  </relation>
{% endhighlight %}

Node from the relation
{% highlight xml %}
  <node id="30417672" lat="39.8792073" lon="3.0157098" version="25" timestamp="2024-04-29T21:56:27Z" changeset="150682867" uid="3392826" user="Hugoren Martinako">
    <tag k="capital" v="8"/>
    <tag k="ele" v="47"/>
    <tag k="name" v="Pollença"/>
    <tag k="name:ca" v="Pollença"/>
    <tag k="name:es" v="Pollensa"/>
    <tag k="place" v="town"/>
    <tag k="population" v="7480"/>
    <tag k="population:date" v="2023"/>
    <tag k="ref:ine" v="07042000401"/>
    <tag k="source" v="Instituto Geográfico Nacional"/>
    <tag k="source:date" v="2011-06"/>
    <tag k="source:ele" v="MDT5"/>
    <tag k="source:name" v="Nomenclátor Geográfico de Municipios y Entidades de Población"/>
    <tag k="wikidata" v="Q601112"/>
    <tag k="wikipedia" v="ca:Pollença"/>
  </node>
{% endhighlight %}


### Data Process

This data can be requested per chunks and easily configured.

#### Terrain Generation

For the terrain we only use the elevation data and it is represented in the map using UProceduralMeshComponent. Using chunk systmes to either update a section of an existing mesh or to create a section with new data we can achieve a sort of "Level of Detail" implementation. 

Let's say that the config is set to use chunks of 1Km squared each. In the config we can implement limitations on how may vertices to request per square. So for chunks near your location we can request 512 vertices per chunk and reduce this value for further chunks.
UProceduralMeshComponent allows us to update existing sections, so once you move closer to an active chunk, the elevation information can be either requested again with more detail or stored for later use if you get closer again.

This example is using 128 vertices squared per chunk displaying completely Mallorca. (No LoD)

![image-center](/assets\PostsAssets\WorldMap\GeoProjectMallorca.jpg){: .align-right}

#### Component Generation - Next Step

The next step will be generate the map elements using the OSM data.

- Vegetation on woods, fields, mountains, etc
- Generate buildings
  - Investigate procedural building generation
  - Have specific building types for Points of Interest, like supermarket or pharmacies
  - 
- Build a street / highway system
  - Geometry and connection with existing streets
  - Have a pathfinding system using nodes
    - This system could allow to implement easily commerce or pathfinding between towns and cities
  - Investigate material to have have road lines


