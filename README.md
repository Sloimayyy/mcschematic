### MCSchematic



#### What is it?
MCSchematic (short for Minecraft Schematic) is a python package aiming to make creating [Minecraft Schematics](https://www.minecraft-schematics.com/worldedit/tutorial/ "Minecraft Schematics") through code easy. 
#### What is a Minecraft Schematic?
In Minecraft, multiple mods such as [WorldEdit](https://worldedit.enginehub.org/en/latest/ "WorldEdit") use schematic files to save an area of the world on your computer, making you able to share and reuse it in other worlds or places of worlds. In summary, it's a big list of blocks and entities saved on our hard drives.
Note that Minecraft Schematics are **Minecraft Java Edition only**.



#### Install
We install MCSchematic using pip.
```shell
pip install mcschematic
```



#### Workflow
The workflow to create a schematic using MCSchematic is really simple:
 - **Instantiate**:
    - Instantiate the schematic object in your program
 - **Place**:
    - Place the blocks in your schematic
 - **Save**:
     - Save it as a schematic file on your computer!



#### Creating our first schematic (Tutorial)
For our first schematic, let's make one that spawns a stone block underneath our player's feet!
##### Instantiate
Let's start by importing "**mcschematic**" to our project.
```python
import mcschematic
```
Next, let's instantiate our **MCSchematic** object. You can think of it as a new Minecraft Schematic file you just created that you can freely edit until you save it on your computer.
```python
import mcschematic

schem = mcschematic.MCSchematic()
```
##### Place
Onto placing our stone block inside our schematic!
To do that, we're gonna use the *MCSchematic#setBlock(  coordinates, blockData  )* method which will set the inputted block at the inputted coordinates (which is a *Tuple*) inside the schematic.
To place a stone block underneath us, we're going to call the function with coordinates **(0, -1, 0)** and blockData **"minecraft:stone"**:
```python
import mcschematic

schem = mcschematic.MCSchematic()

schem.setBlock(  (0, -1, 0), "minecraft:stone"  )
```
**Why does "(0, -1, 0)" work?**: When placing a schematic inside a world, it's going to spawn *relative to our player*, so block coordinates in a schematic are *relative*. Meaning that *(0, -1, 0)* is going to be 1 block away in the negative Y direction from our player (so right under our feet), and *(2, 0, 0)*  will be 2 blocks away in the positive X direction.
**What is a "blockData"?**: It's the way Mojang serializes Minecraft blocks into a string; and also what we input in Minecraft commands when referring to blocks, i.e */setblock*.
A few examples of blockDatas: 
`minecraft:oak_log[axis=z]` is an *oak_log* facing the *z axis*.
`chest[]{Items:[{Slot:0b, Count:1b, id:"minecraft:redstone"}]}` is a chest with its first slot filled with one redstone dust.
So if we want to place a rightside up *stone_slab* in our schematic, the blockData used would be `stone_slab[type=top]` just like in Minecraft!
**Tip**: A common way to check if your blockData is valid, is to type it directly into a */setblock* command in Minecraft, and see if it yells at you with a red error message.
##### Save!
Time to save our stone block schematic to our hard drive to be used in Minecraft directly. We're gonna use the *MCSchematic#save(  outputFolderPath, schemName, minecraftVersion  )* method which saves the schematic to the folder *outputFolderPath*, named *schemName* and will be compatible with the *minecraftVersion* of your choice.
```python
import mcschematic

schem = mcschematic.MCSchematic()

schem.setBlock(  (0, -1, 0), "minecraft:stone"  )

schem.save(  "myschems", "my_cool_schematic", mcschematic.Version.JE_1_18_2)
```
Some information on the method parameters:
**outputFolderPath**: The path to the folder on your computer where the schematic will be saved. It's using python's URI handler, so for example if the path doesn't start with a hard drive, the root folder by which the path anchors to is your project. So just inputting `"myschems"` will create a folder inside your project named "*myschems*".
**schemName**: The name of your schematic!
**minecraftVersion**: The version of Minecraft this schematic is for. Usually doesn't impact the usability of schematic files, but it's good practice to input the Minecraft version you're creating this schematic for. To indicate which version we're using, we're gonna use the *mcschematic.Version* [enum](https://docs.python.org/3/library/enum.html "enum"), where every version of Minecraft since Java Edition 1.9 is present.
So if we wanted our schematic to be compatible with Minecraft 1.13, we would use `Version.JE_1_13`.



#### Additional useful information
Schematic files grow in size very rapidly:
- Schematics saves all the blocks (even air blocks! by default the schematic is air blocks only) in the cuboid from the lowest block in XYZ and the highest block in XYZ present in itself. So a schematic with only 2 stone blocks, one at **(0, 0, 0)**, and another one at **(19, 19, 19)** will actually be saved as a cuboid of 20 blocks of length in all directions, bringing the total block saved to 20^3, which is 8000. For only 2 blocks! So be mindful when creating big schematics, 1 misplaced blocks thousands of blocks away could ruin the fun quite rapdily haha!

The BlockDataDB:
- A static class "BlockDataDB" is present within mcschematic, which is a small library of blockData strings that people may find use for. It currently only contains every container in vanilla Minecraft with a changeable signal strength it would output. It is mainly meant for *computational redstoners* or anyone who needs containers to output specific signal strengths, but that's about it for now. You can access those blocks by doing mcschematic.BlockDataDB.\<CONTAINER IN ALL UPPERCASE\>.fromSS(\<your signal strength between 0 and 15\>).

There also exists a **MCSchematic#getBlockStateAt(  coordinates  )** method:
- This section is pretty technical. MCSchematic stores blocks with *NBT data* (AKA blockDatas with "{ /\* stuff \*/ }" at the end) differently than blocks without; we call these blocks, *blockEntities*. A block entity, is composed of a *blockState* which is the first part of the blockData, and an *NBT* part which is after the blockState. For example: `chest[]{Items:[{Slot:0b, Count:1b, id:"minecraft:redstone"}]}`, here "chest[]" is the blockState, and "{Items:[{Slot:0b, Count:1b, id:"minecraft:redstone"}]}" is the NBT data. 
With that out of the way, our method *getBlockStateAt()* will return which *blockState* is at the inputted coordinates. The blockState might be a chest, which probably contains NBT data, however that NBT data will not be returned, only the *chest[]* part.

Other MCSchematic methods you need to know about:
- **MCSchematic#placeSchematic(incomingSchem: MCSchematic, placePosition: tuple)**: Place the blocks of "incomingSchem" inside the caller.
- **MCSchematic#makeCopy()**: Returns a deep copy of this schematic
- **MCSchematic#getSubSchematic(corner1: tuple, corner2: tuple)**: Returns a new schematic containing all blocks within the boundary inputted.

The very important **MCStructure**:
- Inside every MCSchematic lies an MCStructure. It's the object that actually stores the blocks etc.. An MCSchematic is only an accessor to this class, in my effort to try to make mcschematic more general but lack of motivation made it fall flat. However, it does have some cool methods, like *transform methods*, which are meant to be a "WorldEdit at home" kind of set of tools.

The MCStructure transformation methods:
- **MCStructure#translate( offset )**: Moves every block of the structure by the inputted "offset" vector. The move is done in-place (aka, it doesn't create a new structure when moving the blocks).
- **MCStructure#scaleXYZ(anchorPoint, scaleX, scaleY, scaleZ)**: Scales from an anchor point in X, Y and Z by the inputted amounts, also done in-place.
- **MCStructure#scale(anchorPoint, scale)**: Same as the method above but we're scaling in all axes by the same amount.
- **MCStructure#rotateRadians(anchorPoint, yaw, pitch, roll)**: Rotates the entire structure around an anchor point by a yaw, pitch, and roll expressed in radians. More details about the rotation axes etc. in the source code. (Also, it can rotate blocks like stairs!). Done in-place.
- **MCStructure#rotateDegrees(anchorPoint, yaw, pitch, roll)**: Same as method above but in degrees.
- **MCStructure#centerAround(anchorPoint, structureBounds)**: Centers the structure around an anchor point. You have to pass in the bounds of the structure manually using MCStructure#getBounds() manually, as it is pretty costly and is better handled by the programmer for caching reasons.
- **MCStructure#center(structureBounds)**: Centers the structure around (0, 0, 0) as best as it can.
Other MCStructure methods you should know about:
- **MCStructure#blockStateIterator**: Returns a generator over every block that isn't air in the structure.
- **MCStructure#getBlockPalette**: Returns an unmodifiable view of the block palette as how it would be saved in the schematic file.
- **MCStructure#getInternalBlockPalette**: Returns an unmodifiable view of the internal block palette. (It's different than the block palette returned in the method above for performance reasons).
- **MCStructure#cuboidFilled(), MCStructure#cuboidHollow() and MCStructure#cuboidOutlines()** are 3 useful block generation methods you can use if needed.

This "additional info" section *isn't* exhaustive (nor is this entire readme):
- While MCSchematic doesn't have crazy docs, feel free to look into the code and see what methods may interest you, the source is fully documented in hopes that it'd be enough while MCSchematic doesn't have official clean outside documentation. IDEs usually show you the method docs as well and also can autocomplete which methods you have access to. However I did my best to highlight succintly the main methods you'd wanna use and would find useful!




#### Go wild!
MCSchematic doesn't limit how many blocks you can place, where you can place them, or how big your schematics can be. So the possibilities are only limited by code, which itself isn't limiting! 
So have fun and create abstract mathematical structures, completely custom terrain, render graphs in 3d, render graphs in 4d, pretty block gradients, bézier curves visualisers, a simple house generator, etc.
