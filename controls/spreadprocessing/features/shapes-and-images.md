---
title: Shapes and Images
page_title: Shapes and Images | UI for ASP.NET AJAX Documentation
description: Shapes and Images
slug: spreadprocessing/features/shapes-and-images
tags: shapes,and,images
published: True
position: 11
---

# Shapes and Images



This article briefly describes what are shapes and images, and how to create and work with them.It contains the following sections:

* [What Are Shapes and Images?](#what-are-shapes-and-images?)

* [Properties of Shapes and Images](#properties-of-shapes-and-images)

* [Creating and Inserting an Image](#creating-and-inserting-an-image)

* [Deleting a Shape](#deleting-a-shape)

* [Changing the Shape's Position and Size](#changing-the-shape's-position-and-size)

* [Relationship Between the Cell Index of the Shape and Its Rotation Angle](#relationship-between-the-cell-index-of-the-shape-and-its-rotation-angle)

## What Are Shapes and Images?

The shapes are objects which represent a visual illustration which can be inserted in a worksheet. In the document model they are represented by the abstract class __FloatingShapeBase__.

The image is a kind of shape which is characterized by having an image source. They are represented by the __FloatingImage__ class, which inherits FloatingShapeBase.

## Properties of Shapes and Images

Shapes have the following properties:

* __CellIndex:__The cell index where the top left corner of the shape is located when the shape is not rotated.

* __OffsetX:__The offset between the left side of the shape and the left side of the cell index.

* __OffsetY:__The offset between the top of the shape and the top of the cell index.

* __Width:__The width of the shape.

* __Height:__The height of the shape.

* __RotationAngle:__The angle (in degrees) by which the shape is rotated about its center.

* __IsHorizontallyFlipped:__Indicates whether the shape has been flipped across the y-axis.

* __IsVerticallyFlipped:__Indicates whether the shape has been flipped across the x-axis.

* __Name:__The name of the shape.

* __LockAspectRatio__: Determines whether the aspect ratio between the width and the height of the image will be preserved.

* __Id:__A unique number assigned to the image after it has been added to a worksheet.

* __Worksheet:__The worksheet in which the shape is or will be inserted.

Images have one additional property:

* __ImageSource:__Represents the source of the image.

## Creating and Inserting an Image

To insert an image into a worksheet do the following:

1. Create a __FloatingImage__ instance as in __Example 1__.

1. Configure its properties as in __Example 2__.

1. Insert the image into the worksheet as shown in __Example 3__.

In order to create an instance of __FloatingImage__ you need the worksheet in which you want to insert the image, the cell index and the offset.

````c#
	            Worksheet worksheet = this.radSpreadsheet.ActiveWorksheet;
	            FloatingImage image = new FloatingImage(worksheet, new CellIndex(7, 1), 35, 10);
	
````



The next step is to configure the other properties of the image as needed.

````c#
	            FileStream stream = File.Open(filePath, FileMode.Open);
	
	            using (stream)
	            {
	                image.ImageSource = new Telerik.Windows.Documents.Media.ImageSource(stream, "png");
	            }
	
	            image.Width = 330;
	            image.Height = 45;
	            image.RotationAngle = 20;
	
	
````



Insert the image into the collection of shapes of the worksheet.Note that the worksheet needs to be the same as the one passed in the FloatingImage constructor, otherwise an exception is thrown.

````c#
	            worksheet.Shapes.Add(image);
````



## Deleting a Shape

In order to delete a shape from a worksheet, you need the shape's instance. The collection of shapes of the worksheet exposes a __Remove()__ method with two overloads which you can use.

__Example 4__ demonstrates how you can remove the image added in __Example 3__.

````c#
	            worksheet.Shapes.Remove(image);
````



## Changing the Shape's Position and Size

After the initial values of the properties of the shapes have been assigned, they can always be changed in order to reposition, resize and rotate the shape. You can change the following characteristics of the shapes:

1. Repositioning the shape

````c#
	            image.CellIndex = new CellIndex(4, 5);
	            image.OffsetX = 10;
	            image.OffsetY = 10;
````



1. Changing the shape's width and height

````c#
	            image.Width -= 50;
	            image.Height += 50;
````

The Width and Height properties do not take the LockAspectRatio property into account. If you would like more control on whether the aspect ratio of the shape should be observed, you can also use the following methods.

* void SetWidth(bool respectLockAspectRatio, double width, bool adjustCellIndex = false)

* void SetHeight(bool respectLockAspectRatio, double height, bool adjustCellIndex = false)

````c#
	            image.SetWidth(false, 330);
	            image.SetHeight(true, 45);
````

These two methods will be further explained in the next section.

1. Rotating the shape

````c#
	            image.RotationAngle = 40;
````

The rotation angle of the shape can affect the __CellIndexM__ property and the offset. The relationship between these properties is described in more detail in the next section of this article.

1. Flipping the shape

````c#
	            image.IsHorizontallyFlipped = true;
````



## Relationship Between the Cell Index of the Shape and Its Rotation Angle

The __CellIndex__ of the shape and the cell index where the top left corner of the shape is visually located do not necessarily coincide when there is rotation applied. Consider the following image which has CellIndex B8.![Rad Spread Processing Features Shapes And Images 01](images/RadSpreadProcessing_Features_Shapes_And_Images_01.png)

If we increase the rotation angle of the image, it will be visualized differently.![Rad Spread Processing Features Shapes And Images 02](images/RadSpreadProcessing_Features_Shapes_And_Images_02.png)

It appears that the top left cell index is B5, however, the CellIndex property of the image will remain unchanged, B8, as will the offset.

This set up is convenient as it allows for more intuitive rotation of the shapes. However, when the rotation angle increases substantially, the underlying CellIndex of the shape might become too distant to be useful. In order to avoid this, once the rotation angle becomes 45° and more, the CellIndex should switch to where the top left corner would be at 90° rotation.

This is illustrated in the following images:![Rad Spread Processing Features Shapes And Images 03](images/RadSpreadProcessing_Features_Shapes_And_Images_03.png)

At this point, the CellIndex property of the shape is D1 and the offset should also be recalculated accordingly.![Rad Spread Processing Features Shapes And Images 04](images/RadSpreadProcessing_Features_Shapes_And_Images_04.png)

As rotation increases, the CellIndex of the shape will switch between B8 and D1, depending on what is closer to the visual top left corner of the shape. The result will be the following:

* 0° - 45° (excluded): __B8__

* 45° (included) - 135° (excluded): __D1__

* 135° (included) - 225° (excluded): __B8__

* 225° (included) - 315° (excluded): __D1__

* 315° (included) - 360°: __B8__

Another occasion when adjustments to the top left cell index and offset of a shape might be necessary is when the size of a rotated image is changed. Changing the top left position of the image might be necessary if it is desired that the visual top left corner of the shape remains unmoved.

Additionally, if the size and the rotation angle of the image will result in a top left position outside of the worksheet, the position needs to be automatically adjusted to fit inside it.

In order to provide more flexibility, the model gives the option to have these changes of the top left position of the shape automatically performed or not. The properties __RotationAngle__, __Width__ and __Height__ do not make any adjustments to the position of the shape. If you would like to enable the adjustments, you can use the following methods:

* void SetWidth(bool respectLockAspectRatio, double width, bool adjustCellIndex = false)

* void SetHeight(bool respectLockAspectRatio, double height, bool adjustCellIndex = false)

* void SetRotationAngle(double rotationAngle, bool adjustCellIndex = false)