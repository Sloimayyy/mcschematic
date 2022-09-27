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

schem.save(  "myschems", "my_cool_schematic", Version.JE_1_18_2)
```
Some information on the method parameters:
**outputFolderPath**: The path to the folder on your computer where the schematic will be saved. It's using python's URI handler, so for example if the path doesn't start with a hard drive, the root folder by which the path anchors to is your project. So just inputting `"myschems"` will create a folder inside your project named "*myschems*".
**schemName**: The name of your schematic!
**minecraftVersion**: The version of Minecraft this schematic is for. Usually doesn't impact the usability of schematic files, but it's good practice to input the Minecraft version you're creating this schematic for. To indicate which version we're using, we're gonna use the *mcschematic.Version* [enum](https://docs.python.org/3/library/enum.html "enum"), where every version of Minecraft since Java Edition 1.9 is present.
So if we wanted our schematic to be compatible with Minecraft 1.13, we would use `Version.JE_1_13`.



#### Additional useful information
Schematic files grow in size very rapidly:
- Schematics saves all the blocks (even air blocks! by default the schematic is air blocks only) in the cuboid from the lowest block in XYZ and the highest block in XYZ present in itself. So a schematic with only 2 stone blocks, one at **(0, 0, 0)**, and another one at **(19, 19, 19)** will actually be saved as a cuboid of 20 blocks of length in all directions, bringing the total block saved to 20^3, which is 8000. For only 2 blocks! So be mindful when creating big schematics, 1 misplaced blocks thousands of blocks away could ruin the fun quite rapdily haha!

There exist an unfinished **mcschematic#QOL** class:
 - This class is unfinished and will probably be renamed in future release. It aims to make building schematics a bit easier. Currently only one method is present and is mainly for *computational redstoners* at the moment, but will be changed later to be more general (the next few sentences are going to get a bit technical).  The only method currently present is *QOL#signalStrengthToBarrelBlockDataString(  signalStrength: int  )* used to get the blockData of a barrel outputting whatever signal strength from 0 to 15 that you desire. blockData that can then be inputted in *MCSchematic#setBlock()*. This is pretty useful because some blockData strings tends to get very large when dealing with *blockEntities*.

There also exists a **MCSchematic#getBlockStateAt(  coordinates  )** method:
- This section is pretty technical. MCSchematic stores blocks with *NBT data* (AKA blockDatas with "{ /\* stuff \*/ }" at the end) differently than blocks without; we call these blocks, *blockEntities*. A block entity, is composed of a *blockState* which is the first part of the blockData, and an *NBT* part which is after the blockState. For example: `chest[]{Items:[{Slot:0b, Count:1b, id:"minecraft:redstone"}]}`, here "chest[]" is the blockState, and "{Items:[{Slot:0b, Count:1b, id:"minecraft:redstone"}]}" is the NBT data. 
With that out of the way, our method *getBlockStateAt()* will return which *blockState* is at the inputted coordinates. The blockState might be a chest, which probably contains NBT data, however that NBT data will not be returned, only the *chest[]* part.



#### Upcoming features (maybe)
- Updated and more defined *mcschematic#QOL* class.
- The possibility of loading a schematic from your computer directly into an *MCSchematic()* object.
- Possibly more optimisations though it might not be needed for creations less than 1M blocks in volume.
- 



#### Go wild!
MCSchematic doesn't limit how many blocks you can place, where you can place them, or how big your schematics can be. So the possibilities are only limited by code, which itself isn't limiting! 
So have fun and create abstract mathematical structures, completely custom terrain, render graphs in 3d, render graphs in 4d, pretty block gradients, bézier curves visualiser, a simple house generator, etc.