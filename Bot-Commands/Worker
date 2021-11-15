setreadonly(string,false)
function string.split(str, seperator)
       if seperator == nil then
               seperator = "%s"
       end
       local t = {}
       for portion in string.gmatch(str, "([^"..seperator.."]+)") do
               table.insert(t, portion)
       end
       return t
end
function string.table(str)
   local string_table = {}
  for i=1,#str do
      table.insert(string_table,str:sub(i,i))
  end
 return string_table
end
setreadonly(string,true)



local local_player = game.Players.LocalPlayer


game:GetService('RunService').Stepped:connect(function()
    pcall(function()
        for i,player in ipairs(game.Players:GetPlayers()) do
            if not player.Character then continue end
            for i,p_part in ipairs(player.Character:GetDescendants()) do
                if not p_part:IsA("BasePart") then continue end
                p_part.CanCollide = false
            end
        end
    end)
end)
for i,v in ipairs(game.workspace:GetDescendants()) do
    if v.ClassName == "Seat" then v:Destroy() end
end

local function parse_line(line)
   local split_line = line:table()
   local name = ''
   local data = ''
   local data_switch = false
   for _,character in ipairs(split_line) do
       if character == '=' and data_switch == false then
           data_switch = true
           continue
       end
       if data_switch == false then
           name = name..character
       elseif data_switch == true then
           data = data..character
       end
   end
   data=tonumber(data) or data
   if data == '' then data = nil end
   return name,data
end
local function format_line(key,data)
   if type(key) == 'number' then
       return tostring(data)
   end
   local s = ("%s=%s"):format(tostring(key),tostring(data))
   return s
end

local raw_write_read = {
   fread = function(fname)
       local data = {
           array={};
       }

       if not isfile(fname) then return data end
       local raw_data = readfile(fname)
       local split_data = raw_data:split("\n")
       if not fname:match("bdata") then
       print(raw_data,fname)
       end
       
       for iterator,line in ipairs(split_data) do
          local name,ldata = parse_line(line)
          if name == '' or name == '\n' or name == '\r' then continue end
          if not ldata then
          table.insert(data['array'],name)
          else
           data[name] = ldata    
           end
       end
       return data
   end;
   fwrite = function(fname,tdata)
       local raw_data = ''
       for i,data_pair in pairs(tdata) do
           if type(i) == 'number' or i=='array' then continue end
           raw_data = raw_data..format_line(i,data_pair)..'\n'
       end
       for i,single_data in ipairs(tdata.array) do
           raw_data = raw_data..format_line(i,single_data)..'\n'
       end
       writefile(fname,raw_data)
   end;
}
if not isfolder("bot_data_transfer") then makefolder("bot_data_transfer") end
if not isfolder("bot_data_transfer/mailbox") then makefolder('bot_data_transfer/mailbox') end
local info = {
   ledger_name = "bot_data_transfer/ledger.bdata";
   mailbox_folder = "bot_data_transfer/mailboxes/";
   bot_id = game.Players.LocalPlayer.Name;
}
info['personal_mailbox'] = info.mailbox_folder..info.bot_id
info['data_list_name'] = info['personal_mailbox']..'/signals.bdata'

local function enter_ledger()
  local id = info.bot_id
  local data = raw_write_read.fread(info.ledger_name)
  if table.find(data.array,id) then return print("bot alr entered into ledger") end
  table.insert(data.array,id)
  raw_write_read.fwrite(info.ledger_name,data)
  makefolder(info.personal_mailbox)
end
local function exit_ledger()
  local id = info.bot_id
  local data = raw_write_read.fread(info.ledger_name)
  for i,v in ipairs(data.array) do
      if v == id then
          table.remove(data.array,i)
      end
   end
  raw_write_read.fwrite(info.ledger_name,data)
  if(isfolder(info.personal_mailbox)) then
    delfolder(info.personal_mailbox)
  end
end
local function get_ledger()
  return raw_write_read.fread(info.ledger_name)['array']
end

local function send_data_signal(recipient,signal_name,data)
   if not table.find(get_ledger(),recipient) then error("recipient not found on network") end
   local signal_data = data
   signal_data.array = signal_data.array or {}
   local sending_address = info.mailbox_folder..recipient
   if not isfolder(sending_address) then
      error("no signal folder found")
   end
   raw_write_read.fwrite(info.personal_mailbox..'/'..signal_name..'.signal',signal_data)
   local recipient_signal_ledger = info.mailbox_folder..recipient..'/signals.bdata'
   local signal_list = raw_write_read.fread(recipient_signal_ledger)
   table.insert(signal_list.array,signal_name)
   raw_write_read.fwrite(recipient_signal_ledger,signal_list)
end
local function read_data_signal(signal_name)
   local signal_list = raw_write_read.fread(info.data_list_name)
   for i,v in ipairs(signal_list.array) do
       if v == signal_name then
          table.remove(signal_list.array,i)
       end
   end

   local signal_data = raw_write_read.fread(info.personal_mailbox..'/'..signal_name..'.signal')
   local filename = info.personal_mailbox..'/'..signal_name..'.signal'
   print(data,"DATAXX")
   if isfile(filename) then
  	delfile(info.personal_mailbox..'/'..signal_name..'.signal')
   end
   
   raw_write_read.fwrite(info.data_list_name,signal_list)
   return signal_data
end

local signal_functions = {
   
}

local function check_signals()
   local signal_ledger = raw_write_read.fread(info.data_list_name)
   if #signal_ledger.array > 0 then
       for i,signal_name in ipairs(signal_ledger.array) do
          local data = read_data_signal(signal_name)
          print(data,"DATA THAT HAS BEEN SEND")
          for i,signal_func in ipairs(signal_functions[signal_name] or {}) do
               signal_func(data)
           end
       end
   end
end
local function new_signal_connection(signalname,func)
  signal_functions[signalname] = signal_functions[signalname] or {}
  local current_funcs = signal_functions[signalname]
  table.insert(current_funcs,func)
  return function()
   for i,v in ipairs(current_funcs or {}) do
      if v == func then table.remove(current_funcs,i) return true end
   end
  end
end

local function check_player(player)
    player = player or local_player

    local character = player.Character
    if not character then return false end
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return false end
    local rootpart = humanoid.RootPart
    if not rootpart then return false end
    return true
end

local command_storage = {
	bodyguard_player = nil;
	follow_player = nil;
	look_player = nil;
    orbit_player = nil;
    forbit_player = nil;
    orbit_step = 1;
    forbit_part = Instance.new("Part",game.workspace);
    forbit_prop = nil;
    dupetool_player = nil;
    dupetool_count = 0;
}
command_storage.forbit_part.Anchored = true
command_storage.forbit_part.CanCollide = false
local execution_routines = {
	['bodyguard'] = function(player)
        if not check_player(player) or not check_player() then return end
        local cpos = player.Character.Humanoid.RootPart.CFrame
		local pos_list = {cpos.Position + (cpos.LookVector*3) + (cpos.RightVector*3),cpos.Position + (cpos.LookVector*3) - (cpos.RightVector*3)}
		local ledger = get_ledger()
		local ledger_pos = table.find(ledger,info.bot_id)
		local_player.Character.Humanoid.WalkToPoint = pos_list[ledger_pos] or Vector3.new()
	end;
	['follow'] = function(follow_player)
		if check_player(follow_player) and check_player() then
			local_player.Character.Humanoid.WalkToPoint = follow_player.Character.Humanoid.RootPart.Position
		end
	end;
    ['look'] = function (player)
        if not check_player(player) or not check_player() then return end
        local p_root_part = player.Character.Humanoid.RootPart
        local l_root_part = local_player.Character.Humanoid.RootPart
        local custom_vector = Vector3.new(p_root_part.CFrame.X,l_root_part.CFrame.Y,p_root_part.CFrame.Z)

        if l_root_part.Velocity.X ~= 0 or l_root_part.Velocity.Y ~= 0 or l_root_part.Velocity.Z ~= 0 then return end
        l_root_part.CFrame = CFrame.new(l_root_part.Position,custom_vector)
    end;
    ['forbit'] = function(player)
        if not check_player(player) or not check_player() then return end
        if not command_storage.forbit_prop or command_storage.forbit_prop.Parent == nil then 
            print("making new rock")
            local new_rocket = Instance.new("RocketPropulsion",local_player.Character.Humanoid.RootPart)
            new_rocket.Target = command_storage.forbit_part
            new_rocket.MaxSpeed = 10000 
            new_rocket.MaxThrust = 100000
            new_rocket.ThrustP = 2000
            new_rocket.ThrustD = 500
            command_storage.forbit_prop = new_rocket
            new_rocket:Fire()
        end
        local orbit_radius = math.random(2,10);
        local speed = 1/2
        local local_bot_index = table.find(get_ledger(),info.bot_id)
        local circle_x = math.sin((command_storage.orbit_step+(local_bot_index*3.14))*speed)*orbit_radius
        local circle_y = math.cos((command_storage.orbit_step+(local_bot_index*3.14))*speed)*orbit_radius

        local player_cframe = player.Character.Humanoid.RootPart.CFrame
        player_cframe = player_cframe+ Vector3.new(circle_x,0,circle_y)
    
        command_storage.forbit_part.CFrame = CFrame.new(player_cframe.Position--[[player.Character.Humanoid.RootPart.Position]])
        command_storage.orbit_step++
    end;
    ['orbit'] = function(player)
        if not check_player(player) or not check_player() then return end
        local orbit_radius = 5;
        local speed = 1/5
        local local_bot_index = table.find(get_ledger(),info.bot_id)
        local circle_x = math.sin((command_storage.orbit_step+(local_bot_index*3.14))*speed)*orbit_radius
        local circle_y = math.cos((command_storage.orbit_step+(local_bot_index*3.14))*speed)*orbit_radius

        local player_cframe = player.Character.Humanoid.RootPart.CFrame
        player_cframe = player_cframe+ Vector3.new(circle_x,0,circle_y)
    
        local_player.Character.Humanoid.RootPart.CFrame = CFrame.new(player_cframe.Position,player.Character.Humanoid.RootPart.Position)
        command_storage.orbit_step++
    end;
    ['dupetool'] = function(player)
        if not check_player(player) or not check_player() then return end
        local_player.Character.Humanoid.RootPart.CFrame = local_player.Character.Humanoid.RootPart.CFrame + Vector3.new(math.random(1,10),math.random(1,1000),math.random(1,20))
        wait(0.2)
	    local tools = local_player.Backpack:GetChildren()
	    for i,v in ipairs(tools) do v.Parent = local_player.Character end
	    wait(0.1)
        local_player.Character.Humanoid.RootPart.Anchored = true
	    local_player.Character.Humanoid:Destroy()
	    wait(0.1)
	    for i,v in ipairs(local_player.Character:GetChildren()) do
		    if v:IsA("Tool") then
			    local fire_on = v.Handle
			    local fire_with = player.Character.Humanoid.RootPart
			    firetouchinterest(fire_on,fire_with,1,firetouchinterest(fire_on,fire_with,0))
                wait(0.1)
		    end
	    end
	    wait(0.6)
	    local_player.Character:Destroy()
        command_storage.dupetool_count = command_storage.dupetool_count-1
        if command_storage.dupetool_count == 0 then command_storage.dupetool_player = nil; end
    end;
}


local repeat_connections = {}

local function chat(msg,player)
	if player then msg = '/w '..player..' '..msg end
     game.ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(msg, "All")
end

new_signal_connection("chat",function(data)
	chat(data.message_data,data.player)
end)
new_signal_connection("teleport",function(data)
    if not check_player() then return end

	game.Players.LocalPlayer.Character.Humanoid.RootPart.CFrame = CFrame.new(data.x,data.y,data.z)
end)
new_signal_connection("walkto",function(data)
    if not check_player() then return end
	game.Players.LocalPlayer.Character.Humanoid.WalkToPoint = Vector3.new(data.x,data.y,data.z)
end)
new_signal_connection("droptools",function(data)
    if not check_player() then return end

    for i,v in ipairs(local_player.Backpack:GetChildren()) do
        v.Parent = local_player.Character
    end
    wait(0.1)
    for i,v in ipairs(local_player.Character:GetChildren()) do
        if v:IsA("Tool") then v.Parent = game.workspace end
    end
end)
new_signal_connection("reset",function(data)
    if not check_player() then return end
    local_player.Character:Destroy()
end)
new_signal_connection("follow",function(data)
	print(data.player_name,"FOLLOWSIGNAL")
	if data.player_name then
		command_storage.follow_player = game.Players:FindFirstChild(data.player_name)
	else
		command_storage.follow_player= nil
	end

end)
new_signal_connection("bodyguard",function(data)
	print(data.player_name,"guard_signal",data)
	if data.player_name then
		command_storage.bodyguard_player = game.Players:FindFirstChild(data.player_name)
	else
		command_storage.bodyguard_player = nil
	end

end)
new_signal_connection("lookat",function(data)
    print(data.player_name,"look_signal",data)

	if data.player_name then
		command_storage.look_player = game.Players:FindFirstChild(data.player_name)
	else
		command_storage.look_player = nil
	end
end)
new_signal_connection("orbit",function(data)
    print(data.player_name,"look_signal",data)

	if data.player_name then
		command_storage.orbit_player = game.Players:FindFirstChild(data.player_name)
	else
		command_storage.orbit_player = nil
	end
end)
new_signal_connection("forbit",function(data)

	if data.player_name then
		command_storage.forbit_player = game.Players:FindFirstChild(data.player_name)
	else
        if command_storage.forbit_prop then
            print('destroying forbit') 
            command_storage.forbit_prop:Abort() 
            command_storage.forbit_prop:Destroy()
            command_storage.forbit_prop= nil
        end
		command_storage.forbit_player = nil
	end
end)
new_signal_connection("joinserver",function(data)
    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, data.server_id or game.JobId, local_player)
end)
new_signal_connection("setrepeatstatus",function(data)
	data.status = data.status == 'true'
    if data.status == true then
        local player = game.Players:FindFirstChild(data.player_name)
        repeat_connections[data.player_name] = player.Chatted:connect(chat)
    else
        if data.player_name == "All" then
            for i,v in pairs(repeat_connections) do v:Disconnect() end
        else
            repeat_connections[data.player_name]:Disconnect() 
        end
    end
end)

new_signal_connection("dupetools",function(data)
    if not check_player() then return end

    command_storage.dupetool_player = game.Players:FindFirstChild(data.player_name)
    command_storage.dupetool_count = data.amount
end)
exit_ledger()
enter_ledger()

local rs = game:GetService("RunService").RenderStepped
print(get_ledger())
while rs:wait() do
   check_signals()
   if command_storage.dupetool_player then
    execution_routines.dupetool(command_storage.dupetool_player)
    continue
    end 
   if command_storage.bodyguard_player then
   	execution_routines.bodyguard(command_storage.bodyguard_player)
   end 
   if command_storage.follow_player then
   	execution_routines.follow(command_storage.follow_player)
   end
    if command_storage.look_player then
    execution_routines.look(command_storage.look_player)
    end
    if command_storage.orbit_player then
        execution_routines.orbit(command_storage.orbit_player)
    end
    if command_storage.forbit_player then
        execution_routines.forbit(command_storage.forbit_player)
    end
end


