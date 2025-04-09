---
title: "The Multiplayer Group"
permalink: /MPG/
excerpt: "Multiple Projects"
tags:
  - Programming
toc: true
toc_label: In This Page
toc_sticky: true
header:
  teaser: /assets/WorkAssets/MPG/MPG.webp
sidebar:  
  
  - title: "Roles"
    text: "Software Engineer"
  - title: Location
  - title: MPG
    url: https://www.themultiplayergroup.com/
    icon: "fas fa-fw fa-handshake"


---


|-------|--------|
| Role |Software Engineer |
| Levels | Mid Programmer|
| Language | C++ |
| Engine | Unreal Engine |
| Versions | 4.x,  4.x (InHouse modified), 5.x |

# Context

Refer to [How We Work?](https://www.themultiplayergroup.com/how-we-work). MPG builds teams with workers from different specialities depending on the needs of the costumers. 

# Project A

| Role |Software Engineer |
| Language | C++ |
| Engine | Unreal Engine |
| Version | 4.x (InHouse modified)|

Our team was assigned to this project for 6 months.
We were assigned mostly with tasks about researching optimizations of Unreal Engine systems or on debugging issues related to the build/shipping process. 
I was assigned to work on improving the profiling process of the shader compiling process to get a information on each of the shader compiled.

- APIs used:
  - [MiniTrace](https://github.com/hrydgard/minitrace)
  - [Unreal STAT profiler](https://dev.epicgames.com/documentation/en-us/unreal-engine/profiler-tool-reference?application_version=4.27)

# Project B (The Bench)

| Role |Software Engineer |
| Language | C++ |
| Engine | Unreal Engine |
| Version | 4.x |

Internal MPG Project where peope are assigned while waiting to be added to another customer team.
This project had a world exploration using real location and gps tracking. The user could use different types of *Point of Interest* for different purposes, like a pharmacy for healing or a supermarked for restocking items.
I was part of the team in charge of the map display and the implementation of the systems and APIs used. In this case GPS and Map related.

- APIs used:
  - Docker for the [OSM](https://overpass-turbo.eu/) server
  - [Georeferencing](https://dev.epicgames.com/documentation/en-us/unreal-engine/georeferencing-a-level-in-unreal-engine) UE4 Plugin
  - Modified [OSM UE4 Plugin](https://github.com/ue4plugins/StreetMap) to be albe to generate geometry on runtime instead of on editor

# Project C [MSquared](https://msquared.io/){:target="_blank"}

| Role |Software Engineer |
| Language | C++ |
| Engine | Unreal Engine |
| Version | 4.x |

- Gadgets:
  - Worked on setting up some functionality shared between gadgets, like:
    - Selecting a set of players for muting, teleport them, etc.
    - Selecting a world location
  - Worked on the gadget themselfs to make sure those worked as intended.
- MML Object:
  - Worked on making sure the objects setup on the MML site worked as intended on the Unreal Engine scene.
    - Some properties needed to be implemented on Unreal to be sure the object behaves exactly on both sides.

# Project D (The Bench)

| Role |Software Engineer |
| Language | C++ |
| Engine | Unreal Engine |
| Version | 5.x |

When moved back to the bench after finishin working on the MSquared project I got assigned to a project on a very early stage of development. Thanks to that I got the opportunity to start working on one of the main systems this game would use.

This project needed a system to add some sort of Tint on top of different surfaces and work as a mask that would be drawn on top of the existing textures of the objects

## Tint system
This system allowed to tint any object in the scene, storing this information to be able to clean or spread this tint as needed.
It made use of UV information of the objects and using a centralized compute shader we used the information provided from the scene to modify this tint.

The system was managed from a single subsystem where all the objects affected were registered and managed.

### Tintable objects

Each object that could be tinted would get an actor component that stored each static mesh of the parent object and registered those on the subsystem. The subsystem would use a material to **unfold** the mesh and use a scene capture to store the unfolded view. This will simulate a "UVs" texture. The material would also assign to each pixel the local position of the mesh as a color to be checked against other objects later. This process only happens once per mesh type and only the transform of the mesh instances is used to calculate the world location per object.

### Tint objects

Each object that has the ability to add or remove tint is also stored on the main subsystem. Because the game had multiplayer the way the objects could tint the other objects on the scene was very limited. It was simplified to very simple shapes, like segments or points and radius. This allowed for other parts of the game like particle systems to be easily integrated in this system.

### Tint Processing

#### Tick
During the tick, all the tint patterns were stored on different buffers, sent over a network call and processed. To ensure that all the information was the same for all the clients this system used "Reliable" networking calls. The FrameID was also sent with the tint data to ensure any random call on the clients use the same seed. 

Once all the buffers are generated a compute shader is called for each object that can be tinted.

#### Compute Shader

A single Compute Shader was implemented that managed all the Tint related calculations. It consisted on different steps

- Caclulate current pixel world location using the pixel local position and the current object transform
- Calculate New amount of tint in current location
  - Calculate the amount of tint that would be added in 1 frame to this specific location using all the objects that added tint
  - Calculate the amout of tint removed from this specific location using the objects that remove tint
  - Both calculations involved point to segment and point to point distance calculations
- Calculate tint spreading
  - The tint would pool over time and be able to spread over nearby pixels. It used the world location of those to calculate how vertical the surface is and use it as a guidance to where to spread.

The ammount added or removed was modified by different noise textures using the FrameID as a seed for a pseudo random result. This allowed the stains to not resemble the source object that leave those, meaning that the segments or spheres will have irregular edges, resembling a more realistic stain. Using the same FrameID on all the clients allowed the final result to be exactly the same on all the scenes.

All the information of the tint is stored on a texture where each channel represents some characteristic of the Tint in the specific pixel.
- R channel: Ammount
- G channel: Debug ammount, representing the ammount added this frame by the different sources
- B channel: Debug ammount, representing the ammount removed this frame by the different removing sources
- A channel: Visibility. It coppied the Alpha channel of the Unfolded Material result in order to know which pixels could be tinted
  - This channel could be modified at any time to be able to set zones where the Tint could not happen, like under righs, behind objects on a wall, etc

#### Tint Visualization

In order to make the tint visible this project used a custom material function that was called at the end of the processing of any material of the objects that used the tint system. This function used a lerp with the R channel as alpha value to draw a different texture/color on top of the existing object where the ammount surpassed a threshold value.