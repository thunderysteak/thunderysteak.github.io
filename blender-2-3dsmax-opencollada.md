# 3DS Max and Blender interoperability

If you ever tried to move a model from Blender into 3DS Max, either due of specific Blender toolsets being more user friendly or better, you might find that using the Blender FBX exporter will lead into a lot of issues unless you play around with the settings each time you try to import a file. One such issue might occur when you try to import a FBX from Blender and leave the "Convert bones into dummies" option on:  

![](/img/3dsmax_2020-02-07_15-42-18.png)  

You might try to export the model into a DAE/COLLADA format, since it is an [ISO/PAS 17506:2012](https://www.iso.org/standard/59902.html) standard, but you'll be met with the same exact results, on top of more issues and ton of warnings:

![](/img/3dsmax_2020-02-07_17-03-09.png)

You might be asking yourself, why's that? Open the import dialog again and look at the "Files of type" dropdown. You'll see that its using Autodesk's implementation of COLLADA.

![](/img/c3e041.png)

You might get away with using Alembic, but if your mesh is skinned/rigged, it will not export that.

### KhronosGroup OpenCOLLADA

You might be asking, what's [OpenCOLLADA](https://github.com/KhronosGroup/OpenCOLLADA)?  

>COLLADAMax and COLLADAMaya are new implementation of a 3ds Max or Maya plug-ins to export scene or parts of it to a COLLADA file, released under an MIT-license.
>
>In contrast to other existing COLLADA exporters, these new plug-ins do not store the COLLADA document in an intermidiate data model but writes it directly to file. This leads to a dramatic reduction of memory consumption and to much better performance.

In a nutshell, its a plugin for import and export of COLLADA/DAE files and its created by the creators of the COLLADA format themselves. It imports and exports clean DAE files and supports the must up to date version of v1.5 of the COLLADA format.  

Unfortunately, it only supports 3DS Max versions up to 2018.  

When we import the same file with the OpenCOLLADA plugin, we'll instantly see different results:

![](/img/3dsmax_2020-02-07_17-34-11.png)


### Download, Usage and Installation

Unfortunately, KhronosGroup's S3 bucket containing the builds no longer exists, so your only option is to build it yourself or [use archive.org to download the compiled builds](https://web.archive.org/web/20190430121822/https://github.com/KhronosGroup/OpenCOLLADA/releases).  

Follow the instructions in the Zip file how to install the plugin.  

When importing/exporting, make sure you select the OpenCOLLADA format, as opening a DAE file will default to Autodesk COLLADA.

![](/img/225076.png)

### Compiling it yourself

To see how to compile it yourself, please check the [BUILD](https://github.com/KhronosGroup/OpenCOLLADA/blob/master/COLLADAMax/BUILD) text file for instructions. 

```
Requirements:
-------------

- Autodesk 3ds Max
  To build the COLLADAMax plug-in you need to have a version of 3ds Max, including the SDK, installed.
  Supported versions are:
  3ds Max 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018

- MS Visual Studio 2015 for loading the solution 
   MS Visual Studio 2008 to compile for Max 2011/2012
   MS Visual Studio 2010 to compile for Max 2013/2014
   MS Visual Studio 2012 to compile for Max 2015/2016
   MS Visual Studio 2015 to compile for Max 2017
   MS Visual Studio 2015 update 3 with Windows SDK 10.0.10586 for Max 2018

IMPORTANT : DO NOT let visual studio convert the project files if it ask to when loading the solution.
  It is normal that the solution references different toolchains.

(optional)
- GIT comand line tool in your path. This is used by script\create_version_info_h.bat to create include\COLLADAMaxVersionInfo.h file in the COLLADAMAX prebuild


Building:
---------
Before you can start to build you need to set an environment variable that points to the MAX SDK

The variable is MAX_SDK_PATHXXXX with XXXX replaced with the version of 3DSMAX you want to build for.

For example:

MAX_SDK_PATH2012=C:\Program Files (x86)\Autodesk\3ds Max 2012\maxsdk

would be the value for a 2012 default installation. 

remember to set the variable before launching visual studio, preferably using the 

Then selet the corresponding build target in visual studio

Release or ReleaseStatic or Debug or DebugStatic for the correct version of 3DS MAX.


No longer supported:
--------------------

The following versions used to be supported by the plug-in.
But those are no longer maintained/tested. The source code still contains reference to those versions


3ds Max 7      (32bit)
3ds Max 8      (32bit)
3ds Max 9      (32bit/64bit)
3ds Max 2008   (32bit/64bit)
3ds Max 2009   (32bit/64bit)
3ds Max 2010   (32bit/64bit)
```

In my case, I use 3DS Max 2018 and will require the SDK, Visual Studio 2015 update 3 with Windows SDK 10.0.10586.  

Due of the compilation process being fairly simple, I will not document how to do it. In a nutshell, you only open the COLLADAMax VS Solution and compile it.

### Obtaining the SDK and dependencies

To obtain the 3DS Max SDK for your version, run the setup for 3DS Max and go to the Tools and Utilities menu, where it will be available for downlad.  

[Click here to download Visual Studio 2015 Update 3](https://my.visualstudio.com/Downloads?q=visual%20studio%202015%20Update%203). I'd highly suggest to download the offline ISO installer.  

[Click here to download Windows 10 SDK (10.0.10586.212) and Microsoft Emulator for Windows 10 mobile (10.0.10586.11)](https://go.microsoft.com/fwlink/p/?LinkID=698771).

