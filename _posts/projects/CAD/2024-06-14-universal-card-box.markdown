---
layout: post
title:  "Universal card box"
date:   2024-06-14 10:25:33 +0100
category: Projects
tags: FreeCAD 3d-printng things-to-make
--- 

Tutorial for Universal card box project that you can find on my [thingiverse page](https://www.thingiverse.com/thing:6669379)

### Prerequisites
---
This project was developed in FreeCAD 0.21.2 it should be compatible with version 0.21.0 and later. FreeCAD, an open-source 3D parametric modeler, can be downloaded from [freecad.org](https://www.freecad.org/)

### How to configure
---
The FreeCAD project contains 3 parts, a lid, box body with divider slots and a divider to separate cards.

You can modify the box dimensions via the spreadsheet stored in the `Data` folder within the project

[![startup](/assets/img/uni-card-box/startup.png)](/assets/img/uni-card-box/startup.png)

Double clicking on the spreadsheet will open it in a new page:
[![Project Spreadsheet](/assets/img/uni-card-box/spreadsheet.png)](/assets/img/uni-card-box/spreadsheet.png)

The spreadsheet contains 15 variables that can be adjusted to modify the model:
`
1. `Width` : Width of the box
2. `Length` : Length of the box
3. `Height` : Height of the box
4. `WallThickness` : Thickness of the box wall
5. `DividerStartDist` Distance of first slot from the front of the box
6. `DividerCount` Number of divider slots 
7. `DividerDist` Linear pattern distance of the divider slots
8. `DividerSlotDepth` : Depth of the divider slots
9. `DividerThickness` : Thickness of the divider
10. `DividerTol` : Divider tolerance, this value is subtracted/added to parts of the divider/divider slots to allow for printer inaccuracies overshoot etc. 
11. `DividerTopHeight` : Height of the top section of the divider
12. `DividerTabHeight` : Height of the tab on top of the divider
13. `DividerTabWidth` : Width of the tab on top of the divider
14. `DividerTabLeft` : Height of the tab on top of the divider
15. `LidDepth` : Depth of the lid over the box

**Warning:** Don't set any value to `0mm` as this will cause FreeCAD to fail to compute the objects

Values entered into the spreadsheet need to be in the following format: =*value*mm e.g. to set height of the box to `100mm` change `Height` to `=100mm`. Without the = or units FreeCAD will fail to compute the objects

##### Box Dimensions
[![Box Drawing](/assets/img/uni-card-box/box-drawing.png)](/assets/img/uni-card-box/box-drawing.png)

If you want to generate a box without divider slots, set `DividerSlotDepth` to `0.001mm`.

If you want to generate a box without a lid, set `LidDepth` to `0.001mm` (note the lid will still be generated in the project you can ignore it when exporting)

The dimensions of the lid will automatically update to fit over the box with a `2mm` tolerance

##### Divider Dimensions
[![Divider Drawing](/assets/img/uni-card-box/divider-drawing.png)](/assets/img/uni-card-box/divider-drawing.png)

Currently the project only generates one object for the divider.\
If you want multiple dividers with different tab positions, you'll need to generate and export a separate divider for each desired tab position

If you want to generate a divider without a tab set `DividerTabHeight` to `0.001mm`

### Exporting for 3D printing
3D model parts can be found in the `models` folder in the project. 
To export objects for printing, expand `models` folder and select the part you want to export 

[![Select Body](/assets/img/uni-card-box/select-box.png)](/assets/img/uni-card-box/select-box.png)

Click File Export 

[![File Export menu](/assets/img/uni-card-box/file-export.png)](/assets/img/uni-card-box/file-export.png)

Set the file type as `STL Mesh (*.stl *.ast)` in the export file window, and click save.
[![File Export dialog](/assets/img/uni-card-box/file-save.png)](/assets/img/uni-card-box/file-save.png)

### License
This work (Universal card box) is licensed under CC BY-SA 4.0

