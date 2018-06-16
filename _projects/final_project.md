---
layout: project
title: ROS (Robot Operating System) Course Project
date: June 15, 2018
image: https://github.com/KansoW/Whisker_Pulling_Machine/blob/master/Pics/IMG_20180614_215205.jpg
---

## Project description

# **Linear Motion Pulling Machine for Whicker Drawing Research**

#### Weilin Ma, _April ~ December 2018_
#### Master of Science in Robotics, Northwestern University
## **Overview:**
#### This is the personal project for my final quarter at the **MSR (Master of Science in Robotics) Program, Northwestern University**.
#### The goal for this project is to build and control a machine that can pull a plastic filament through a **heated oven** at a **constant speed**, so that the filament can linearly taper and form into a **whisker** that can be used for later related robotics research.
#### My plan for the first quarter (April ~ June 2018) is to build a simple prototype version frist. As I am building the prototype, I will be able to study various aspects of the design and try to make adjustments for later versions.

## **Design:**
### **Mechanical:**
#### The prototype is more focused on mechanical design because of the need to get it working early.
#### The mechanical system consists of a stepper motor with lead screw and traveling nuts, and an aluminum slide bar with rotary sliders. The two parts are joined together by a <a href="#Image1: slider mount with gripper">_slider mount with a whisker gripper on top_</a>.
#### <a name="Image1: slider mount with gripper"></a>
#### <img src="https://github.com/KansoW/Whisker_Pulling_Machine/blob/master/Pics/IMG_20180614_215216.jpg?raw=true" width="400" height="350" />

### **Electrical:**
#### The electrical system is fairly simple. It consists of an <a href="#Image2: Arduino and MP6500">_Arduino Uno_</a> and a [**MP6500 Stepper Motor Driver**](https://www.pololu.com/product/2968) from Pololu. The power supply for now is a 12V DC signal from a table top power supplier. 
#### <a name="Image2: Arduino and MP6500"></a>
#### <img src="https://github.com/KansoW/Whisker_Pulling_Machine/blob/master/Pics/IMG_20180614_215226.jpg?raw=true" width="400" height="350" />

## **Progress:**
#### As for right now (June 15, 2018), I have built the whole prototype and initial simple Arduino code to drive the pulling mechanism linearly. The stepper motor can drive the lead screw at a constant rotary speed, thus driving the traveling nuts at a relatively low constant linear speed. 
#### <img src="https://github.com/KansoW/Whisker_Pulling_Machine/blob/master/Pics/IMG_20180614_215205.jpg" width="400" height="350" />

## **Videos:**
#### Will update very soon...