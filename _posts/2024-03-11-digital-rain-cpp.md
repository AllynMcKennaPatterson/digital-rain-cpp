---
layout: post
title: A Digital Rain Project in Modern C++
tags: cpp coding project
categories: demo
---
<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/Header.png" width="800" height="200">

## Introduction

Digital Rain is a visual effect that mimics a stream of falling raindrops on a computer screen. The effect was first popularised by its use in "The Matrix" and has since been adopted in various contexts. It was originally depicted as layers of green Unicode characters with a white head that fade as they fell down the screen.

In this blog I will explore my approach to this challenge, my implementation using features of modern C++, and the problems I encountered along the way.


## Design & Test
My project features three classes.

<img src="https://raw.githubusercontent.com/allynmckennapatterson/digital-rain-cpp/main/docs/assets/images/UML Diagram.png" width="500" height="200">

The DigitalRain class is responsible for creating RainDrop objects, adding them to a vector, and calling the print method on each RainDrop. 

A RainDrop object holds the data for each individual drop and will print them one character at a time when the DigitalRain class instructs it to do so.

A StartMenu object is created when the project starts. I designed this class to add some customisation to my project. The user can select the set of characters and colour they wish to be printed.

### Character Animation
Console output is typically written horizontally, whereas Digital Rain requires the output to be written vertically. The easiest option here was to use the WinAPI's ```SetConsoleCursorPosition``` method which allows me to set the X and Y coordinate of the cursor and print a character anywhere in the console window. In this case creating the falling animation is as simple as incrementing the Y coordinate each time a character is printed. 

## Algorithm

## Problem Solving

## Modern C++
