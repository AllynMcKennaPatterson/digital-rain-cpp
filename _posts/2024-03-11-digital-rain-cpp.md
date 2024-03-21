---
layout: post
title: A Digital Rain Project in Modern C++
tags: cpp coding project
categories: demo
---
<div align="center">
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/Blog_Header.png" width="800" height="200">
</div>

## Introduction

Digital Rain is a visual effect that mimics a stream of falling raindrops on a computer screen. The effect was first popularised by its use in "The Matrix" and has since been adopted in various contexts. It was originally depicted as layers of green Unicode characters with a white head that fade as they fell down the screen.

In this blog I will explore my approach to this challenge, my implementation using features of modern C++, and the problems I encountered along the way.


## Design & Test

### Project Structure
My project features three classes.

<div align="center">
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/UML Diagram.png" width="500" height="200">
</div>

- The DigitalRain class is responsible for creating RainDrop objects, adding them to a vector, and calling the print method on each RainDrop. 

- A RainDrop object holds the data for each individual drop and will print them one character at a time when the DigitalRain class instructs it to do so.

- A StartMenu object is created when the project starts. I designed this class to add some customisation to my project. The user can select the set of characters and colour they wish to be printed.

### Design Challenges

#### Character Animation
Console output is typically written horizontally, whereas Digital Rain requires the output to be written vertically. The easiest option here was to use the WinAPI's ```SetConsoleCursorPosition``` method which allows me to set the X and Y coordinate of the cursor and print a character anywhere in the console window. In this case creating the falling animation is as simple as incrementing the Y coordinate each time a character is printed. 

#### Generating Random Characters
Digital Rain with random characters is much more interesting than a single character. I have used C++'s ```default_random_engine``` from the ```<random>``` library. ```default_random_engine``` is a modern C++ feature and is generally considered better than C's ```rand``` function due to its higher quality random number generation.

#### Performance Optimisation
The efficiency of the algorithm will determine how smooth the Digital Rain appears. My printing algorithm may need to loop through, and print 400-1800 characters per "frame". Any small delay while printing each character could result in a noticable performance drop. This was the biggest challenge I encountered and I will expand on the performance issues later in the Performance section.

## Algorithm

### My Approach
I considered a number of approaches when I began planning this project. I could choose to use mulithreading, asynchronous tasks, or even treat the console window as a large 2D array. Instead I chose to focus on the core logic of the simulation without adding overhead such as thread coordination and synchronisation.

I have split my algorithm into two sections. My object management occurs in the outer loop and is responsible for managing my ```RainDrop``` objects. My printing algorithm is called on each ```RainDrop``` object at the end of the Object Management loop.

### Object Management
###### Flow Chart
<div align="center">
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/Object_Management.png" width="500" height="600">
</div>

Each ```RainDrop``` object is intialised with a random ```length``` and ```X``` coordinate. 

I have designed my algorithm such that only three hundred raindrop object will exist at one time, this provides good character density while only increasing my process memory usage by 100KB.

###### Visual Studio Diagnostic Tools
<div align="center">
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/Performance_Stats.png" width="437" height="201">
</div>

### Printing Algorithm
###### Flow Chart
<div align="center">
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/Print_Flowchart.png" width="500" height="800">
</div>

My printing algorithm loops through and prints each character in a ```RainDrop```'s ```chars``` vector before incrementing the ```Y``` coordinate. I made the decision to add characters to the drop each loop to create a smooth falling effect as the drop enters the screen. When the drop reaches the bottom of the screen I begin to pop characters from the vector so it can exit the screen. 

I have excluded a set of ```if/else``` statements that determine the character's colour for simplicity.

## Performance

Performance was one of my main concerns while working on this project. My goal was to find a good balance between an efficient algorithm and impressive visuals. In my earlier versions of the project I only created a maximum of 150 ```RainDrop``` objects, which resulted in a smooth output but a scattered display. I then doubled the maximum ```RainDrop``` count without any noticable performance drops. It was only when I introduced colours that I noticed a significant reduction in framerate, which lead me to investigate why.

### Performance Testing

I needed a way to measure the performance so I implemented a frames-per-second (FPS) monitor by measuring the time between each console window update (period). The frequency of console window updates could then be calculated as 1/period. Setting the ```Sleep``` period to 16ms between prints would result in approximately 60Hz if we assume the character are instantly displayed, however this is far from the case.

#### Test Case 1

#### Test Case 2

#### Test Case 3

### Conclusion: Cout is slow
Now that I have tested a version with multiple colours, with a single colour, and with no colour, I suspect that my performance issues lie with ```cout```. Since there can be approximately 150 raindrops on the screen at one time, each with 4-12 characters, cout can be called 600-1800 times per frame. I wrote a small program to measure the time taken to execute ```cout``` with a single character. 

##### Results
<div align="center">
Execution time of a single character without colour
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/cout_no_colour.png">
</div>

<div align="center">
Shortest recorded execution time of a single character with colour
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/cout_colour_short.png">
</div>

<div align="center">
Longest recorded execution time of a single character with colour
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/cout_long.png">
</div>

After seeing these results I am confident that ```cout``` is the root of my performance issues. The high-level abstraction that ```cout``` provides creates additional overhead in the form of function calls and parameter passing compared to writing directly to the buffer using Windows API function. It also seems that printing the character wrapped in an ANSI colour code significantly increases the execution time.

The variable length in execution is likely due to the internal synchronisation mechanisms that make ```cout``` thread safe.

## Problem Solving
I did not encounter many problems while working on this project. Instead I will talk about how I would fix the existing problems if I could start again.

I would not change my approach. I still believe that threading is unnecessary and would increase the Process Memory of the program for marginal performance gains. I don't believe threading would fix my performance issues either since rendering the rain is not a computationally intensive task and I would still need to print each character using ```cout```. Instead I would utilise the Windows API library, specifically the ```WriteConsoleOutput``` function to write directly to the console buffer. This would remove the abstracted function calls and synchronisation that occurs when ```cout``` is executed.


## Modern C++
