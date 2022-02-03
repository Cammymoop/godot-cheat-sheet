How do I do X in Godot/gdscript

offical docs: [here](https://docs.godotengine.org/en/stable)

## Reload the current scene:
`get_tree().reload_current_scene()`

## Generate random numbers:

`randf()` for a random float from 0 to 1 (I use this one almost exclusively)
`randomize()` call this once when you start to initialize the RNG with random seed, probably system time or whatever
use `seed(some_integer)` to set the RNG seed manually

You can create an instance of the `RandomNumberGenerator` class instead of using the global methods, 
but it has more stuff, notably you dont have to make your own `randi_range()` for ints in a range.

Godot also implements open simplex noise for noise stuff. (noise has a lot of advantages over RNG)
see the docs for the OpenSimplexNoise class <https://docs.godotengine.org/en/stable/classes/class_opensimplexnoise.html>

## Change the game speed:
use `Engine.time_scale`
don't set this to 0 to pause if you want timers that are still able to run.
use the `get_tree().paused` property and set the Timer's `pause_mode` to `Process`

## Pause the game:
use `get_tree().paused = true`
if you want things to still happen while paused set the Node's `pause_mode` to `Process`
the default pause mode is `Inherit` so child nodes will also still run

## Get a reference to something from something else:
if theres behind the scenes code or data that you want accessable from everywhere, see "Make/Use a singleton"

for accessing things like the player, or just any specific ubiquitous object
The quick and dirty, but still fast and relatively safe way to do this is to add a Group to the node you want to find

e.g. for making the player findable by other stuff:
select the player's node in the tree
click the "Node" tab next to the inspector tab where signals are
click the "Groups" button
type "Player" into the textbox and click "Add"
to find the player from other nodes do something like:
```
var found_player = get_tree().get_nodes_in_group("Player")
if found_player:
    var player = found_player[0]
	# do stuff with player
```
something like this for finding nodes is useful to wrap in a utility function inside of a singleton for easy access from any script
so scripts can do something like
`Utility.get_player()`

## Make/Use a singleton (Script that is automatically instaciated and accessable by any other scripts regardless of scene)
Ok, first off **DO THIS**, singletons are just really handy for utility functions, game settings, and various scripts that handle behind the scenes stuff.

In the script editor window make a new script by selecting File -> New Script...
It should already be inheriting `Node`, select that if not
save it wherever makes sense (I usually put all my .gd scripts in a src/ directory)

put a bunch of useful stuff in the script. Utility functions, global variables (bad idea), 
global properties with getter/setter (slightly better idea), game settings, etc.

go to Project -> Project Settings -> AutoLoad
click the folder by "Path:" and select your script file
give it a name or use the one automatically filled in based on your file name
click "Add"

Now to access the stuff in this script from another script:
you can access the node by it's name directly e.q. with a singleton called Utility:
`Utility.do_things()`
or you can get it with:
`get_node("/root/Utility")`
but idk why you would do it that way unless you didn't read this first

more info: <https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoload.html>

## Make my pixel art not blurry
click one of the image files in the file tree
click the import tab next to the scene tree tab
uncheck "Filter"
click the "Preset" button and click "Set as default for Texture"
click reimport to update this image
either delete all the existing .import files for your other images or manually fix them all in the import tab
new images will now already be non-filtered

## Make tiles from an image
Note if your textures are filtered make sure your tiles have spacing between them so they dont bleed into each other

First create a tilemap node then in the inspector click the tile set arrow and click "new tileset"
when you want to edit the tileset just click the tileset in the inspector for the tilemap

in the tileset section that opens at the bottom by default:
click the + button to add your tiles image (I'd recommend having your tiles in 1 or a few images rather than one image per tile)
click the image in the list on the left to start making the tiles
click "new single tile" dont worry about the grid yet
click and drag a rectangle

now that you have a tile you can edit the snapping grid and seperation etc in the inspector on the right
use the little magnet button above the tiles to enable/disable the snapping
to edit the collisionbox of a tile first select it then click the collision button
click the "new rectangle" or "new polygon" button and draw the shape

the collision shapes are automatically created by the tilemap wherever you place the tiles so if you need to change the collision properties do it on the tilemap
you can set individual tiles to be one way collision in the inspector

## Move a specific node not all the other stuff in the same place as it
click the move tool up by the cursor icon at the top
switch back to the select tool to be able to select stuff by clicking it

## Make autotiles work
Once you've drawn enough tiles to use for the autotiler you need to set up the bitmask to get them to work
click new auto tile
drag the region over all the tile variants
over in the inspector, switch the autotile mode to 3x3 minimal (I wouldn't bother with 2x2 unless its for something that is always at least 2 thick)
click on the bitmask button
click on the tiles to paint the bitmask, left click to set the bit to on (red)
right click to clear
shift left click to set the bit to ignore (blue checker)

if you dont want to make tons of tile variations (ignore internal corners) just use this pattern
![EZ bitmask](easy_bitmask.png)

if you're drawing autotiles and they look all broken it's probably because your brush is mirrored or rotated, hit the little paintbrush at the top right to reset the transform

## Enable or disable part of an area or physics body
you can disable collisionshape nodes to make the shape they hold stop interacting with stuff
but you have to call `collision_shape.set_deffered('disabled', true)`
instead of setting it directly

This is a good way to change the size of the player's physical body for example (having 2 collision shapes that you switch between)
just keep in mind it wont be changed until the next idle frame

## Make a control size itself realtive to the game viewport
make it the root node of the scene
is there any other way to do this? that would be nice...

## Make a prefab
use scenes

## Close the game
`get_tree().quit()`

## Use scenes as prefabs
TODO

## Change the font or font size used by UI elements
TODO

## Generate collision shapes in code
TODO

## Stop hitting phantom corners in a solid surface in a tilemap
quick and dirty fix: use a circle or capsule collider (not very good solution for platformer)
TODO

## Make paralax layers
TODO

## How does all this UI stuff even work
TODO

## Get or set "Theme Properties" (theme overrides) in code
a "theme property" is a named override of some type
the possible types are: color, constant, font, icon, shader, and stylebox
use the appropriate has and set method to check and add these via code (has_X_overrride, add_X_override)

to get the current value which is either an override or from the theme use get_X
e.g. to set a theme property called "panel" which is a stylebox use:
`add_stylebox_override("panel", new_panel)`
set it to null to disable the override:
`add_stylebox_override("panel", null)`
to get the current value:
`var cur_panel = get_stylebox("panel")`

## Save stuff to disk/Read save files
put files in the "user://" file system

## Find resource files or files in the user filesystem
Use the Directory class see docs for an example <https://docs.godotengine.org/en/stable/classes/class_directory.html>