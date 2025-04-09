---
layout: single
title:  ""
date:   2019-02-08 23:26:41 +0100
categories: cv
permalink: /cv/
author: Xavier Rebasa Moll

toc: true
toc_label: In This Page
toc_sticky: true

---

# Technical Skills

## Languages

| Language | Level|
|-------|--------|
| Catalan | Native |
| Spanish | Native |
| English | First Certificate Cambridge - B2 |
|  | IELTS 7.5 Band Score - CEFR: C1 |

## Programming Languages
- Mainly Used
  - [C / C++](http://www.cplusplus.com/){:target="_blank"}
- Commonly Used
  - [HLSL](https://docs.microsoft.com/en-us/windows/desktop/direct3dhlsl/dx-graphics-hlsl){:target="_blank"}
  - [GLSL](https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)){:target="_blank"}
  - [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/){:target="_blank"}
  - [Java](https://www.java.com/en/){:target="_blank"}
  - [Python](https://www.python.org/){:target="_blank"}
- Barely Used
  - [Swift](https://developer.apple.com/swift/){:target="_blank"}
  - [Lua](https://www.lua.org/){:target="_blank"}
  - [ARM-ASM](#!){:target="_blank"}
  - [JavaScript](https://www.javascript.com/){:target="_blank"}

## APIs Used

### Graphics
- [OpenGL](https://www.opengl.org/){:target="_blank"} +
	- [GLFW](https://www.glfw.org/){:target="_blank"}
	- [SFML](https://www.sfml-dev.org){:target="_blank"}
- [DirectX 11](#!) (Framework)

### Other APIs
- [ImGui](https://github.com/ocornut/imgui){:target="_blank"}
- [GLM](https://glm.g-truc.net/0.9.9/index.html){:target="_blank"}
- [Intel TBB](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onetbb.html){:target="_blank"}

## Engines
- [Unreal Engine 4/5.x](https://www.unrealengine.com/en-US/unreal-engine-5){:target="_blank"}
  - Gameplay
  - Multiplayer
  - Compute Shader
  - Plugins
- [Unity](https://unity3d.com/){:target="_blank"}

## Software

### IDEs
- [Rider](https://www.jetbrains.com/rider/){:target="_blank"} (Main IDE used)
- [Visual Studio](https://visualstudio.microsoft.com/){:target="_blank"}
- [Android Studio](#!)
- [XCode](#!)

### Project building tools
- [GENie](https://github.com/bkaradzic/GENie){:target="_blank"}
- [Sharpmake](){:target="_blank"}

## Source Control
- Git
  - Console
  - SourceTree
  - GitHub Desktop
- Preforce(P4V)
  - (Unreal Game Sync) UGS

# Education

## [ESAT](https://www.esat.es/){:target="_blank"} (Escuela Superior de Arte y Tecnología)
<a href="https://www.google.com/maps/place/ESAT+-+Escuela+Superior+de+Arte+y+Tecnolog%C3%ADa/@39.4778271,-0.3754507,17z/data=!3m1!4b1!4m5!3m4!1s0xd6048ad1c6e6aef:0x3f3bd8ce9722b1f3!8m2!3d39.477823!4d-0.373262"><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Valencia, Spain</a>
- [BTEc Level 5 HND in Computing And Systems Develpment](https://www.esat.es/estudios/carreras/carrera-programacion-videojuegos/)
- Graduated with **Merit** 
- 2016-2018

## [SHU](https://www.shu.ac.uk/){:target="_blank"} (Sheffiel Hallam University)
<a href="https://www.google.com/maps/place/Sheffield+Hallam+University/@53.3782423,-1.4680749,17z/data=!3m1!4b1!4m5!3m4!1s0x487982831b2243e9:0x37add1086f57be4f!8m2!3d53.3782391!4d-1.4658862" ><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Sheffield, UK</a>
- [BSc(honours) Computer Science for Games](https://www.shu.ac.uk/courses/computing/bsc-honours-computer-science-for-games/full-time/2019)
- Graduated 2019
- **Bachelor of Science** with **First Class Honours**

## References
<!-- - [Gustavo Aranda](mailto:garanda@esat.es)
  - Programme Leader at ESAT

- [Juan Diego Alegre](mailto:jd@esat.es)
  - Project Manager at ESAT
-->

- [Chris Jackson](mailto:chris@jackson.me.uk)
   {% assign mpg_post = site.works | where: "title", "The Multiplayer Group" | first %}
    {% if mpg_post %}
  - Technical Director at [MPG]({{ mpg_post.url }})
    {% else %}
  - Technical Director at [MPG](https://www.themultiplayergroup.com/)
  {% endif %}

# Experience
## Programming

### Projects

  {% for post in site.portfolio %}
  - [{{post.title}}]({{post.url}})
  {% endfor %}

## Others

### Previous Works
  {% for work in site.works %}
  - [{{work.title}}]({{work.url}})
  {% endfor %}
