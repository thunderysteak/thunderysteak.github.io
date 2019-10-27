# Getting the best image quality out of SFM

### DISCLAIMER
Source engine is over a decade old and many things on here may break things for you, but it is advised to use them.
I’m not responsible for any breakages or ruined project files. Use this at your own risk.

### Progressive Refinement Settings
This is one of the most important parts people almost always neglect, yet its one of the
most important things for your animation/poster quality. By default, SFM by default
uses “Camera settings”, which has all of the sample sizes set to the absolute minimum.
DoF and MB sample size affects the AO quality volumetric lighting, and any radius
lighting. Always push these settings up to the absolute max! Yes it will greatly increase
the rendering times, but quality art requires that specific spit & polish. It only costs you
time and is not something like V-Ray where a single frame is 12+ hours.

![](/img/sfm_2019-10-20_03-03-23.png)

### Rendering Image Sequence instead of a Poster
This one is a double edged sword. Rendering as a Poster has a benefit of being able to 
render at any custom resolution you desire, particle systems will render exactly at the time of the playhead and posters don’t have 
issues with the DoF plane not actually being where the final image focuses on. Now here’s WHY you should render an image as an
image sequence. Ambient Occlusion ends up blocky on high resolution images when rendered as Poster, Poster renders do not 
render bloom and because Image Sequence doesn’t use tile rendering, render times will be faster. It is generally suggested to render
your image as an Image Sequence.

### 32bpc workflow for post-processing of images

If you do not plan on doing any kind of post processing of your images, you can 
skip this one. By default, all of SFM’s renders are utilizing only 8 bits per channel,
which doesn’t contain much color information. If you utilize 32 bits per channel
render, you’ll get a ton more of color information. Its like 32bpc was RAW 
and 8bpc was regular JPEG. To use this, render your image sequence in the PFM format. **Do NOT tick the “Write PFM depth file”.**
You can open the PFM file in Photoshop natively without any plugins for any post-processing.

![](/img/sfm_2019-10-20_03-42-05.png)

### The “highest possible quality” rendering setup

# VERY BIG WARNING
DO NOT use this for editing of your scene and DO NOT SAVE YOUR SCENE WITH THESE LAUNCH OPTIONS! YOU’LL BREAK THE FILE!
Create a desktop shortcut of the sfm.exe and DO NOT add this into your launch options in Steam. Use this for RENDERING ONLY!

```
sfm.exe -nosteam -nop4 -num_edicts 8192 -sfm_resolution 2160 -sfm_shadowmapres 8192 -monitortexturesize 1024 -r_novis 1 -reflectiontexturesize 1024 +mat_envmapsize 256  mat_forceaniso 16 +r_waterforceexpensive 1 +r_waterforcereflectentities 1 +mat_wateroverlaysize 1024 +r_hunkalloclightmaps 0 +flex_smooth 0
```

### Breakdown of the launch flags

`-nosteam` & `-nop4`
Launches SFM without Steam and Perforce P4V
integration. Doesn’t make SFM lockup when 
Steam Crashes.  
`-num_edicts 8192`
Sets the entity limit for map/game entities.
Lower to 4096 if you encounter problems  
`-sfm_resolution 2160`
Changes SFM’s render resolution
to 4K maximum (720p is default)  
`-sfm_shadowmapres 8192`
Raises the shadow map resolution to the
maximum power of two value   
`-monitortexturesize 1024 `
Raises the size of RTcamera monitor
textures. Default is 256  
`-r_novis 1`
Disables the Potentially Visible Set system of
Occlusion Culling.  If you’re having any kind of
map visibility issue, this might fix it.
Disable if you get any issues.  
`-reflectiontexturesize 1024`
Size of water reflection textures. 
The default is 1024.  
`+mat_envmapsize 256`
Adjusts the resolution of environment
 maps that are used for reflective surfaces.  
 `+mat_forceaniso 16`
Forces 16x passes of forces anisotropic filtering.
SFM uses only one pass by default.  
`+r_waterforceexpensive 1`
Forces water to reflect the world  
`+r_waterforcereflectentities 1 `
Forces water to reflect all entities  
`+mat_wateroverlaysize 1024`
Sets the resolution of water distortion  
`+r_hunkalloclightmaps 0`
Prevents Engine Hunk Overflow crash.
Required for num_edicts set to higher than
4096 and maps with lightmap errors  
`+flex_smooth 0`
It SHOULD disable flex decay on models.
Should fix problem where custom model
flexes keep resetting on 
custom models






