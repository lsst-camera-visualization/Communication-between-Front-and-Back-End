# Example-Commands-in-JSON
A documentation on how commands are specified in the communication between client and server.

### Sample Command

The command will be in the format:

<pre>
<b>Command</b> -argument [-optional arguments] [-Image]
</pre>
`-Image` let user specify which one of the currently opening images to process.

Right now we have proposed the following commands.

1. `show_boundary`
2. `hide_boundary`
3. `create_box`
4. `delete_box`
5. `hide_box`
6. `show_box`
7. `clear_box`
8. `viewer`
9. `chart`
10. `chart2`
11. `read_mouse`
12. `blink`

Detailed usage of the commands:

* `show_boundary [id]`

        Show boudaries on the main view.
        To show a particular boudary with specified ID is not supported currently.

* `hide_boundary [id]`
 
        Hide the boundary of the main view if it is presented.
        Sometimes need to zoom in to see the effect

* `create_box <name>`

        This will create a separate box for graph or analysis on the right of the screen.
        The box will be empty initially. Need to use other commands to add content in it.

* `delete_box <name>`
        
        Will delete the box (completely)

* `hide_box <name>`
        
        Will hide the box with only title left

* `show_box <name>`
  
        Show the box if it's hided already

* `clear_box <name>`
        
        Clear the contents in the box specified by the name.

* `viewer <name> <image>`

        Add another firefly viewer inside the box 

* `chart <name>`

        Add a dummy chart inside box. The next step is to connect to backend. It will follow the specification in this documentation when sent to the server.

* `chart2 <name>`

        The same as "chart".

* `blink <name> <image_1> <image_2> <interval> <times>`

        To show two images flash back and forth.
        blink <name> stop will stop the blinking.
        -image_1/image_2    :   Name of images need to blink. (Currently only "im0" and "im1" avaliable)
        -interval           :   time interval in ms.
        -times              :   number of times to blink.

* `read_mouse <name>`

        Add the mouse position parameters inside the specified name.
        It will show pixel coordinates of cursor's x,y and the region it correspond to.

### JSON format

The corresponding JSON file would be:
```json
{
"Command":{
  "argument_1"  : "value",
  "argument_2"  : "value",
  "..."         : "...",
  "image"       : "filename"
  },
  "DISCARD"     : "boolean",
}
```
For instance, to select a region of rectangle `create_region`:
>`create_region -rect -coord_1 -coord_2 ... -image`

```json
{
"create_region":{
  "name_id" : "name defined by user",
  "shape"   : "rectangle",
  "coord_1" : "value",
  "coord_2" : "value",      
  "..."     : "...",
  "image_id": "id for an image"
  },
  "DISCARD" : false
}
```
* Coordinates (like `coord_1`) will be specific to the shape to identify the region. For rectangle, two diagonal coordinates would be enough.
* If image is not defined in the command, it would be selected as the current image showing  by default.
* Then we can also have `select_region -name -image` to focus on a particular region of interest.

#### Possible Issue
* Do we need to send multiple commands in one json file? 
  * Though json accepts duplicate key names, it would only parse the last one. If multiple commands with same name are sent in one json object, we probably need to put "commands" into a queue or list.
