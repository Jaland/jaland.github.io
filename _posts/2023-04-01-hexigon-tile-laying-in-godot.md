---
layout: post
title:  "Hexagonal Tile Laying in Godot"
tags: godot
category: Godot
---

# Hexagonal Tile Laying in Godot

Started working on a Godot/Quarkus version of the [Uk'otoa](https://boardgamegeek.com/boardgame/323206/ukotoa) boardgame as a fun side project.

Work (as it always does) got in the way and I have moved on to other things at this point but I did want to do a quick post about the Tile laying logic that I used in the game because it is a little tough to wrap your mind around and I figured this could be useful to someone else that wants to do Hexagon Tiles in their game.

## Uk-otoa Rules (Quick)

I won't bore you with all the rules of Uk-otoa but it is important you understands the setup part since that is what the code below is for. 

At the start of the game players will take turns putting out tiles with the following rules:

- A center tile is place somewhere (does not matter where)
- Each Tile has an arrow with a direction
- Players take turns placing a tile adjacent to the previous tile
  - The new tile **must** be placed where the previous tile is pointing

That is really it that is all you need to know.

## High Level

The basic idea of how we can do Hex Tiles (and now why I realize so many old games did hex but no one does Octagons) is that a hex grid can be made by just creating a normal grid, and offsetting every other row.

How to do this and make it look good is a little tricky so instaed of trying to explain it in text I found this video of someone smarter and better looking than me that does a better job:

[`TileMap` creation video](https://youtu.be/3WDH5QgnUf8?t=169)

## The Code

> Note: This is **all** the relevant code. Sections below break it down more

**Main Code**

```go
# This is the tilemap that will be used to place the ship tiles
extends TileMap


onready var overlay:TileMap = get_parent().get_node("ShipMap_Overlay")

# List of all the ship tiles in order
# Note the object will contain two properties
# position: The position of the tile
# type: The type of tile
var ship_tiles = []

var current_ship_tile_type = 0

# Using for temporary random number generation
var rng = RandomNumberGenerator.new()


# Declare member variables here. Examples:
# var a = 2
# var b = "text"


# Called when the node enters the scene tree for the first time.
func _ready():
  pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
#func _process(delta):
#	pass


func _input(event):
  var adjusted_position = Vector2(get_global_mouse_position().x - 20, get_global_mouse_position().y - 20)
  if event is InputEventMouse:
    adjusted_position = Vector2(get_global_mouse_position().x - 20, get_global_mouse_position().y - 20)
   # Mouse in viewport coordinates.
  if event is InputEventMouseButton && event.pressed:
    print("Adding at: ", adjusted_position)
    rng.randomize()
    var tile_type = current_ship_tile_type
    var tile_pos = world_to_map(adjusted_position)
    add_tile(tile_pos, tile_type)
  
  # Show an overlay so the user knows where the tile is being put down
  if event is InputEventMouseMotion:
    overlay.clear()
    var tile_pos = world_to_map(adjusted_position)
    overlay.set_cellv(tile_pos,  current_ship_tile_type)
    
  # Lets me clear for testing
  if Input.is_action_pressed("ui_cancel"):
    clear()
    ship_tiles.clear()
    
  # Lets me clear for testing
  if Input.is_action_pressed("rotate"):
    current_ship_tile_type = (current_ship_tile_type + 1) % UkatoaUtils.ship_tile_types.size()
    print("Rotating tile new current tile is: ", current_ship_tile_type, " (", UkatoaUtils.ship_tile_types.get(current_ship_tile_type), "")
    var tile_pos = world_to_map(adjusted_position)
    overlay.set_cellv(tile_pos,  current_ship_tile_type)

func add_tile(tile_pos, tile_type):
  if(!validate_tile(tile_pos, tile_type)):
    print("Invalid tile, skipping...")
    return
  
  set_cellv(tile_pos,  tile_type)
  ship_tiles.append({"position": tile_pos, "type": tile_type})
  
  print(ship_tiles)
  
  
func validate_tile(tile_pos, tile_type) -> bool:
  
  if(ship_tiles.size() == 0):
    print("Placing first tile")
    return true
  
  var current_tile = current_tile()
  
  print("Validating Tiles.... Current Tile: ", current_tile.position, " New Tile: ", tile_pos)

  if(is_tile_spot_used(tile_pos)):
    print("Tile already placed in that spot")
    return false

  if(!tile_adjacent(tile_pos)):
    print("Tile must be adjacent to current tile")
    return false

  if(get_position_of_next_tile(current_tile.position, current_tile.type) != tile_pos):
    print("The next tile must be placed at ", get_position_of_next_tile(current_tile.position, current_tile.type))
    return false

  if(points_to_existing_tile(tile_pos, tile_type)):
    print("Tile must NOT point to an existing tile")
    return false
  return true
  
func is_tile_spot_used(tile_pos):
  for tile in ship_tiles:
    if tile.position == tile_pos:
      return true

func tile_adjacent(tile_pos) -> bool:
  var current_tile_pos = current_tile().position
  # Check if tile is on the same row
  if(current_tile_pos.y == tile_pos.y):
    # Check if the tile is to the left or right of the current tile
    if(current_tile_pos.x - 1 == tile_pos.x || current_tile_pos.x == tile_pos.x - 1):
      return true
    else:
      print("Tile is on the same row but not adjacent")
  # Check if the tile is one row above or below the current tile
  elif(current_tile_pos.y == tile_pos.y + 1 || current_tile_pos.y == tile_pos.y - 1):
    # Check if the tile is on the same column(s)
    # Note because it is a hexigon everything is offset which changes for even and odd rows
    # The following is based on the position of the **CURRENT TILE** not the new tile
    
    # So for even rows
    # if the x's are equal then it is bottom/top right of the current tile
    # if the new tile's x is x + 1 then it is bottom/top left of the current tile
    if(is_even(current_tile_pos.y)):
      if (current_tile_pos.x == tile_pos.x || current_tile_pos.x == tile_pos.x + 1):
        return true
      else:
        print("Tile within one row but not adjacent (Even Row)")
    
    # So for odd rows
    # if the new tile's x is x - 1 then it is bottom/top right of the current tile
    # if the x's are equal then it is bottom/top left of the current tile
    if(is_odd(current_tile_pos.y)):
      if(current_tile_pos.x == tile_pos.x - 1 || current_tile_pos.x == tile_pos.x):
        return true
      else:
        print("Tile within one row but not adjacent  (Odd Row)")
  else:
    print("Tile is not adjacent (not on same row or one row above/below)")
  return false


# Returns true if the tile is pointing to an existing tile
func points_to_existing_tile(tile_pos: Vector2, tile_type: int) -> bool:
  var tile_to_check = get_position_of_next_tile(tile_pos, tile_type)
  print("Checking tile ", tile_to_check)
  if(ship_tiles.has(tile_to_check)):
    print("Tile is pointing to an existing tile: ", tile_to_check)
    return true
  return false
  
func get_position_of_next_tile(tile_pos: Vector2, tile_type: int) -> Vector2:
  match (tile_type):
    UkatoaUtils.ship_tile_types.west:
      return get_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.east:
      return get_right_tile(tile_pos)
    UkatoaUtils.ship_tile_types.north_west:
      return get_top_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.north_east:
      return get_top_right_tile(tile_pos)
    UkatoaUtils.ship_tile_types.south_west:
      return get_bottom_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.south_east:
      return get_bottom_right_tile(tile_pos)
  push_error("Invliad tile position or type") 
  get_tree().quit()
  return Vector2(0,0)

# Utility Fu  nctions
func current_tile():
  return ship_tiles[ship_tiles.size() - 1]
  
func is_even(x: int):
    return x % 2 == 0

func is_odd(x: int):
    return x % 2 != 0

# Returns the left tile of the given tile
func get_left_tile(tile_pos):
  var y = tile_pos.y
  var x = tile_pos.x - 1
  return Vector2(x, y)

# Returns the right tile of the given tile
func get_right_tile(tile_pos):
  var y = tile_pos.y
  var x = tile_pos.x + 1
  return Vector2(x, y)

# Returns the top left tile of the given tile
func get_top_left_tile(tile_pos):
  var y = tile_pos.y - 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x - 1
  else:
    x = tile_pos.x
  return Vector2(x, y)


# Returns the top right tile of the given tile
func get_top_right_tile(tile_pos):
  var y = tile_pos.y - 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x
  else:
    x = tile_pos.x + 1
  return Vector2(x, y)

# Returns the top left tile of the given tile
func get_bottom_left_tile(tile_pos):
  var y = tile_pos.y + 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x - 1
  else:
    x = tile_pos.x
  return Vector2(x, y)


# Returns the top right tile of the given tile
func get_bottom_right_tile(tile_pos):
  var y = tile_pos.y + 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x
  else:
    x = tile_pos.x + 1
  return Vector2(x, y)
```

-------------------------------------------------------

**Utility Class Used to determine which way the tile is pointing**

```go
  class_name UkatoaUtils


  # Dictionary of all the tile types
  # Should match up with the numbers on `ship-tiles.tres`
  const ship_tile_types:Dictionary={
    west=0,
    north_west=1,
    north_east=2,
    east=3,
    south_east=4,
    south_west=5,
  }

```

## Tile Adjacency Code

So the comments explain most of what is happening in the code below, but the goal of this method is just to take a new tile with position `tile_pos` and see if it is adjacent to the current tile with position `current_tile_pos`.

> Note: Again the offset is kinda tough to wrap your mind around and it took me a little trial and error to actually get this totally working. Also I think the `+`/`-` or `even`/`odd` logic may need to be swapped based on how you set up your `TileMap`.

```go
  func tile_adjacent(tile_pos) -> bool:
    var current_tile_pos = current_tile().position
    # Check if tile is on the same row
    if(current_tile_pos.y == tile_pos.y):
      # Check if the tile is to the left or right of the current tile
      if(current_tile_pos.x - 1 == tile_pos.x || current_tile_pos.x == tile_pos.x - 1):
        return true
      else:
        print("Tile is on the same row but not adjacent")
    # Check if the tile is one row above or below the current tile
    elif(current_tile_pos.y == tile_pos.y + 1 || current_tile_pos.y == tile_pos.y - 1):
      # Check if the tile is on the same column(s)
      # Note because it is a hexigon everything is offset which changes for even and odd rows
      # The following is based on the position of the **CURRENT TILE** not the new tile
      
      # So for even rows
      # if the x's are equal then it is bottom/top right of the current tile
      # if the new tile's x is x + 1 then it is bottom/top left of the current tile
      if(is_even(current_tile_pos.y)):
        if (current_tile_pos.x == tile_pos.x || current_tile_pos.x == tile_pos.x + 1):
          return true
        else:
          print("Tile within one row but not adjacent (Even Row)")
      
      # So for odd rows
      # if the new tile's x is x - 1 then it is bottom/top right of the current tile
      # if the x's are equal then it is bottom/top left of the current tile
      if(is_odd(current_tile_pos.y)):
        if(current_tile_pos.x == tile_pos.x - 1 || current_tile_pos.x == tile_pos.x):
          return true
        else:
          print("Tile within one row but not adjacent  (Odd Row)")
    else:
      print("Tile is not adjacent (not on same row or one row above/below)")
    return false
```

## Tile Direction Code

Below is all the code relevant to tile direction. Here we want to do 2 validations.

1. Find the valid location of the "next" tile based on where the previous tile was pointing `get_position_of_next_tile`

2. Make sure the new tile is not pointing to any existing tiles `points_to_existing_tile`

```go

# Returns true if the tile is pointing to an existing tile
func points_to_existing_tile(tile_pos: Vector2, tile_type: int) -> bool:
  var tile_to_check = get_position_of_next_tile(tile_pos, tile_type)
  print("Checking tile ", tile_to_check)
  # `ship_tiles` contain all of the ship tiles, so we are making sure the position of the "next" tile is not included in the list of existing `ship_tiles`
  if(ship_tiles.has(tile_to_check)):
    print("Tile is pointing to an existing tile: ", tile_to_check)
    return true
  return false
  
func get_position_of_next_tile(tile_pos: Vector2, tile_type: int) -> Vector2:
  match (tile_type):
    UkatoaUtils.ship_tile_types.west:
      return get_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.east:
      return get_right_tile(tile_pos)
    UkatoaUtils.ship_tile_types.north_west:
      return get_top_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.north_east:
      return get_top_right_tile(tile_pos)
    UkatoaUtils.ship_tile_types.south_west:
      return get_bottom_left_tile(tile_pos)
    UkatoaUtils.ship_tile_types.south_east:
      return get_bottom_right_tile(tile_pos)
  push_error("Invliad tile position or type") 
  get_tree().quit()
  return Vector2(0,0)

# Utility Functions
func current_tile():
  return ship_tiles[ship_tiles.size() - 1]
  
func is_even(x: int):
    return x % 2 == 0

func is_odd(x: int):
    return x % 2 != 0

# Returns the left tile of the given tile
func get_left_tile(tile_pos):
  var y = tile_pos.y
  var x = tile_pos.x - 1
  return Vector2(x, y)

# Returns the right tile of the given tile
func get_right_tile(tile_pos):
  var y = tile_pos.y
  var x = tile_pos.x + 1
  return Vector2(x, y)

# Returns the top left tile of the given tile
func get_top_left_tile(tile_pos):
  var y = tile_pos.y - 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x - 1
  else:
    x = tile_pos.x
  return Vector2(x, y)


# Returns the top right tile of the given tile
func get_top_right_tile(tile_pos):
  var y = tile_pos.y - 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x
  else:
    x = tile_pos.x + 1
  return Vector2(x, y)

# Returns the top left tile of the given tile
func get_bottom_left_tile(tile_pos):
  var y = tile_pos.y + 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x - 1
  else:
    x = tile_pos.x
  return Vector2(x, y)


# Returns the top right tile of the given tile
func get_bottom_right_tile(tile_pos):
  var y = tile_pos.y + 1
  var x
  if (is_even(tile_pos.y)):
    x = tile_pos.x
  else:
    x = tile_pos.x + 1
  return Vector2(x, y)
```

## Conclusion

Hopefully this helps someone in the future (maybe me ha ha). I know I did not go super indpeth into the methods but I think most of them are pretty self explanatorily and really just there cause I thought that the adjacency logic was annoying to figure out but seems like someone that a lot of folks could use.
