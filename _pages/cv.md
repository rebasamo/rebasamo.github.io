---
layout: single
#title:  "cv"
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
- High Level
  - [C / C++](http://www.cplusplus.com/)
  - [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/)
  - [GLSL](https://www.khronos.org/opengl/wiki/Core_Language_(GLSL))
- Medium Level
  - [ARM-ASM](#!)
  - [Python](https://www.python.org/)
  - [Swift](https://developer.apple.com/swift/)
  - [Lua](https://www.lua.org/)
  - [HLSL](https://docs.microsoft.com/en-us/windows/desktop/direct3dhlsl/dx-graphics-hlsl)
- Low Level
  - [Java](https://www.java.com/en/)
  - [JavaScript](https://www.javascript.com/)

## APIs Used

- [OpenGL](https://www.opengl.org/) +
	- [GLFW](https://www.glfw.org/)
	- [SFML](https://www.sfml-dev.org)
- [DirectX 11](#!) (Framework)
- [ImGui](https://github.com/ocornut/imgui)
- [GLM](https://glm.g-truc.net/0.9.9/index.html)

## Engines
- [Unreal Engine 4](https://www.unrealengine.com/en-US/what-is-unreal-engine-4)
- [Unity](https://unity3d.com/)

## Software
- [Visual Studio](https://visualstudio.microsoft.com/)
- [Android Studio](#!)
- [XCode](#!)
- [GENie](https://github.com/bkaradzic/GENie)

## Source Controll
- Git
  - Console
  - SourceTree
  - GitHub Desktop
- Preforce(P4V)

# Education

## [ESAT](https://www.esat.es/) (Escuela Superior de Arte y Tecnología)
<a href="https://www.google.com/maps/place/ESAT+-+Escuela+Superior+de+Arte+y+Tecnolog%C3%ADa/@39.4778271,-0.3754507,17z/data=!3m1!4b1!4m5!3m4!1s0xd6048ad1c6e6aef:0x3f3bd8ce9722b1f3!8m2!3d39.477823!4d-0.373262"><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Valencia, Spain</a>
- [BTEc Level 5 HND in Computing And Systems Develpment](https://www.esat.es/estudios/carreras/carrera-programacion-videojuegos/)
- Graduated with **Merit** 
- 2016-2018

## [SHU](https://www.shu.ac.uk/) (Sheffiel Hallam University)
<a href="https://www.google.com/maps/place/Sheffield+Hallam+University/@53.3782423,-1.4680749,17z/data=!3m1!4b1!4m5!3m4!1s0x487982831b2243e9:0x37add1086f57be4f!8m2!3d53.3782391!4d-1.4658862" ><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Sheffield, UK</a>
- [BSc(honours) Computer Science for Games](https://www.shu.ac.uk/courses/computing/bsc-honours-computer-science-for-games/full-time/2019)
- Studying (2018-2019)

## References
- [Gustavo Aranda](mailto:garanda@esat.es)
  - Programme Leader at ESAT

- [Juan Diego Alegre](mailto:jd@esat.es)
  - Project Manager at ESAT

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
