# Applying Turbosmooth or OpenSubDiv on an already rigged mesh in 3DS Max

If you ever tried to export a model with something above the `Skin` modifier in the modifier stack, you'd end up with a following message:

```
The plug-in has detected modifiers listed above Skin (or Physique) in the Modifier list and must disable them. 
```


To fix this, you either have to move the modifier below Skin, and risk breaking things, or remove Skin, do your modifications and then redo the whole weight painting. Fortunately, 3DS Max has skin tools that allows us to transfer the skin data from the mesh.


Select the mesh, go to the Utilities menu (Wrench icon) and click on `More...`. From that list, select `SkinUtilities`, followed by `Extract Skin Data To Mesh`. Now remove the Skin modifier from our mesh and apply the Turbosmooth/OpenSubDiv modifier on both the mesh and the new `SkinData_xxxxxx` mesh with all of the options being the same.


Select the mesh again and create a new Skin modifier, adding all of the bones the model had previously and then select both the mesh and the SkinData mesh. Go back to the Utilities tab and click on `Import Skin Data From Mesh`. Now match all of the source bones to the target ones and press ok. You can also press `Match by name` and quickly see if you added extra bones into the skin modifier or you forgot to add any.


Press OK and you should have the mesh with turbosmooth/opensubdiv and its original skinning. You should be able to export it now.