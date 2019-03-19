---
layout: single
title:  "DX11 Animation System"
date:   2019-02-08 23:26:41 +0100
categories: Project
author: Xavier Rebasa Moll

toc: true
toc_label: In This Page
toc_sticky: true

header:
  image: /assets/PostsAssets/Animations/AllRobots2.png
  teaser: /assets/PostsAssets/Animations/Robot.png

sidebar:
  - title: "Role"
    text: "Programmer"
  - title: "Language"
    text: "C++"
  - title: "Platforms"
    text: "PC"
  - title: "Framework"
    text: "DX11 - University Framework"
  - title: "Date"
    text: "Course 2018-2019"
  - title: "Mark"
    text: "82/100"
---
<br>

# Context
<video width="100%" controls muted autoplay loop>
    <source src="/assets/PostsAssets/Animations/DieVid.mp4" type="video/mp4">
</video>
Given a framework, we needed to create a system to manage object and component hierarchy as well to handle animation that could be rendered using a given hierarchy.
## Tasks

- Implement a plane without animation but with a movable rotor and turret.
- The plane needs to be able to shot from the turret and the bullet will have inertia.
- Implement a robot with 3 different animations (Attack, Idle, Death)
  - Animations need to blend between them.
  - Death animation will trigger when a robot is shot.
  - Attack animation will trigger if the plane flies nearly.
  - Idle will trigger after a few seconds of death or if the plane flies out of range.



# Project

## First Aproach

This project required a number of classes that will handle all the behavior of the project. The first integrated was all the code needed for that.

## Classes Implemented

### Mesh System
- MeshBase

Container class which contains the information of the loaded mesh. Will be unique for each different mesh.

- MeshSystem

Contains the array of the different MeshBases. Is the class called to request for a mesh and returns an existent MeshBase or generates a new one with the new mesh to load. That evades duplicated information.

- Boundings

A bounding box is generated with the information of each MeshBase.

### Animations

<video width="100%" muted autoplay loop>
    <source src="/assets/PostsAssets/Animations/AnimationVid.mp4" type="video/mp4">
</video>

- AnimationTransform

Contains the information about the hierarchy of an object for each keyframe. One of this class is generated for each bone. Has a getter function that returns the new hierarchy transform for a given time. The transform will be a merge of the 2 keyframes that the given time is between.

{% highlight c++ %}
class AnimationTransform {
public:

	// Pair <KeyFrameTime, Value/s>
	std::vector<std::pair<float, float>>RotationX;
	std::vector<std::pair<float, float>>RotationY;
	std::vector<std::pair<float, float>>RotationZ;
	std::vector<std::pair<float, float[3]>>Translation;
	std::vector<std::pair<float, float[3]>>Scale;

	
	Transform GetLerped(float time); 

private:
	// Internal functions
	std::vector<XMVECTOR> GetCurrentQuatRotation(float time);
	std::vector<float> GetCurrentRotation(float time);
	std::vector<float> GetCurrentTranslation(float time);
	std::vector<float> GetCurrentScale(float time);

};
{% endhighlight %}


- AnimationBase

Container class which has all the information of an animation. Saves an AnimationTransform for each bone. Will be unique for each loaded animation.

- AnimationSystem

Contains the array of loaded AnimBases. Class called to request an animation. Will return the existent AnimationBase or generates a new one with the new animation to load. That evades duplicated information.

- AnimationObject

Object created for each instance of an AnimationBase being reproduced. Contains a pointer to the animation being played and information about how to reproduce it, such as being reproduced forward or backward and how to be reproduced (Once, Loop or PingPong).

{% highlight c++ %}
class AnimationObject
{
public:
  AnimationObject();
  ~AnimationObject();

  bool Load(const char* animationName);
  Transform GetBoneTransform(const char* boneName);

  void Play();
  void Stop();
  void Pause();
  void Resume();
  void Update(float DeltaTime);

  AnimationBase* animation_ = nullptr;

  AnimType type_ = AT_Once;
  float animTime_=0.0f;
  bool isPlaying_=false;
  bool forward_=true;
private:

};

{% endhighlight %}

- AnimationBlender

That is the actual class used for all the objects that use animations. Contains at the most 2 AnimationObject playing at the same time and is used to switch from one to another. That blends the animation being reproduced given a time to do it. Each AnimationObject used is stored to evade the creation of a new one if needed.


{% highlight c++ %}
class AnimationBlender
{
public:
    AnimationBlender();
    ~AnimationBlender();

    void Update(float DeltaTime);

    Transform GetBoneTransform(const char* boneName);

    //Forces the playing animation to a given one
    bool SetAnimation(std::string animation);

    //Animation to blend to and the blend duration
    bool BlendTo(std::string animation, float time = 1.0f);

    //Forces the blending to a given percentage
    void SetBlendValue(float blendPercentage);

    AnimationObject* animationPlaying = nullptr;
    AnimationObject* animationBlending = nullptr;

    float blendTime_ = 0.0f;
    float blendDuration_ = 1.0f;

    //Blend Percentage (time/duration)
    float blendValue = 0.0f;

    bool blending_ = false;

private:
    std::map<std::string,AnimationObject*> animations_;
    AnimationObject* GetAnimation(std::string name);
};
{% endhighlight %}


### Objects

<video width="100%" muted autoplay loop>
    <source src="/assets/PostsAssets/Animations/DebugVid.mp4" type="video/mp4">
</video>

A base class which from all the Object type classes will inherit. Contains the basic information as well as the main functionality for a 3D object. 

- Transform
- Component Vector
	- Root Component
- Draw Mode (For Debug purposes)
	- Normal
	- Bounding
	- Wireframe

An Object can have a vector of components which will be updated with the object itself. Those components must inherit from the base class Component.
A Templated class is provided to ensure that.

{% highlight c++ %}
   template <typename T>
    T* AddComponent() {
        static_assert(std::is_base_of<Component, T>::value, "T must inherit from Component");
        T* newComponent = new T();
        if (newComponent) {
            components_.push_back(newComponent);
            ((Component*)newComponent)->objectOwner_ = this;
            return newComponent;
        }
        return nullptr;
    }
{% endhighlight %}

_std::vector::push_back() would create an assert if T incompatible, but a custom assert is more helpfull_

### Component

A base component class containing most of the basic information and functionality. Different component classes will inherit from that base.

- Parent Component
- Vector of child Components
- Pointer to the owner Object
- Local Transform

#### MeshComponent

An inherited class that contains 2 extra variables.
- MeshBase
- BoundingBox

Only the draw function has been overridden.

{% highlight c++ %}
class MeshComponent : public Component
{
public:
    MeshComponent();
    ~MeshComponent();

    bool SetMesh(const char* route);

    virtual void Draw() override;
    void DrawBounding();

    MeshBase* mesh_ = nullptr;
    BoundingBox bounding_;

};

{% endhighlight %}

#### SkeletalMeshComponent

Even if it is not a complete SkeletalMesh, that class contains an std::map pairing each bone name with an individual MeshComponent. The components will replicate the hierarchy of the animation requested for the AnimationBlender. It uses Root as the parent component.
Only the draw function has been overridden.

{% highlight c++ %}
 class SkeletalMeshComponent : public Component
{
public:
    SkeletalMeshComponent();
    ~SkeletalMeshComponent();

    bool AddBone(const char* BoneName, const char* Route, Transform& transform, const char* Parent = "");

    virtual void Draw() override;
    void DrawBoundings();
    //Bone-Mesh Structure
    std::map<std::string, MeshComponent*> skeleton_; 
    AnimationBlender* animBlender_=nullptr;

};
{% endhighlight %}