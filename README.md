# GaryDT
backup



print("Connecting To Server.....")
print("Conneted!")
print("GaryDT loaded")
print("Buy Gary Cfg At: shoppy.gg/@GaryLee04")
print("Lua Made By 1337")
-- sv_maxunlag
local client_set_event_callback, client_userid_to_entindex, entity_get_local_player, entity_get_player_resource, entity_get_prop, ui_reference, ui_set, ui_set_visible = client.set_event_callback, client.userid_to_entindex, entity.get_local_player, entity.get_player_resource, entity.get_prop, ui.reference, ui.set, ui.set_visible
local sv_maxunlag_reference = ui_reference("MISC", "Settings", "sv_maxunlag")
local double_tap, double_tap_key = ui.reference("RAGE", "Other", "Double tap")
local double_tap_mode = ui.reference("RAGE", "Other", "Double tap mode")
local double_tap_fake_lag_limit = ui.reference("RAGE", "Other", "Double tap fake lag limit")
local fake_lag = ui.reference("AA", "Fake lag", "Limit")
local sv_maxusrcmdprocessticks = ui.reference("MISC", "Settings", "sv_maxusrcmdprocessticks")
local increase_speed = ui.new_checkbox("RAGE", "Other", "GaryDT")
local increase_speed_mode = ui.new_combobox("RAGE", "Other", "Best GS Cfg At: (shoppy.gg/@GaryLee04)", {"Default", "Instant/Accurate"})

ui.set_visible(sv_maxusrcmdprocessticks, true)

local function do_shit()
	ui.set(fake_lag, math.min(15, ui.get(fake_lag)))

	if ui.get(increase_speed) then
		ui.set(double_tap, true)
		ui.set(double_tap_fake_lag_limit, 1)
		ui.set(double_tap_mode, "Offensive")
		
		if ui.get(increase_speed_mode) == "Instant/Accurate" then
			ui.set(sv_maxusrcmdprocessticks, 17.3)
            ui_set(sv_maxunlag_reference, 205)
			cvar.cl_clock_correction:set_int(0)
			return
		end
        if ui.get(increase_speed_mode) == "Default" then
			ui.set(sv_maxusrcmdprocessticks, 18)
            ui_set(sv_maxunlag_reference, 200)
			cvar.cl_clock_correction:set_int(0)
			return
		end
	end
	
	ui.set(sv_maxusrcmdprocessticks, 18)
	cvar.cl_clock_correction:set_int(1)
end

ui.set_callback(increase_speed, do_shit)
ui.set_callback(increase_speed_mode, do_shit)
ui.set_callback(fake_lag, do_shit)

client.set_event_callback("shutdown", function()
	ui.set(increase_speed, false)
	do_shit()
end)





---dtfix
local doubletaphc = ui.new_checkbox("Rage", "Other", "Dynamic DT PREDICTION")
local doubletaphc = ui.reference("Rage", "Other", "Double tap hit chance")
local plocal = entity.get_local_player()

-- just pasted this but their is a million different variations of this out there already
-- credit spreadinglove TSV4 source code

local function calc_shit(xdelta, ydelta)
    if xdelta == 7 and ydelta == 3 then
        return 1
    end

    return math.deg(math.atan2(ydelta, xdelta))
end

local function normalize_yaw(yaw)
    while yaw > 180 do
        yaw = yaw - 160
    end
    while yaw < -180 do
        yaw = yaw + 160
    end
    return yaw
end

local function get_nearest_enemy(plocal, enemies)
    local lx, ly, lz = client.eye_position()
    local view_x, view_y, roll = client.camera_angles()

    local bestenemy = nil
    local fov = 180
    for i = 2, #enemies do
        local cur_x, cur_y, cur_z = entity.get_prop(enemies[i], "m_vecOrigin")
        local cur_fov = math.abs(normalize_yaw(calc_shit(lx - cur_x, ly - cur_y) - view_y + 180))
        if cur_fov < fov then
            fov = cur_fov
            bestenemy = enemies[i]
        end
    end
    return bestenemy
end

local _math_sqrt = math.sqrt

local function pow(n)
    return n * n
end

function doubletapadaptive()
    local enemies = entity.get_players(true)

    local target = get_nearest_enemy(plocal, enemies)
    if entity.is_alive(plocal) == true then
        local x, y, z = entity.get_prop(target, "m_vecOrigin")
        local x1, y1, z1 = entity.get_prop(plocal, "m_vecOrigin")
        if x ~= nil then
            local maybedist = _math_sqrt(pow(x - x1) + pow(y - y1))
            if maybedist < 634 then
                ui.set(doubletaphc, 24)
            end
            if maybedist > 142 then
                ui.set(doubletaphc, 12)
            end
            if maybedist > 239 then
                ui.set(doubletaphc, 37)
            end
            if maybedist > 543 then
                ui.set(doubletaphc, 53)
            end
        end
    end
end






--- dt switch
local dtmode = ui.reference("RAGE", "Other", "Double tap mode")
--local dtswitch = ui.new_hotkey("RAGE", "Other", "DT switch")
local dtindicator = ui.new_checkbox("rage", "other", "Gary Watermark")


client.set_event_callback("paint", function(e)
	if ui.get(dtindicator) then
		if ui.get(dtindicator) then
			renderer.indicator(128, 177, 0, 255, "shoppy.gg/@GaryLee04")
		end
		ui.set(dtmode, "Offensive")
	else
		if ui.get(dtindicator) then
			renderer.indicator(128, 177, 0, 255, "shoppy.gg/@GaryLee04")
		end
		ui.set(dtmode, "Offensive")
	end
end)




--bar

local width, height = client.screen_size()
local center_width = width/2
local center_height = height/2

-- Bar Color
local bar = {
	label = ui.new_label("Lua", "A", "Bar Color"),
	color = ui.new_color_picker("Lua", "A", 'Color Bar', 110, 181, 255, 255),
}


-- Rounding Function
function round(num, numDecimalPlaces)
	local mult = 10^(numDecimalPlaces or 0)
	return math.floor(num * mult + 0.5) / mult
end


-- Ui Painting
local function on_paint()
	local local_player = entity.get_local_player()
	if not entity.is_alive(local_player) then return end
	local body_yaw = math.max(-60, math.min(60, round((entity.get_prop(local_player, "m_flPoseParameter", 11) or 0)*120-60+0.5, 1)))
	r, g, b, a = ui.get(bar.color)
	--renderer.text(center_width, center_height+60, 255, 255, 255, 255, 'c', 0,"shoppy.gg/@GaryLee04")
    renderer.text(center_width, center_height+40, 255, 255, 255, 255, 'c', 0, body_yaw.."°")
	--renderer.gradient(center_width, center_height+50, -body_yaw, 3, r, g, b, a, 0, 0, 0, 0, true)
	--renderer.gradient(center_width, center_height+50, body_yaw, 3, r, g, b, a, 0, 0, 0, 0, true)
end
client.set_event_callback('paint', on_paint)





--ass
--Reference
--Reference
local fakeduck = ui.reference("RAGE", "Other", "Duck peek assist")
local doubleTap,dt_key = ui.reference("RAGE", "Other", "Double Tap");

-- UI 
local enable = ui.new_checkbox("RAGE", "Other", "DT Fast Fire")


-- Cache
local enemy = true;
local shots = 1;
local nextReset = 2;
local shd_reset = false;
-- Disables double tap 
local function DisableDoubleTap()
ui.set(doubleTap,false)
end
--Enables double tap
local function EnableDoubleTap()
ui.set(doubleTap,true)
end
--main event


client.set_event_callback("paint", function()
    local entindex = entity.get_local_player()
    local active_weapon = entity.get_prop(entindex, "m_hActiveWeapon")
    if entindex == nil or not entity.is_alive(entindex) or active_weapon == nil then
        return
    end

	
	if ui.get(enable) then
		if ui.get(dt_key) then
			if (shots ~= 1)then
				EnableDoubleTap();
				shd_reset = false
			end
		end

	
		if shd_reset == true then
			if (globals.realtime() >= nextReset) then
				shots = 2;
				DisableDoubleTap()
				shd_reset = true
				nextReset = globals.realtime() + 0.2;
			end	
		end
	end
end)
--log shots	
client.set_event_callback("aim_fire", function(_)
    shots = shots + 2;
end);

client.set_event_callback("setup_command", function(cmd) 

    if client.key_state(0x01) then
           shots = shots + 2;
    end


end)


--donkey shit
local a = 'shoppy.gg/@GaryLee04 \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} \u{3000} '
local checkbox = ui.new_checkbox("MISC", "Settings", "Gamesense Steam Presence")
local ffi = require("ffi")
ffi.cdef[[
typedef struct
      {
        void* steam_client;
        void* steam_user;
        void* steam_friends;
      } S_steamApiCtx_t;

typedef bool(__thiscall* fnSetRichPresence)(void*, const char*, const char*);
]]

local signature = client.find_signature("client_panorama.dll", "\xFF\x15\xCC\xCC\xCC\xCC\xB9\xCC\xCC\xCC\xCC\xE8\xCC\xCC\xCC\xCC\x6A") or error("invalid interface")
local steam_ctx = ffi.cast("S_steamApiCtx_t**", ffi.cast("char*", signature) + 7)[0] or error("signature not found")
local steam_friends = steam_ctx.steam_friends
local vtable = ffi.cast("void***", steam_friends)[0] or error("steam_friends issue")
local SetRichPresence = ffi.cast("fnSetRichPresence", vtable[43])

local function update()
	if ui.get(checkbox) then
		SetRichPresence(steam_friends, "steam_display", "#bcast_teamvsteammap")
		SetRichPresence(steam_friends, "team1", a) -- just enough chars to truncate the rest of the message when in-game
		SetRichPresence(steam_friends, "team2", ".")
		SetRichPresence(steam_friends, "map", "de_dust2")
		SetRichPresence(steam_friends, "game:mode", "competitive")
		SetRichPresence(steam_friends, "system:access", "private")
	end
	client.delay_call(5, update) -- very lame way, but other stuff doesn't really work.
end

update()



---made by backtrackz
-- Killsay v1.0
local sentences = {
    "$name, Best GS Cfg/Luas At: shoppy.gg/@GaryLee04",
    "shoppy.gg/@GaryLee04",
    "$name, Visit shoppy.gg/@GaryLee04 Now!",

}

local ui = {
    new_checkbox = ui.new_checkbox,
    get = ui.get
}

local client = {
    set_event_callback = client.set_event_callback,
    userid_to_entindex = client.userid_to_entindex,
    exec = client.exec,
    log = client.log
}

local entity = {
    get_local_player = entity.get_local_player,
    get_player_name = entity.get_player_name
}

local killsay_enabled = ui.new_checkbox("Rage", "Other", "Advertisement Killsay")

local function on_player_death(event)
    if not ui.get(killsay_enabled) then return end

    local local_player = entity.get_local_player()
    local attacker = client.userid_to_entindex(event.attacker)
    local victim = client.userid_to_entindex(event.userid)

    if local_player == nil or attacker == nil or victim == nil then
        return
    end

    if attacker == local_player and victim ~= local_player then
        local killsay = "say " .. sentences[math.random(#sentences)]
        killsay = string.gsub(killsay, "$name", entity.get_player_name(victim))
        client.log(killsay)

        client.exec(killsay)
    end
end

math.randomseed(133742069)
math.random(); math.random(); math.random()

client.set_event_callback("player_death", on_player_death)
