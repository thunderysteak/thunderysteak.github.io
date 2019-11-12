---
layout: default
---

# Recognizing WoW files with no listfile or extension

So, a new WoW patch came out with a model, sound file or a texture that you want to
get and it’s too fresh to already have a listfile or for tools to support the new patch version
and you’re inpatient. What are your options? Give up? Nah, its time for some very 
beginner’s reverse engineering. You see, files have this thing called 
“magical number” in the first 4 bits of their file header, defining what kind
 of file they are. Since Blizzard removed the filenames
 for everything, we can abuse this to get our files


CASCView will be functional without a listfile. Open the WoW CASC archive and go to the FILE_DATA_ID, 
which contains the original CASC archive file structure with no listfile. You can check on wow.tools the last file data ID of the previous patch, giving you the starting point from which file ID the new patch
content starts. **Now this is where the fun begins**. 

![](/img/ubuntu_2019-10-10_21-26-00.png) ![](/img/ubuntu_2019-10-10_21-27-16.png)

The magic number of the files in ASCII correspond to these files:  
.anim = AFM2, BLP = BLP2, .skin = SKIN, M2 = MD21, ADT = REVM, ogg = oggS, and so on.



I suggest you research the magical numbers first. You can write a script to
override the .dat extensions for you. This should give you a pointer how to write your bash script. 


![](/img/ubuntu_2019-10-10_21-03-29.png)

You should really be using `file --magic`, with an example [here](https://github.com/Marlamin/wow.tools/blob/master/builds/scripts/wow.mg).

If you are trying to rip out a character model but don’t want to dabble with the file structure itself
to locate the skin files, quick and dirty way how to find the skin files you’re lacking is by extracting
the M2 itself and then use M2Mod Redux to load up the M2 into it, look at the log output
and get the file IDs for the missing files from the error logs. 
It will also spit out the an M2i to work with once you get all the files.


![](/img/M2ModRedux_2019-08-02_22-43-15.png)

Refer to the [WoWdev.wiki](https://wowdev.wiki/) for more information about the files and inner workings of WoW.