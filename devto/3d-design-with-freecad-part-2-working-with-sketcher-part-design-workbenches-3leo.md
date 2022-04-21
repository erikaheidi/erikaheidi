---
title: How to Create a Pen Holder on FreeCAD Using the Sketcher + Part Design Workbenches
published: true
description: In the second part of our introduction to 3D design with FreeCAD, learn how to create a pen holder step by step on FreeCAD.
tags: tutorial, beginners, 3d, design
series: Introduction to 3D Design with FreeCAD
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sfxzmg3nutic14wfw987.png
canonical_url: https://eheidi.dev/blog/3d-design-with-freecad-part-2-working-with-sketcher-part-design-workbenches-3leo
---

Sketcher and Part Design are two [FreeCAD workbenches](https://wiki.freecadweb.org/Workbenches) that work closely together, typically used to turn 2D geometries into 3D models. In a nutshell, through the Sketcher workbench you create the blueprints of parts that are manipulated through the Part Design workbench.

In this guide, which is part 2 of a [series about FreeCAD for beginners](https://dev.to/erikaheidi/an-introduction-to-3d-design-with-freecad-part-1-navigation-3gjo), we'll see how to use the Sketcher and Part Design workbenches to create a minimalist pen holder model that you can print with a 3D printer.

Before continuing with this post, it is important that you have FreeCAD installed on your machine, and that you are familiar with how to navigate the interface. You can refer to [the first post of this series](https://dev.to/erikaheidi/an-introduction-to-3d-design-with-freecad-part-1-navigation-3gjo) for more details on how to navigate FreeCAD's user interface.

## Step 1: Create a new project, then move to the Part Design workbench

Create a new project, then select the "Part Design" workbench on the workbench selection box.

![Part Design Workbench - FreeCAD for beginners](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/03etxhvkffjvo54ixsnc.gif)

## Step 2: Create a new body, and then create a new Sketch. Choose XY as base plane.

You can use the shortcuts on the left sidebar to create a new body, and then create a new sketch on that body. When creating the sketch, select the XY_Plane.

![Creating a new Body on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2lm08h4rsfa20kl7y44r.gif)

## Step 3: Select the square/rectangle tool, then create a rectangle at the center of the canvas.

Don't worry about the size now, we'll set up constraints in the next step. 

![Creating a rectangle shape on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wsqj7jsm82hxj3iei8m4.gif)

## Step 4: Select the width constraint, then set the size of **60mm**. Select the height constraint, then set the height of **40mm**.

Constraints allow you to specify a set of rules that can be applied to points, lines, and angles. For this example, we'll use the width and height constraints to limit the size of the rectangle that serves as base for our model.

## Step 5: Select the pad tool and pad to the height of 100mm.

Close the sketch. This will bring you back to the Part Design workbench, automatically. You can then select the pad tool, setting the type to Dimension and the height to 100mm. Click  "ok" to confirm.

![Padding the Sketch on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4v0k2d44mcror4b85kr1.gif)

## Step 6: Create a datum plane using the top face of the pad as reference.

Use the "top view" button to make sure you're looking to the object from the top. Select the top face of the pad, then select the "Datum Plane" tool to create a new datum plane using that face as reference. This will serve as base for a new sketch.

![Creating a Datum Plane on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lbhqchkdqklm4y3391ba.gif)

## Step 7: Create a new sketch based on that datum plane. 

Create a new sketch and make sure to select the new DatumPlane as reference.

![Creating a new sketch on top of a Datum Plane](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/49d20b39nabxh296ibp1.gif)

## Step 8: Hide the datum plane by selecting it and hitting the space bar on your keyboard. 

Create a rectangle form slightly smaller than the first one, using the constraints of 50mm and 30mm for width and height, respectively.

![Creating a new rectangle](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tfkkb8s3h7r7c4i5hpgz.gif)

## Step 9: Use the pocket tool to hollow the model.

Close the sketch, and you will be brought back to the Part Design workbench. Then, use the pocket tool instead of pad - this time we want to create a pocket to make the model hollow. Select the "Dimension" type and the length of 80mm.

![Creating a pocket in the model](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0lrtn9ldzuodrq3z3iov.gif)

## Step 10: Use the fillet tool to round the edges slightly.

Select one of the faces of the model, then select the "Fillet" tool. Click on the "add ref" button to add all external faces, one by one. Rotate the model and add the other faces. You can use the radios of 1mm for a discrete effect. When you're done, click the "OK" button.

![Using the fillet tool to round edges on FreeCAD](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zctca7gpiqlku5lmmi84.gif)

And it is done! If you want to print this model, you can export it through the menu `File -> Export`, then choose the **STL** format. You can then import the STL to a slicer software to get it ready for printing.

This model is functional as is, but we might want to add some details to the sides to make it more attractive. In the next part of this series, we'll add a special touch to the walls of the model using SVG files. Stay tuned!