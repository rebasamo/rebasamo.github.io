---
layout: single
date:   2019-02-08 23:26:41 +0100
title: ""
categories: about
permalink: /aboutme/
author: Xavier Rebasa Moll

toc: true
toc_label: In This Page
toc_sticky: true

---

# About Me
<!--<font style="opacity: 0.7" size= "3">-->

I am a C++ programmer with six years of experience in the video game industry, having worked at two companies on multiple projects. As a generalist, I have a broad skill set that allows me to work across different areas of game development, from gameplay systems to engine-level programming and performance optimization. This versatility enables me to adapt quickly to new challenges, collaborate effectively with different teams, and contribute to various aspects of a project. I am passionate about writing high-quality, maintainable code and continuously expanding my knowledge to create efficient and scalable solutions.

I am particularly interested in unusual game techniques, exploring innovative ways to push technical and creative boundaries. On my last project on MPG I had the opportunity to develop a gameplay mechanic that allowed dynamic tinting of any object in a scene, bringing a unique visual and interactive layer to gameplay. Currently, I am developing a template that leverages real-world data—such as elevation maps and city layouts—to enable the creation of games based on real geographic locations, opening new possibilities for immersive world-building and procedural content generation.

<!--</font>-->
<br>

# Education

## [ESAT](https://www.esat.es/) (Escuela Superior de Arte y Tecnología)
<a href="https://www.google.com/maps/place/ESAT+-+Escuela+Superior+de+Arte+y+Tecnolog%C3%ADa/@39.4778271,-0.3754507,17z/data=!3m1!4b1!4m5!3m4!1s0xd6048ad1c6e6aef:0x3f3bd8ce9722b1f3!8m2!3d39.477823!4d-0.373262"><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Valencia, Spain</a>
- [BTEc Level 5 HND in Computing And Systems Develpment](https://www.esat.es/estudios/carreras/carrera-programacion-videojuegos/)
- Graduated with **Merit** 
- 2016-2018

## [SHU](https://www.shu.ac.uk/) (Sheffiel Hallam University)
<a href="https://www.google.com/maps/place/Sheffield+Hallam+University/@53.3782423,-1.4680749,17z/data=!3m1!4b1!4m5!3m4!1s0x487982831b2243e9:0x37add1086f57be4f!8m2!3d53.3782391!4d-1.4658862" ><i class= "fas fa-fw fa-map-marker-alt" aria-hidden="true"></i> Sheffield, UK</a>
- [BSc(honours) Computer Science for Games](https://www.shu.ac.uk/courses/computing/bsc-honours-computer-science-for-games/full-time/2019)
- Graduated 2019
- **Bachelor of Science** with **First Class Honours**


# Experience
## Programming

### Projects

  {% for post in site.portfolio %}
  - [{{post.title}}]({{post.url}})
  {% endfor %}

### Ongoing Projects

{% assign tagged_posts = site.portfolio | where: "tags", "Ongoing" %}
{% for post in tagged_posts %}
- [{{ post.title }}]({{ post.url }})
{% endfor %}

## Others

### Previous Works

  {% for work in site.works %}
  - [{{work.title}}]({{work.url}})
  {% endfor %}

# Interests

## Sports

- Judo
- Tennis
- Paddle Tennis

## Favourite Games

- Elite Dangerous
- Terraria
- Minecraft
- Monster Hunter

- Dead Cells
- Enter the Gungeon
- The Binding of Isaac

- Borderlands
- Magicka

- Crypt of the NecroDancer
- Rift of the NecroDancer
- Beat Saber
- To The Moon / Finding Paradise

- Hollow Knight
- Ori and the Blind Forest