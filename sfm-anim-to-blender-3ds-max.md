# Exporting SFM animations to Blender and 3DS Max, or how to use SFM as an animation tool

If you're a long time user of Source filmmaker and you recently migrated to a new application like Blender, Maya or 3DS Max, you might encounter some pretty painful migration pains and wish you could still use SFM even when its too limiting for your workflows.

But do not worry! You can actually still animate in SFM and then render in the application of your choice, if you're willing to jump through a lot of hoops that is.

**This guide ASSUMES that you’re at least somewhat familiar with either Blender or 3DS Max and Source engine/Source Filmmaker.**  

**It is not recommended to use this kind of workflow and you should preferably learn the animation system of your corresponding 3D application. Its recommended to fully go through this document before following anything in it.**

You cannot export the entire scene animations and your animation will be in the Y axis up coordinate system. You can only export the animation of an individual model and your skeleton should contain a root bone.

### Requirements
[Knowing how to get your model into SFM](/model-into-sfm)  
CrowbarTool: <https://steamcommunity.com/groups/CrowbarTool>  
Blender Source Tools: <http://steamreview.org/BlenderSourceTools/>  
3DS Max Source Tools: <https://knockout.chat/thread/806/1>  

### Preparing your model and scene

When starting a new scene, you should always set it to the target frame rate you will render your final scene in. Depending on what you're animating, you can exclude some bones from the model in order to keep it simple, but the bone structure should stay the same. Please keep in mind that the Source engine has ton of limitations. One such limitation is [Bone Weights Per Vertex Limit](https://github.com/ballerfuturistic/sfmnauts/blob/master/general/rigging.md#bone-weights-per-vertex-limit) and you should keep that in mind. **Do not use `rootTransform` as it doesn't get exported.**   

Before exporting, remove any constrains and IK rig on your animation set.


### Exporting and importing the animation

So you have finished animating and you're ready to export your animation.   
Trim your shot to the amount you want to export, select all of the bones, right click on it and select `Export Animation`. This will open the animation export menu. You should select `Trim To Shot` as trim type and **make sure you enable the Ascii option for export** as the tools can't handle binary DMX files.  

#### Importing the animation into Blender

For Blender, first load up your model into the scene, import the DMX animation, fix the bone rotation and thats it.   


#### Importing the animation into 3DS Max

Start with a completely blank scene and import the DMX animation with the Source tools plugin installed. After importing the animation, you need to fix the rotation of the root bone as 3DS Max is using the Z up axis system and the animation is exported for the Y axis system. If you used the Biped system for your initial model skeleton and then used the IK system in SFM, you also need to clone and rename the pelvis bone to be the root bone. Something like `Bip001 Pelvis => Bip001`.  

After that's done, export the scene as FBX with the default settings and import it into your scene with your model that you want to apply the animation to. When you get FBX Import dialog, under `File Content` option, select `Update animation`. [Yes that's the proper way to do it](https://knowledge.autodesk.com/support/3ds-max/learn-explore/caas/CloudHelp/cloudhelp/2016/ENU/3DSMax/files/GUID-606356A7-B8CD-434A-A6E3-584419830A28-htm.html). If you're using Biped, make sure your biped skeleton is not in figure mode. You might need to adjust specific frames if you use IK resolvers, since it can make things spaz out.


