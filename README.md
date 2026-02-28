# Waka Lua API Documentation

A comprehensive Lua scripting API for Waka CS2 cheat. This documentation covers all available functions, constants, and examples.

## Table of Contents
- [Getting Started](#getting-started)
- [UI System](#ui-system)
- [Client Functions](#client-functions)
- [Engine Functions](#engine-functions)
- [Entity Functions](#entity-functions)
- [Pawn Functions](#pawn-functions)
- [Controller Functions](#controller-functions)
- [Render Functions](#render-functions)
- [Input Functions](#input-functions)
- [Command Functions](#cmd-functions)
- [Math Functions](#math-functions)
- [Constants](#constants)
- [Callbacks](#callbacks)
- [Complete Examples](#complete-examples)

## Getting Started

### Script Structure
```lua
-- Basic script structure
function OnPaint()
    -- Called every frame for rendering
end

function OnPresent()
    -- Called after Present
end

function OnCreateMove()
    -- Called in CreateMove for command manipulation
end

-- Print to script console
print("Hello from Lua!")
client.log("Debug message")
client.notify("User notification")
```

### Script Location
Place your `.lua` scripts in: `C:\waka\lua\`

## UI System

### ui.checkbox(id, label, default_value)
Creates or gets a checkbox value.

```lua
-- Example: Simple checkbox
local esp_enabled = ui.checkbox("esp_enabled", "Enable ESP", true)

if esp_enabled then
    -- Do ESP stuff
end

-- Example: Get value later
local current_value = ui.get("esp_enabled")
```

### ui.slider_int(id, label, default, min, max)
Integer slider.

```lua
-- Example: Integer slider for ESP distance
local esp_distance = ui.slider_int("esp_dist", "ESP Distance", 1000, 100, 5000)

-- Use the value
client.log("Current distance: " .. esp_distance)
```

### ui.slider_float(id, label, default, min, max)
Float slider.

```lua
-- Example: Float slider for opacity
local opacity = ui.slider_float("opacity", "ESP Opacity", 0.8, 0.0, 1.0)

-- Use with render
local color = render.color(255, 255, 255, opacity * 255)
```

### ui.combo(id, label, default_index, items)
Combo box for single selection.

```lua
-- Example: ESP type selection
local esp_types = {"Box", "Health", "Name", "All"}
local esp_type = ui.combo("esp_type", "ESP Type", 0, esp_types)

if esp_type == 0 then
    -- Box ESP
elseif esp_type == 1 then
    -- Health ESP
end
```

### ui.multi_combo(id, label, default_mask, items)
Multi-select combo box using bitmask.

```lua
-- Example: Multi-select features
local features = {"Wallhack", "Radar", "Snaplines", "Distance"}
local selected = ui.multi_combo("features", "Features", 0, features)

-- Check if specific feature is selected (bit 0 = wallhack)
if selected & 1 ~= 0 then
    -- Wallhack enabled
end

-- Check bit 1 (radar)
if selected & 2 ~= 0 then
    -- Radar enabled
end
```

### ui.color(id, label, r, g, b, a)
Color picker.

```lua
-- Example: Color picker for ESP
local r, g, b, a = ui.color("esp_color", "ESP Color", 255, 0, 0, 255)

-- Use with render
render.rect(x1, y1, x2, y2, r, g, b, a)
```

### ui.button(id, label)
Button that returns true when clicked.

```lua
-- Example: Refresh button
if ui.button("refresh_btn", "Refresh Script") then
    client.log("Refreshing...")
    -- Reload logic
end

-- Button stays pressed only one frame
```

### ui.keybind(id, label, default_key)
Keybind selector.

```lua
-- Example: Triggerbot key
local trigger_key = ui.keybind("trigger_key", "Triggerbot Key", keys.X)

-- Check if key is currently held
if ui.is_keybind_active("trigger_key") then
    -- Triggerbot active
end

-- Get raw key value
local key = ui.get("trigger_key")
```

### ui.label(id, text)
Static text label.

```lua
-- Example: Status label
local health = pawn.get_health(entity.get_local_player())
ui.label("health_label", "Health: " .. health)

-- Update later
ui.label("health_label", "Health: " .. new_health)
```

### ui.get(id)
Get current value of any UI element.

```lua
-- Example: Get multiple values
local esp = ui.get("esp_enabled")
local dist = ui.get("esp_dist")
local r, g, b, a = ui.get("esp_color")
```

### ui.set(id, value, ...)
Set value of UI element.

```lua
-- Example: Set values programmatically
ui.set("esp_enabled", true)
ui.set("esp_dist", 2000)
ui.set("esp_color", 255, 0, 0, 255)  -- RGB for color
```

### ui.is_keybind_active(id)
Check if keybind is currently pressed.

```lua
-- Example: Check if trigger key is held
if ui.is_keybind_active("trigger_key") then
    -- Key is being held down
    cmd.add_button(buttons.IN_ATTACK)
end
```

## Client Functions

### client.log(message)
Add message to script console.

```lua
client.log("Script initialized")
client.log("Player health: " .. health)
```

### client.notify(message)
Add notification to console (highlighted).

```lua
client.notify("Enemy spotted!")
client.notify("Triggerbot fired")
```

### client.get_username()
Get Windows username.

```lua
local username = client.get_username()
client.log("Hello, " .. username)
```

### client.get_timestamp()
Get current time string (HH:MM:SS).

```lua
local time = client.get_timestamp()
ui.label("time_label", "Time: " .. time)
```

### client.get_frametime()
Get delta time between frames.

```lua
local dt = client.get_frametime()
local speed = 100 * dt  -- Frame-independent movement
```

### client.get_fps()
Get current FPS.

```lua
local fps = client.get_fps()
ui.label("fps_label", "FPS: " .. fps)
```

### client.get_realtime()
Get time since script started.

```lua
local time = client.get_realtime()
if time > 60 then
    client.log("Script running for over a minute")
end
```

### client.is_in_game()
Check if currently in a game.

```lua
if client.is_in_game() then
    -- Do in-game stuff
else
    -- In menu
end
```

### client.is_connected()
Check if connected to a server.

```lua
if client.is_connected() then
    local map = client.get_map_name()
    client.log("Connected to: " .. map)
end
```

### client.get_map_name()
Get current map name.

```lua
local map = client.get_map_name()
ui.label("map_label", "Map: " .. map)
```

### client.get_screen_size()
Get screen dimensions.

```lua
local width, height = client.get_screen_size()
local center_x = width / 2
local center_y = height / 2
```

## Engine Functions

### engine.get_local_player_index()
Get local player entity index.

```lua
local index = engine.get_local_player_index()
client.log("Local player index: " .. index)
```

### engine.get_max_clients()
Get maximum number of clients.

```lua
local max_players = engine.get_max_clients()
for i = 1, max_players do
    -- Iterate through players
end
```

### engine.get_view_angles()
Get current view angles.

```lua
local pitch, yaw, roll = engine.get_view_angles()
client.log(string.format("Angles: %.2f %.2f", pitch, yaw))
```

### engine.set_view_angles(pitch, yaw, roll)
Set view angles.

```lua
-- Set angles (aim at something)
engine.set_view_angles(45.0, 90.0, 0.0)
```

## Entity Functions

### entity.get_local_player()
Get local player pawn entity.

```lua
local local_player = entity.get_local_player()
if local_player then
    local health = pawn.get_health(local_player)
    client.log("Local health: " .. health)
end
```

### entity.get_local_controller()
Get local player controller.

```lua
local controller = entity.get_local_controller()
if controller then
    local name = controller.get_name(controller)
    client.log("Player name: " .. name)
end
```

### entity.get_entity_by_index(index)
Get entity by index.

```lua
for i = 1, 64 do
    local ent = entity.get_entity_by_index(i)
    if ent and entity.is_player_pawn(ent) then
        -- Process player
    end
end
```

### entity.get_highest_index()
Get highest entity index.

```lua
local max_idx = entity.get_highest_index()
for i = 1, max_idx do
    -- Loop through all entities
end
```

### entity.is_player_controller(entity)
Check if entity is a player controller.

```lua
local ent = entity.get_entity_by_index(i)
if entity.is_player_controller(ent) then
    -- It's a controller
end
```

### entity.is_player_pawn(entity)
Check if entity is a player pawn.

```lua
local ent = entity.get_entity_by_index(i)
if entity.is_player_pawn(ent) then
    -- It's a player model
end
```

### entity.get_class_name(entity)
Get entity class name.

```lua
local ent = entity.get_entity_by_index(i)
local class = entity.get_class_name(ent)
if class == "C_CSPlayerPawn" then
    -- It's a player
end
```

## Pawn Functions

### pawn.get_health(pawn)
Get pawn health.

```lua
local health = pawn.get_health(some_pawn)
if health > 0 then
    -- Player is alive
end
```

### pawn.get_max_health(pawn)
Get maximum health.

```lua
local health = pawn.get_health(player)
local max_hp = pawn.get_max_health(player)
local health_percent = (health / max_hp) * 100
```

### pawn.get_armor(pawn)
Get armor value.

```lua
local armor = pawn.get_armor(player)
if armor > 0 then
    -- Has armor
end
```

### pawn.get_team(pawn)
Get team number.

```lua
local team = pawn.get_team(player)
if team == 2 then
    -- Terrorist
elseif team == 3 then
    -- Counter-Terrorist
end
```

### pawn.get_origin(pawn)
Get world position.

```lua
local x, y, z = pawn.get_origin(player)
local screen_x, screen_y, visible = render.world_to_screen(x, y, z)
if visible then
    -- Draw at screen position
end
```

### pawn.get_eye_pos(pawn)
Get eye/camera position.

```lua
local ex, ey, ez = pawn.get_eye_pos(local_player)
```

### pawn.get_velocity(pawn)
Get velocity vector.

```lua
local vx, vy, vz = pawn.get_velocity(player)
local speed = waka_math.vec_length(vx, vy, vz)
```

### pawn.get_flags(pawn)
Get player flags.

```lua
local flags = pawn.get_flags(player)
if flags & flags.FL_ONGROUND ~= 0 then
    -- Player is on ground
end
```

### pawn.is_alive(pawn)
Check if pawn is alive.

```lua
if pawn.is_alive(player) then
    -- Player is alive
end
```

### pawn.is_scoped(pawn)
Check if player is scoped.

```lua
if pawn.is_scoped(player) then
    -- Player is scoped in
end
```

### pawn.is_defusing(pawn)
Check if player is defusing.

```lua
if pawn.is_defusing(player) then
    -- Player is defusing bomb
end
```

### pawn.is_on_ground(pawn)
Check if player is on ground.

```lua
if not pawn.is_on_ground(player) then
    -- Player is in air
end
```

### pawn.get_bone_position(pawn, bone_index)
Get bone position by index (0-27).

```lua
-- Head bone (bone 6)
local hx, hy, hz = pawn.get_bone_position(player, 6)

-- Chest bone (bone 5)
local cx, cy, cz = pawn.get_bone_position(player, 5)
```

### pawn.get_shots_fired(pawn)
Get shots fired count.

```lua
local shots = pawn.get_shots_fired(player)
if shots > 0 then
    -- Player is shooting
end
```

### pawn.get_active_weapon_name(pawn)
Get current weapon name.

```lua
local weapon = pawn.get_active_weapon_name(player)
client.log("Weapon: " .. weapon)
```

### pawn.get_active_weapon_id(pawn)
Get current weapon ID.

```lua
local weapon_id = pawn.get_active_weapon_id(player)
if weapon_id == 7 then  -- AK-47
    -- Do AK specific stuff
end
```

### pawn.get_active_weapon_clip(pawn)
Get current weapon clip ammo.

```lua
local ammo = pawn.get_active_weapon_clip(player)
ui.label("ammo_label", "Ammo: " .. ammo)
```

## Controller Functions

### controller.get_name(controller)
Get player name.

```lua
local name = controller.get_name(ctrl)
client.log("Player: " .. name)
```

### controller.get_pawn(controller)
Get player's pawn from controller.

```lua
local pawn = controller.get_pawn(ctrl)
if pawn and pawn.is_alive(pawn) then
    -- Player is alive
end
```

### controller.get_ping(controller)
Get player ping.

```lua
local ping = controller.get_ping(ctrl)
if ping > 100 then
    -- High ping
end
```

### controller.get_steam_id(controller)
Get Steam ID as string.

```lua
local steamid = controller.get_steam_id(ctrl)
client.log("SteamID: " .. steamid)
```

### controller.is_alive(controller)
Check if controller's pawn is alive.

```lua
if controller.is_alive(ctrl) then
    -- Player is alive
end
```

## Render Functions

### render.line(x1, y1, x2, y2, r, g, b, a, thickness)
Draw a line.

```lua
-- Draw red line from (100,100) to (200,200)
render.line(100, 100, 200, 200, 255, 0, 0, 255, 2.0)
```

### render.rect(x1, y1, x2, y2, r, g, b, a, rounding, thickness)
Draw rectangle outline.

```lua
-- Draw white rectangle with rounded corners
render.rect(100, 100, 300, 200, 255, 255, 255, 255, 5.0, 1.5)
```

### render.rect_filled(x1, y1, x2, y2, r, g, b, a, rounding)
Draw filled rectangle.

```lua
-- Draw semi-transparent black background
render.rect_filled(100, 100, 300, 200, 0, 0, 0, 128, 5.0)
```

### render.circle(x, y, radius, r, g, b, a, segments, thickness)
Draw circle outline.

```lua
-- Draw circle at crosshair
local w, h = client.get_screen_size()
render.circle(w/2, h/2, 50, 255, 255, 255, 255, 32, 2.0)
```

### render.circle_filled(x, y, radius, r, g, b, a, segments)
Draw filled circle.

```lua
-- Draw filled circle
render.circle_filled(100, 100, 25, 255, 0, 0, 255, 32)
```

### render.text(x, y, text, r, g, b, a, font_size, center_x, center_y, shadow)
Draw text.

```lua
-- Draw centered text with shadow
render.text(400, 300, "Hello World", 255, 255, 255, 255, 16, true, true, true)

-- Draw text without centering
render.text(10, 10, "FPS: " .. client.get_fps(), 0, 255, 0, 255)
```

### render.text_size(text, font_size)
Get text dimensions.

```lua
local w, h = render.text_size("Sample Text", 16)
client.log("Text width: " .. w .. " height: " .. h)
```

### render.triangle(x1, y1, x2, y2, x3, y3, r, g, b, a, thickness)
Draw triangle outline.

```lua
-- Draw triangle
render.triangle(100, 100, 150, 50, 200, 100, 255, 255, 255, 255, 2.0)
```

### render.triangle_filled(x1, y1, x2, y2, x3, y3, r, g, b, a)
Draw filled triangle.

```lua
-- Draw filled triangle
render.triangle_filled(100, 100, 150, 50, 200, 100, 255, 0, 0, 128)
```

### render.world_to_screen(x, y, z)
Convert 3D world position to 2D screen position.

```lua
local sx, sy, visible = render.world_to_screen(ent_x, ent_y, ent_z)
if visible then
    -- Draw at screen position
    render.rect(sx - 10, sy - 10, sx + 10, sy + 10, 255, 255, 255, 255)
end
```

### render.color(r, g, b, a)
Create packed color for ImGui.

```lua
local col = render.color(255, 0, 0, 255)
-- Use with some ImGui functions
```

## Input Functions

### input.is_key_down(key)
Check if key is currently held.

```lua
if input.is_key_down(keys.X) then
    -- X key is held
end
```

### input.is_key_pressed(key)
Check if key was pressed this frame.

```lua
if input.is_key_pressed(keys.SPACE) then
    -- Space was just pressed
end
```

### input.get_cursor_pos()
Get cursor position.

```lua
local mx, my = input.get_cursor_pos()
client.log("Mouse at: " .. mx .. ", " .. my)
```

### input.is_menu_open()
Check if cheat menu is open.

```lua
if not input.is_menu_open() then
    -- Only do stuff when menu is closed
end
```

## Command Functions

### cmd.get_buttons()
Get current button state.

```lua
local buttons = cmd.get_buttons()
if buttons & buttons.IN_ATTACK ~= 0 then
    -- Attacking
end
```

### cmd.set_buttons(buttons)
Set all buttons.

```lua
-- Force attack
cmd.set_buttons(buttons.IN_ATTACK)
```

### cmd.add_button(button)
Add button to current state.

```lua
-- Force jump
cmd.add_button(buttons.IN_JUMP)
```

### cmd.remove_button(button)
Remove button from current state.

```lua
-- Prevent ducking
cmd.remove_button(buttons.IN_DUCK)
```

## Math Functions

### waka_math.vec_length(x, y, z)
Calculate vector length.

```lua
local vx, vy, vz = pawn.get_velocity(player)
local speed = waka_math.vec_length(vx, vy, vz)
```

### waka_math.vec_distance(x1, y1, z1, x2, y2, z2)
Calculate distance between two points.

```lua
local px, py, pz = pawn.get_origin(player)
local lx, ly, lz = pawn.get_origin(local_player)
local dist = waka_math.vec_distance(px, py, pz, lx, ly, lz)
```

### waka_math.normalize_angle(angle)
Normalize angle to -180 to 180 range.

```lua
local angle = 270
local norm = waka_math.normalize_angle(angle)  -- Returns -90
```

## Constants

### Button Constants (buttons)
```lua
buttons.IN_ATTACK       -- Attack/shoot
buttons.IN_JUMP         -- Jump
buttons.IN_DUCK         -- Crouch
buttons.IN_FORWARD      -- Move forward
buttons.IN_BACK         -- Move back
buttons.IN_MOVELEFT     -- Move left
buttons.IN_MOVERIGHT    -- Move right
buttons.IN_USE          -- Use key
buttons.IN_RELOAD       -- Reload
buttons.IN_ATTACK2      -- Secondary attack
buttons.IN_SPEED        -- Speed/walk
buttons.IN_SCOPE        -- Scope
```

### Flag Constants (flags)
```lua
flags.FL_ONGROUND       -- Player on ground
flags.FL_DUCKING        -- Player ducking
flags.FL_INWATER        -- Player in water
```

### Key Constants (keys)
```lua
keys.LBUTTON    -- Left mouse
keys.RBUTTON    -- Right mouse
keys.MBUTTON    -- Middle mouse
keys.MOUSE4     -- Mouse 4
keys.MOUSE5     -- Mouse 5
keys.SPACE      -- Spacebar
keys.SHIFT      -- Shift
keys.CTRL       -- Control
keys.ALT        -- Alt
keys.TAB        -- Tab
keys.ESCAPE     -- Escape

-- Number keys (0-9)
keys["0"], keys["1"], ..., keys["9"]

-- Letter keys (A-Z)
keys["A"], keys["B"], ..., keys["Z"]

-- Function keys
keys.F1, keys.F2, ..., keys.F12
```

## Callbacks

### OnPaint()
Called every frame for rendering. Use for ESP and visuals.

```lua
function OnPaint()
    if not client.is_in_game() then return end
    
    local local_player = entity.get_local_player()
    if not local_player then return end
    
    -- Draw crosshair
    local w, h = client.get_screen_size()
    render.line(w/2 - 10, h/2, w/2 + 10, h/2, 255, 255, 255, 255)
    render.line(w/2, h/2 - 10, w/2, h/2 + 10, 255, 255, 255, 255)
end
```

### OnPresent()
Called after Present. Similar to OnPaint but different timing.

```lua
function OnPresent()
    -- Additional rendering
end
```

### OnCreateMove()
Called in CreateMove for command manipulation.

```lua
function OnCreateMove()
    if not client.is_in_game() then return end
    
    -- Bunny hop
    if ui.is_keybind_active("bhop_key") then
        local player = entity.get_local_player()
        if player then
            local flags = pawn.get_flags(player)
            if flags & flags.FL_ONGROUND ~= 0 then
                cmd.add_button(buttons.IN_JUMP)
            end
        end
    end
end
```

## Complete Examples

### Basic ESP Script
```lua
-- Basic ESP example with UI
local esp_enabled = ui.checkbox("esp_enabled", "Enable ESP", true)
local esp_box = ui.checkbox("esp_box", "Draw Box", true)
local esp_health = ui.checkbox("esp_health", "Draw Health", true)
local esp_distance = ui.slider_int("esp_dist", "Max Distance", 1000, 100, 5000)
local esp_color = ui.color("esp_color", "ESP Color", 255, 255, 255, 255)

function OnPaint()
    if not esp_enabled then return end
    if not client.is_in_game() then return end
    
    local local_player = entity.get_local_player()
    if not local_player then return end
    
    local lx, ly, lz = pawn.get_origin(local_player)
    local r, g, b, a = ui.get("esp_color")
    
    -- Loop through players
    for i = 1, 64 do
        local ent = entity.get_entity_by_index(i)
        if ent and entity.is_player_pawn(ent) and ent ~= local_player then
            if pawn.is_alive(ent) then
                local ex, ey, ez = pawn.get_origin(ent)
                local dist = waka_math.vec_distance(lx, ly, lz, ex, ey, ez)
                
                if dist <= esp_distance then
                    local sx, sy, visible = render.world_to_screen(ex, ey, ez)
                    if visible then
                        -- Draw box
                        if esp_box then
                            render.rect(sx - 15, sy - 30, sx + 15, sy, r, g, b, a)
                        end
                        
                        -- Draw health
                        if esp_health then
                            local health = pawn.get_health(ent)
                            render.text(sx, sy - 40, tostring(health), 0, 255, 0, 255, 12, true, false)
                        end
                    end
                end
            end
        end
    end
end
```

### Triggerbot Script
```lua
-- Advanced triggerbot with keybind and delay
local trigger_enabled = ui.checkbox("trigger_enabled", "Enable Triggerbot", true)
local trigger_key = ui.keybind("trigger_key", "Trigger Key", keys.X)
local trigger_delay = ui.slider_int("trigger_delay", "Delay (ms)", 50, 0, 500)
local trigger_hitgroup = ui.combo("trigger_hitgroup", "Hit Group", 0, {"Any", "Head", "Chest", "Stomach", "Arms", "Legs"})

local last_shot_time = 0

function OnCreateMove()
    if not trigger_enabled then return end
    if not ui.is_keybind_active("trigger_key") then return end
    if not client.is_in_game() then return end
    
    local local_player = entity.get_local_player()
    if not local_player or not pawn.is_alive(local_player) then return end
    
    local current_time = client.get_realtime()
    if (current_time - last_shot_time) * 1000 < trigger_delay then
        return
    end
    
    -- Get crosshair entity
    -- Note: You'd need to implement crosshair entity detection
    -- This is simplified example
    
    local target = get_crosshair_entity()  -- Hypothetical function
    if target and pawn.is_alive(target) then
        if pawn.get_team(target) ~= pawn.get_team(local_player) then
            -- Check hitgroup if needed
            cmd.add_button(buttons.IN_ATTACK)
            last_shot_time = current_time
        end
    end
end
```

### Bunny Hop Script
```lua
-- Bunny hop with auto-strafe
local bhop_enabled = ui.checkbox("bhop_enabled", "Enable Bunny Hop", true)
local bhop_key = ui.keybind("bhop_key", "Bhop Key", keys.SPACE)
local auto_strafe = ui.checkbox("auto_strafe", "Auto Strafe", true)

function OnCreateMove()
    if not bhop_enabled then return end
    if not ui.is_keybind_active("bhop_key") then return end
    if not client.is_in_game() then return end
    
    local local_player = entity.get_local_player()
    if not local_player or not pawn.is_alive(local_player) then return end
    
    local flags = pawn.get_flags(local_player)
    
    -- Bunny hop
    if flags & flags.FL_ONGROUND ~= 0 then
        cmd.add_button(buttons.IN_JUMP)
    else
        cmd.remove_button(buttons.IN_JUMP)
    end
    
    -- Auto strafe
    if auto_strafe and flags & flags.FL_ONGROUND == 0 then
        local vx, vy, vz = pawn.get_velocity(local_player)
        local speed = waka_math.vec_length(vx, vy, 0)
        
        if speed > 10 then
            local pitch, yaw, roll = engine.get_view_angles()
            
            -- Simple strafe logic
            local side = (math.random() > 0.5) and buttons.IN_MOVELEFT or buttons.IN_MOVERIGHT
            
            if side == buttons.IN_MOVELEFT then
                cmd.add_button(buttons.IN_MOVELEFT)
                cmd.remove_button(buttons.IN_MOVERIGHT)
                engine.set_view_angles(pitch, yaw - 20, 0)
            else
                cmd.add_button(buttons.IN_MOVERIGHT)
                cmd.remove_button(buttons.IN_MOVELEFT)
                engine.set_view_angles(pitch, yaw + 20, 0)
            end
        end
    end
end
```

### Radar Hack Script
```lua
-- Simple radar hack
local radar_enabled = ui.checkbox("radar_enabled", "Enable Radar", true)
local radar_size = ui.slider_int("radar_size", "Radar Size", 200, 100, 400)
local radar_zoom = ui.slider_float("radar_zoom", "Zoom", 2.0, 1.0, 5.0)

function OnPaint()
    if not radar_enabled then return end
    if not client.is_in_game() then return end
    
    local local_player = entity.get_local_player()
    if not local_player then return end
    
    local lx, ly, lz = pawn.get_origin(local_player)
    local lpitch, lyaw, lroll = engine.get_view_angles()
    
    -- Radar position (top-left corner)
    local radar_x = 10
    local radar_y = 10
    local size = radar_size
    
    -- Draw radar background
    render.rect_filled(radar_x, radar_y, radar_x + size, radar_y + size, 0, 0, 0, 128, 5)
    render.rect(radar_x, radar_y, radar_x + size, radar_y + size, 255, 255, 255, 255, 5, 1)
    
    -- Draw center lines
    local center_x = radar_x + size / 2
    local center_y = radar_y + size / 2
    render.line(center_x, radar_y, center_x, radar_y + size, 255, 255, 255, 64)
    render.line(radar_x, center_y, radar_x + size, center_y, 255, 255, 255, 64)
    
    -- Draw local player at center
    render.circle_filled(center_x, center_y, 5, 0, 255, 0, 255)
    
    -- Draw enemies
    for i = 1, 64 do
        local ent = entity.get_entity_by_index(i)
        if ent and entity.is_player_pawn(ent) and ent ~= local_player then
            if pawn.is_alive(ent) then
                local ex, ey, ez = pawn.get_origin(ent)
                local team = pawn.get_team(ent)
                
                -- Calculate relative position
                local dx = ex - lx
                local dy = ey - ly
                
                -- Rotate by view angles (simplified)
                local angle_rad = math.rad(lyaw)
                local rotated_x = dx * math.cos(angle_rad) - dy * math.sin(angle_rad)
                local rotated_y = dx * math.sin(angle_rad) + dy * math.cos(angle_rad)
                
                -- Scale and clamp to radar
                local scale = (size / 2) / radar_zoom
                local radar_dot_x = center_x + rotated_x * scale
                local radar_dot_y = center_y - rotated_y * scale  -- Y is inverted
                
                -- Clamp to radar bounds
                radar_dot_x = math.max(radar_x + 2, math.min(radar_x + size - 2, radar_dot_x))
                radar_dot_y = math.max(radar_y + 2, math.min(radar_y + size - 2, radar_dot_y))
                
                -- Draw enemy dot
                local color = (team == 2) and {255, 0, 0} or {0, 0, 255}
                render.circle_filled(radar_dot_x, radar_dot_y, 4, color[1], color[2], color[3], 255)
            end
        end
    end
end
```
