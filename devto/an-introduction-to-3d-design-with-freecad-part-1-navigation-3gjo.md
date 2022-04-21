---
title: An Introduction to 3D Design with FreeCAD: Navigation
published: true
description: In the first part of this introduction to 3D design with FreeCAD series, you'll get familiar with FreeCAD's interface and learn how to navigate, create models, change perspective, and save STL files.
tags: tutorial, beginners, 3d, design
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/84f2mexytm3wbixg9644.png
series: Introduction to 3D Design with FreeCAD
canonical_url: https://eheidi.dev/blog/an-introduction-to-3d-design-with-freecad-part-1-navigation-3gjo
---

As the name suggests, [FreeCad](https://www.freecadweb.org/) is a free CAD software, a parametric modeler that can be used to design 3D objects for printing. It is available for Linux, Mac, and Windows users.

In this post, which is part of an introductory series about FreeCad, we'll see the basics of the software, some important concepts, and how to navigate the user interface.

If you want to follow along, now is a good time to [get FreeCad installed](https://www.freecadweb.org/downloads.php) on your computer before moving ahead.

FreeCad has a very "raw" interface in my opinion, it can be hard to figure out things on your own as it happens with other software you might be used to work with.

In any case, once you get familiar with the general concepts of how to work with the interface and manipulate planes and perspectives, you'll be in a much better position to explore the available workbenches and experiment with different methods of designing objects.

## Creating a New Model

To experiment with the interface, you can create a new model in File -> New, then open the Part Design workbench and click on the Cube icon to create a new solid cube.

![Creating a new model on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wgzsiab0ijsy2b9r2i4y.gif)

Then, make sure you're using the Touchpad navigation style, at the bottom right of the screen:

![Changing navigation style on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g5xc91rzvorqon37q1en.gif)

## Navigating XYZ

When designing 3D models, one of the most important things to get used to at first is the Z axis, and how to navigate different perspectives. 

Most of us are familiar with X and Y, but when designing 3D objects you'll work with a third axis: Z. You'll typically work from a top view of the object you're designing; Z in this case is the axis that represents the depth of the object. In many cases, Z might simply represent the extrusion width. Wait - what is an extrusion? We'll get there in a moment.

![Navigating XYZ axis on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/18wheg9ubrdv2kohyyva.gif)

On FreeCad, you can see the axis position on the bottom right of the screen, and the top right shows a cube with a more user-friendly view of the same information.

Here are the two most important controls, using the Touchpad navigation style:

- `SHIFT` + Mouse move: Move the object on the screen without changing the perspective.
- `SHIFT` + `LEFT CLICK` + Mouse move. This will move the object freely across different axis. Like you're handling the object in front of you with one hand.

Additionally, the zoom controls are:

- Zoom In: `SHIFT` + `CTRL` + `+` key, or `SHIFT` + `CTRL` + Mouse move.
- Zoom Out: `CTRL` + `-` key, or `SHIFT` + `CTRL` + Mouse move.

If you "lose" the object, you may use the magnifying glass icon on the top left, and that will fit the whole content on the screen.

## Working with Perspectives

In most 3D design applications, you'll work with different perspectives that are exhibited in the screen as a 2D image, representing different possible combinations of X, Y, and Z. Depending on the software you use, there might be different shortcuts for accessing default perspectives. Freecad has some convenient perspective buttons you can use to see an object from all sides:

![Changing Perspective of objects on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kz4tstp38gvxikrs0lpj.gif)

## Navigating through Workbenches

Workbenches provide different tools that are grouped to target different design workflows. Some workflows may require the use of multiple workbenches. As an example, the Sketcher workbench is typically used with the Part Design workbench to create parts based on a 2D sketch.

To navigate to a different workbench, you may use the workbench select menu on the top of the screen. You'll see that the menu tools will change depending on the workbench selected:

![Navigating through Workbenches on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7ufqicc6uk89w5ufwtfl.gif)

## Saving an STL file

To save a model as an STL file that you can slice and print with a 3D printer, you first need to select the object you want to export, from the "Model" view on the left navigation sidebar, and then go to File -> Export menu. Make sure to select "STL Mesh" on the file type.

![Saving an STL file on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/soioip6m1301vnu7g96v.gif)

In the next part of this FreeCad series, I'll share a bit of my workflow with the Sketcher + Part Design workbenches, and we'll create an actual fun model to print.