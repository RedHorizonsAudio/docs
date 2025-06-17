# Transferring animations from one weapon to another (Apex -> Apex!)



Many thanks to Kralrindo for the video and help with the actual process.


# Prerequisites (latest versions):


**RSX** (**ReSource eXtractor**, tool for previewing, inspecting and exporting assets from .RPAKs and .STARPAKs, the successor of Legion+, constantly updated) - https://github.com/r-ex/rsx

**THESE SETTINGS MUST BE USED FOR R5 RELOADED:**

![[Pasted image 20250616154514.png]]

## Export Formats

ui: TXT
mdl_: RMDL
uiia: DDS (High Quality)
impa: TXT
arig: RRIG
uimg: DDS (Textures)
matl: Uber (Raw)
wepn: TXT
aseq: RSEQ
shdr: MSW
txtr: DDS (Mip Mapped)
shds: MSW
subt: TXT
font: PNG (Atlas)

ReSource references Respawn's branches of the Source Engine, hence Re(spawn)Source.


**Legion+** / **LegionPlus** (the predecessor of RSX, tool for previewing, inspecting and exporting assets contained in .RPAKs and .STARPAKs, **DISCONTINUED, NO LONGER BEING MAINTAINED AND UPDATED!!**) - https://github.com/r-ex/LegionPlus , preferably zer0byte's forked and updated version, which has features missing from the latest stable release of Legion+ and also from RSX (as of 17.06.2025. **!!!THIS WILL CHANGE IN THE FUTURE, RSX WILL BE UPDATED TO RECEIVE THE MISSING FEATURES!!!**)


**RePak** (tool for packaging your own custom .RPAKs and .STARPAKs) - https://github.com/r-ex/RePak


# Optional, but nice to have


**Kralrindo's Json Compiler for Repak** (automates the process, saving a lot of time and energy) - https://github.com/kralrindo/JsonCompiler - you have to BUILD the solution (.sln) yourself, compiling the binary! Microsoft Visual Studio 2022 with C, C# and C++ development packages recommended.

**DISCLAIMER: THIS TUTORIAL WILL BE USING THIS TOOL!**


# The process


## Step 1

You need to determine which **viewmodel** (the model rendered / drawn in first person - it's usually preceded by the prefix "v" or "ptpov", from "Pilot PoV (Point of View)", originating from the Titanfall series, where playable "Pilots" piloted "Titans") you want to use, to transfer the animations from another model onto, and the name and path of the viewmodel (such as weapons/wingman_elite/ptpov_wingman_elite.rmdl), using **RSX** (**ReSource eXtractor**) or by looking through game files yourself.

The model rendered in third person is called the "worldmodel", and is usually preceded by the prefix "w". Example: "w_r101_sfp.rmdl" with the path "weapons/r101_sfp/w_r101_sfp.rmdl" (preced by ROOTASSETRPAK/ - not mentioned in the file path because it's the root)

Strings referencing models, paths, etc. *usually* do **not** include the extension at the end (such as .rmdl)

For Apex Legends, Respawn uses **.rmdl** files (**Respawn model**), which serve as containers for models, textures and animations, etc.

## Context & History

--- 

Previously, [.mdl](https://developer.valvesoftware.com/wiki/MDL) (Source proprietary model format) alongside [.phy](https://developer.valvesoftware.com/wiki/PHY) (physics), [.ani](https://developer.valvesoftware.com/wiki/ANI(animation), [.vtx](https://developer.valvesoftware.com/wiki/VTX) (mesh strips) and [.vvd]() (Valve Studio Model Vertex Data Files) were used to store this data.

[.smd](https://developer.valvesoftware.com/wiki/SMD) (StudioModel Data files) were also used, for analysis and compilation by studiomdl.exe, the model compiler for the Source engine. 

[.dmx](https://developer.valvesoftware.com/wiki/DMX) (Data Model eXchange) files, familiar to experienced Source Filmmaker users, were used as well.

**Key-Value Pairs** or **keyvalues**: pairs consisting of a **KEY** with a corresponding, associated **VALUE**, which can be an integer, a string, etc.

They are used for configuring parameters corresponding to objects, such as rate of fire, magazine size, etc.

Not to be confused with Valve's KeyValues format.

These Key-Value pair files usually look something like this (from mp_weapon_esaw.txt, the Devotion's plaintext keyvalue file):

WeaponData <- main keyvalue container name
{ <- opening the main keyvalue container
// General <- developer comment for ease of reading and programming

-> a **KEY**    "printname"   									"#WPN_ESAW"      <- a **VALUE**
"shortprintname"								"#WPN_ESAW_SHORT"
"description" 									"#WPN_ESAW_DESC"
"longdesc"										"#WPN_ESAW_LONGDESC"
.
.
.
.
.
.
// Models

"viewmodel"   									"mdl/weapons/hemlock_br/ptpov_hemlock_br.rmdl"
"playermodel" 									"mdl/weapons/hemlock_br/w_hemlock_br.rmdl"
"holster_type"									"rifle"
.
.
.
.
.
} <- closing the main keyvalue container

With possible modifications such as using dollar sign (" $ ") as a prefix for the keys, commas ( " , " ) after each value and colons ( " : " ) after the keys.

---


## Step 2: 

Using RSX, extract (simple double click) the **.rmdl** of the weapon you want to transfer animations **to** and the **.rrig** of the weapon you want to transfer animations **from**. **MAKE SURE YOU HAVE "EXPORT ASSET DEPENDENCIES" CHECKED, OTHERWISE YOU WILL BE MISSING CRITICAL FILES!!!**


## Step 3:

After decompressing Kralrindo's Json Compiler for Repak, build the .sln to get the compiled binary.

In JsonCompiler's main folder, rename R5Reloaded_Asset_Database.db to something like R5Reloaded_Asset_Database1.db, to prevent the files that we want to package into an .RPAK (which already exist in the game) from being ignored (because it ignores files that already exist).

Afterwards, go to the exported_files directory in your RSX installation root directory / folder.

Right click empty space in the folder to get the context menu and open a CMD / Terminal window in the folder. Drag the executable JsonCompiler.exe into the window, then drag the exported_files folder from RSX's root folder into the command prompt window, then press ENTER. You should see a message confirming the successful conversion into a .JSON and, on the next line, the output path for said .JSON.


## Step 4:

You can use a text editor with or without a .JSON syntax highlighting extension, but it is recommended to use software like Visual Studio Code 2022 with a .JSON syntax highlighting extension or a text editor like Notepad++ with a .JSON syntax highlighting extension.

Open output.json with one of the above-mentioned methods and, starting from the top, delete the key-value pairs (keyvalues or key values, not to be confused with Valve's KeyValues format) mentioning **.aseq**s (**animation sequences**), **.shds** files (**shaders**), **.txtr**s (**textures**) and .**matl**s (materials), as they are completely unnecessary, since we already have all of them in our existing **.rmdl** that we want to transfer animations to and we don't want to overwrite them. Delete the keyvalues up to where you see .**rrig**s OF **THE MODEL YOU WANT TO TAKE THE ANIMATIONS FROM** being mentioned. The **.rrig**s (Respawn Rigs - see: rigs and rigging in animation) are vital - they are tied to the animations.


## Step 5:

Copy the .rrig path of the model you want to transfer animations FROM (such as: "animrig/weapons/data_knife/ptpov_data_knife.rrig", scroll to the bottom of the .json file and, in the last key-value pair, look for the $animrigs **KEY**, then, in the corresponding **VALUE**, replace the path of the .rrig of the model you want to transfer the animations TO, such as "animrig/weapons/r101_sfp/ptpov_r101_sfp.rrig".



## Step 6:

Scroll up to the top of the .JSON file and, for the "streamFileMandatory" and "streamFileOptional" **KEYS**, modify the name of the **output** in the paths to something else, such as "testrpak.STARPAK" and use the exact same name for the "name" **KEY**, obtaining something along the lines of:


"name": "testrpak",
"streamFileMandatory": "paks/Win64/testrpak.starpak",
"streamOptional": "paks/Win64/testrpak.opt.starpak",


**Save the .JSON file!**


## Step 7:

In the RSX root folder, rename "export_assets" to simply "assets" and create a new folder named "build". 

Copy RePak.exe and paste it into the RSX root folder.

Drag the "assets" folder onto the RePak.exe executable .

You should now see the packaged .RPAKs and .STARPAKs in the "build" folder.

Copy everything except for the .STARMAP file.

Navigate to the root folder of your R5 Reloaded build installation (NOT the launcher folder! The R5 Reloaded build folder contains r5apex.exe) and create a folder that you can name anything, for example "testmod".

Inside the folder, create another folder named "paks", then open it.

Inside "paks", create a folder named "Win64", then open it.

Inside, paste the contents that you copied earlier.

Navigate back to the "mods" folder, go to Kralstuff, paks, Win64 and copy "preload.rson".

Paste "preload.rson" file into the Win64 folder that **YOU** created, after navigating to it.

Open "preload.rson" with something like a text editor, then rename "common_kral.rpak" to the .RPAK you created (i.e.: "testrpak.rpak).

Copy "mod.vdf" from the "Kralstuff" folder and **paste it into the "testmod" folder**, or whatever you named the parent folder you created (with the folder "paks" and "Win64" as children).

Open the "mod.vdf" file you pasted into "testmod" with a text editor and change the 
"name" and "id" keys to "testmod" or whatever else. You *can* and *should* also change the "author" key to something else, like your name or nickname. You can also edit the "description" key if you want to

Save the file.

Navigate to the "mods" folder of the ROOT directory of your R5 Reloaded build installation, open "mods.vdf" (mod**S**.vdf with an "s" at the end!) with your text editor / IDE of choice and add a new key "testmod" (or remember whatever you named it) with the value "1" (between quotation marks!). 

**SAVE THE FILE. THIS LAST PART WILL MAKE SURE THAT YOUR MOD IS REGISTERED BY R5 RELOADED AND ACTIVATED!**

Congratulations, you are done! You can now open R5 Reloaded and enjoy your mod.