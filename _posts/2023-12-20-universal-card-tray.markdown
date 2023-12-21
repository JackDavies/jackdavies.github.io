---
layout: post
title:  "Universal card tray"
date:   2023-12-20 10:25:33 +0100
category: Projects
tags: FreeCAD 3d-printng things-to-make
--- 

Tutorial for Universal card tray project that you can find on my [thingiverse page](https://www.thingiverse.com/thing:6390807)

### Prerequisites
---
This project has been created in FreeCAD 0.21.1, and should be compatible with all version grater than 0.21.0
FreeCAD is an open source 3D parametric modeler, you can download it from [freecad.org](https://www.freecad.org/)

### How to configure
---

The FreeCAD project contains a spreadsheet that can be used to modify the tray model, you can find the spreadsheet in the project tree
[![startup](/assets/img/uni-tray-project/startup.png)](/assets/img/uni-tray-project/startup.png)

Double clicking on the spreadsheet will open it in a new page:
[![Project Spreadsheet](/assets/img/uni-tray-project/spreadsheet.png)](/assets/img/uni-tray-project/spreadsheet.png)

The spreadsheet contains 7 variables that can be adjusted to modify the model:
1. `CardHeight`: height of the card that the tray is going to hold (make 2-3mm larger than the cards height to avoid a tight fit)
2. `CardWidth`: width of the card that the tray is going to hold (make 2-3mm larger than the cards width to avoid tight fit)
3. `WallThickness`: Thickness of the wall that surrounds the cards
4. `WallHeight`: height of the wall that surrounds the cards (note base is 3mm)
5. `Radius`: radius size on the outside edges of the tray, if you don't want rounded edges set this to a small number e.g. 0.00001mm (Don't set to 0mm this will cause FreeCAD to fail to compute the model)
5. `Fillet`: fillet size on the inside edges of the tray, if you don't want rounded edges set this to a small number e.g. 0.00001mm (Don't set to 0mm this will cause FreeCAD to fail to compute the model)
6. `TrayCount`: Number of trays that will be on the model

[![Project Drawing](/assets/img/uni-tray-project/drawing.png)](/assets/img/uni-tray-project/drawing.png)

`TrayCount` can be used to create a model with n number of trays in it (image with `TrayCount = 4` 

[![Tray Count 4](/assets/img/uni-tray-project/tray-count.png)](/assets/img/uni-tray-project/tray-count.png)

### Exporting for 3D printing
To export the tray model for 3D printing, select `Body` from the project tree, if the Body is not selected then FreeCAD wont export mesh information.
[![Select Body](/assets/img/uni-tray-project/select-body.png)](/assets/img/uni-tray-project/select-body.png)

Click File Export 

[![File Export menu](/assets/img/uni-tray-project/file-export.png)](/assets/img/uni-tray-project/file-export.png)

Set the file type as `STL Mesh (*.stl *.ast)` in the export file window 
[![File Export dialog](/assets/img/uni-tray-project/file-save.png)](/assets/img/uni-tray-project/file-save.png)

### Issues and limitations
---
Sometimes FreeCAD fails to recompute the model if `CardWidth` and `CardHeight` values are increased or decreased by large values e.g. 40mm to 100mm in one change. To avoid this try changing the values in small steps (10mm at a time) this helps FreeCAD to recompute the model correctly.

[![Compute Fail](/assets/img/uni-tray-project/compute-fail.png)](/assets/img/uni-tray-project/compute-fail.png)

FreeCAD will fail to compute the model if Radius or Fillet values are 0mm. If you want to remove the Inner and outer fillets, set their values to a small number (e.g. 0.0001mm)


