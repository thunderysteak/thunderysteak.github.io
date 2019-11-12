---
layout: default
---

# WoW data extraction resource sheet

So you want to start making 3D WoW stuff, have the knowledge how to create good renders but you
want to create things with WoW models? Great, thats what we’re here for. This resource sheet will
list several applications you can use for model extraction from WoW files
Lets start with the basics first and then the tools

## Type of files World of Warcraft uses:

**CASC** is an archiving file format used in several of Blizzard Entertainment's games, created to replace the outdated format of MPQ  
**M2** files (also called MDX) contain model objects. This is used for characters, creatures and doodads.  
**.anim** files are low priority sequences (e.g. emotes) are in extra files to allow for lazy loading. Required for M2 decompiling.  
**WMO** files contain world map objects. They have a chunked structure with root WMO and group WMO file.  
**ADT** files contain terrain and object information for map tiles.  
**BLP** files store textures with precalculated mipmaps and contain alpha map with DXT or similar compression.

## The tools

### WoW Model Viewer
The most popular tool for model extraction. Comes bundled
with a listfile, allows you to import armory characters with
mogs, equip any item, preview all M2 files and preview
particles. Allows exporting in OBJ and FBX formats with 
all model animations. WMV itself is no longer build to
 properly export doodads and structures


Download: 
<https://wowmodelviewer.net/new/download/>  
JIRA Tracking:
<https://wowmodelviewer.net:8443/projects/WMV/summary>

### BLPNG Converter
Its a simple tool  for conversion of a single 
or a batch of BLP textures. It has a GUI and 
supports both way conversion.  

Download
<https://www.wowinterface.com/downloads/info22128-BLPNGConverter.html>

### CASCView

CASCView is a tool for viewing and extracting data files
from WoW and other Blizzard games. You’ll require a
CSV based listfile and Bnet launcher to be closed to use it

Download:
<http://www.zezula.net/en/casc/main.html>  
Listfile: <https://wow.tools/casc/listfile/download/csv>

### M2Mod Redux
M2Mod Redux is a tool created primarily for creating model
edits for WoW, but it can also be used for extracting a 
character file with all of its geosets as a different separate
mesh, being more suitable for edits or "universal modular model" due of no requirements of having to reskin the whole mesh and can toggle on and off specific geosets like hair


Download:
 <https://bitbucket.org/suncurio/m2mod/downloads/>

### WoW Export Tools
 A recent tool created for export of WMO, ADT and 
non-character/creature M2s. It supports exporting full ADT
map tile with the full ground textures, WMOs and even all
doodads. You require to use the Blender plugin to stitch
the files together into a single map tile with all meshes
in their correct positions


Download:
 <https://marlam.in/obj/>
 
### WoWdev wiki
 Want to deep dive into the internals of how WoW functions,
or planning on developing your own WoW tool? This public
wiki is a great place about all of the file types the WoW
engine uses. Its mostly info from 3.3.5 and 6.0.1 but still
useful info either way.


<https://wowdev.wiki/>

### WoW.Tools
By the creator of WoW export tools. Contains model explorer
map viewer, WoW build monitoring, WoW database
browser and it contains all WoW files between 6.0.1
and current build of WoW with previous build versions being 
available for download or preview.


<https://wow.tools/>