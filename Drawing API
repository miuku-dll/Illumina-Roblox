if not shared.__BIGCACHE then
    shared.__BIGCACHE = {};
end

shared.ckill = false;

local Players = game:GetService'Players';
local LocalPlayer = Players.LocalPlayer;
local Mouse = LocalPlayer:GetMouse();
local Camera = workspace.CurrentCamera;
local RunService = game:GetService'RunService';

	local _G, game, script, getfenv, setfenv, workspace,
		getmetatable, setmetatable, loadstring, coroutine,
		rawequal, typeof, print, math, warn, error,  pcall,
		xpcall, select, rawset, rawget, ipairs, pairs,
		next, Rect, Axes, os, tick, Faces, unpack, string, Color3,
		newproxy, tostring, tonumber, Instance, TweenInfo, BrickColor,
		NumberRange, ColorSequence, NumberSequence, ColorSequenceKeypoint,
		NumberSequenceKeypoint, PhysicalProperties, Region3int16,
		Vector3int16, elapsedTime, require, table, type, wait,
		Enum, UDim, UDim2, Vector2, Vector3, Region3, CFrame, Ray, delay =
		_G, game, script, getfenv, setfenv, workspace,
		getmetatable, setmetatable, loadstring, coroutine,
		rawequal, typeof, print, math, warn, error,  pcall,
		xpcall, select, rawset, rawget, ipairs, pairs,
		next, Rect, Axes, os, tick, Faces, unpack, string, Color3,
		newproxy, tostring, tonumber, Instance, TweenInfo, BrickColor,
		NumberRange, ColorSequence, NumberSequence, ColorSequenceKeypoint,
		NumberSequenceKeypoint, PhysicalProperties, Region3int16,
		Vector3int16, elapsedTime, require, table, type, wait,
		Enum, UDim, UDim2, Vector2, Vector3, Region3, CFrame, Ray, delay

local CoroWrap = coroutine.wrap
local Clamp = math.clamp

RunService:UnbindFromRenderStep'RLESP';

local RefreshRate = 60; RefreshRate = 1 / RefreshRate;

local Colors = {
    White           = Color3.new(1, 1, 1);
    Black           = Color3.new(0.12, 0.12, 0.12);
    ReallyBlack     = Color3.new(0, 0, 0);
    Red             = Color3.new(1, 0, 0);
    Green           = Color3.new(0, 1, 0);
    Blue            = Color3.new(0, 0, 1);
    Scroll          = Color3.fromRGB(255, 151, 41);
    ScrollOutline   = Color3.fromRGB(25, 20, 30);
    Trinket         = Color3.fromRGB(81, 117, 255);
    TrinketOutline  = Color3.fromRGB(25, 25, 25);
    Gem             = Color3.fromRGB(205, 46, 255);
    HotPink         = Color3.fromRGB(255, 0, 255);
    Ice             = Color3.fromRGB(70, 255, 255);
    Shroom          = Color3.fromRGB(100, 220, 25);
    Crimson         = Color3.fromRGB(220, 50, 50);
    PD              = Color3.fromRGB(255, 120, 0);
    Diamond         = Color3.fromRGB(255, 255, 255);
    Ruby            = Color3.fromRGB(255, 25, 75);
    Emerald         = Color3.fromRGB(50, 220, 50);
    Sapphire        = Color3.fromRGB(25, 75, 255);
    RoyalPurple     = BrickColor.new'Royal purple'.Color;
}

shared.__Colors = Colors;

local Updates = setmetatable({}, {
    __index = function(t, i)
        return rawget(t, i) and rawget(t, i) or 0;
    end;
});

shared.RLDrawings = shared.RLDrawings or {
    ObjectsToDraw = setmetatable({
        Tables = {};
        Cap = 150;
    }, {
        __index = function(self, i)
            return rawget(self.Tables, i) or rawget(self, i);
        end;

        __newindex = function(self, i, v)
            local current = self.Tables[#self.Tables];
            
            if typeof(current) ~= 'table' or #current > self.Cap then
                current = {};
                table.insert(self.Tables, current);
            end

            current[i] = v;
        end
    });
    Cache = {};
    ScreenPositionCache = {};
    Enabled = true;
};

local function IsStringEmpty(String)
    if type(String) == 'string' then
        return String:match'^%s+$' ~= nil or #String == 0 or String == '' or false;
    end
    return false
end

local function Set(t, i, v)
    t[i] = v;
end

function GetIndex(T, V)
    for i, v in pairs(T) do
        if v == V then
            return i;
        end
    end

    return -1;
end

local DefaultPropties = {
    Line = {
        Visible         = false;
        Color           = Color3.new(1, 1, 1);
        Transparency    = 1;
        Thickness       = 1;
    };
    Square = {
        Size            = Vector2.new(100, 100);
        Position        = Vector2.new(250, 100);
        Filled          = false;
        Color           = Color3.new(1, 1, 1);
        Thickness       = 2;
        Visible         = true;
    };
    Text = {
        Size            = 16;
        Position        = Vector2.new(25, 15);
        Text            = 'undefined';
        Color           = Color3.new(1, 1, 1);
        Visible         = true;
        Center          = true;
        Outline         = true;
        OutlineColor    = Colors.Black;
    };
    Circle = {
        Radius          = 10;
        Position        = Vector2.new(25, 25);
        Color           = Color3.new(1, 1, 1);
        Filled          = true;
        Visible         = true;
    };
};

function SetDefaultProperties(Drawing)
    local Type = getmetatable(Drawing).__type;

    if DefaultPropties[Type] then
        for i, v in pairs(DefaultPropties[Type]) do
            pcall(Set, Drawing, i, v);
        end
    end
end

local function False() return false; end

function shared.RLDrawings:GetInstanceName(Instance, Name)
    if Instance == nil or typeof(Instance) ~= 'Instance' then
        return false;
    end

    return string.format('%s-%s', Instance:GetDebugId(), Name);
end

function shared.RLDrawings:DrawingExists(Instance, Name)
    local CachedName = self:GetInstanceName(Instance, Name);
    return self.Cache[CachedName] ~= nil;
end

function shared.RLDrawings:CreateDrawing(Type, Instance, Name)
    if not self:DrawingExists(Instance, Name) then
        local CachedName = self:GetInstanceName(Instance, Name);

        local Drawing = Drawing.new(Type);
        table.insert(shared.__BIGCACHE, Drawing);
        
        SetDefaultProperties(Drawing);

        self.Cache[CachedName] = {
            LastUpdate = 0;
            Instance = Instance;
            Drawing = Drawing;
        };

        return Drawing;
    else
        return false, 'drawing already exists';
    end
end

function shared.RLDrawings:GetDrawing(Instance, Name)
    if self:DrawingExists(Instance, Name) then
        local CachedName = self:GetInstanceName(Instance, Name);
        local Drawing = nil;
        local Cache = self.Cache[CachedName];

        self.Cache[CachedName].LastUpdate = os.clock();

        if Cache.Drawing then
            Drawing = Cache.Drawing
        else
            return False;
        end

        return function(func)
            if typeof(func) == 'function' then
                func(Drawing);
            else
                return Drawing;
            end
        end;
    end

    return False
end

function shared.RLDrawings:RemoveDrawing(Instance, Name)
    if self:DrawingExists(Instance, Name) then
        self:GetDrawing(Instance, Name)(function(Drawing)
            local CachedName = self:GetInstanceName(Instance, Name);
            
            Drawing.Visible = false;
            Drawing:Remove();
            self.Cache[CachedName] = nil;
        end);
    end    
end

function shared.RLDrawings:CheckForInvalidDrawings()
    for i, v in pairs(self.Cache) do
        if i == nil or typeof(v) ~= 'table' or v.Instance == nil or not v.Instance:IsDescendantOf(workspace) or (os.clock() - v.LastUpdate) > 10 then
            -- i == nil or typeof(v) ~= 'table' or v.Instance == nil or (v.Instance.Parent ~= workspace and not v.Instance:IsDescendantOf(workspace)) or (os.clock() - v.LastUpdate) > 1 or not v.Instance:IsDescendantOf(workspace)
            -- print('1',i==nil)
            -- print('2',i.Parent==nil)
            -- print('3',v.Instance==nil)
            -- print('4',v.Instance.Parent~=workspace)
            -- print('5',os.clock() - v.LastUpdate > 1)
            -- print('6',v.Instance:IsDescendantOf(workspace));
            -- print('removing', v.Instance:GetDebugId());
            -- print()
            v.Drawing.Visible = false;
            v.Drawing:Remove();
            self.Cache[i] = nil;
            self:RemoveObject(v.Instance);
        end
    end
end

function shared.RLDrawings:Cleanup()
    for i, v in pairs(self.Cache) do
        if i.Parent == nil or not i:IsDescendantOf(workspace) or (os.clock() - v.LastUpdate) > 1 / 3 then
            v.Drawing.Visible = false;
            v.Drawing:Remove();
            self.Cache[i] = nil;
        end
    end
end

-- local t = nil;

function shared.RLDrawings:GetScreenPosition(Instance)
    -- if Instance.ClassName ~= 'Part' and Instance.ClassName ~= 'UnionOperation' and not Instance:IsA'BasePart' then
    --     -- print(Instance.ClassName ~= 'Part', Instance.ClassName ~= 'UnionOperation', Instance:IsA'BasePart');
    --     return false;
    -- end

    local Unit = (Instance.Position - Camera.CFrame.p).unit;
    local Dot = Camera.CFrame.lookVector:Dot(Unit);

    if Dot > 1.000001 then
        return false;
    end

    if self.ScreenPositionCache[Instance] == nil then
        self.ScreenPositionCache[Instance] = {
            LastUpdate = os.clock() + 1;
            ScreenPosition = Vector2.new();
        };
    end

    local Cache = self.ScreenPositionCache[Instance];

    if (os.clock() - Cache.LastUpdate) > RefreshRate then
        -- local t = t or Instance;

        -- if Instance == t then
        --     print(Instance:GetFullName(), os.clock(), os.clock() - Cache.LastUpdate, tableToString(debug.getlocals(2)));
        -- end

        local Position, _V = Camera:WorldToViewportPoint(Instance.Position);

        Cache.LastUpdate = os.clock();
        Cache.ScreenPosition = Vector2.new(Position.X, Position.Y);
        Cache.Depth = Position.Z;
        Cache.Distance = (Camera.CFrame.p - Instance.Position).magnitude; 
        -- tonumber(string.format('%.1f', (Camera.CFrame.p - Instance.Position).magnitude));
        Cache.Visible = _V;
        Cache.OVisible = Position.Z > 0;
    end

    self.ScreenPositionCache[Instance] = Cache;

    return Cache;
end

function shared.RLDrawings:AddObject(Instance, Text, Color, DrawDistance, DrawTracer, DrawCircle)
    if typeof(Instance) ~= 'Instance' and not Instance:IsA'BasePart' then return false; end

    local Table = {
        Instance = Instance;
        Text = IsStringEmpty(Text) and nil or Text;
        Color = Color or Colors.White;
        DrawTracer = DrawTracer;
        DrawCircle = DrawCircle;
        DrawDistance = DrawDistance;
    };

    self.ObjectsToDraw[Instance] = Table;

    return Table;
end

function shared.RLDrawings:RemoveObject(Instance)
    self.ObjectsToDraw[Instance] = nil;
end

function DrawObjectText(Instance, DisplayText, DisplayColor, ShowDistance)
    local TextObject = shared.RLDrawings:GetDrawing(Instance, 'NameTag')() or shared.RLDrawings:CreateDrawing('Text', Instance, 'NameTag');

    local Position, Visible = shared.RLDrawings:GetScreenPosition(Instance);

    -- print(Position);
    
    if Position and Position.Visible and Position.Distance < 25000 then
        if ShowDistance then
            DisplayText = DisplayText .. '\n' .. tostring(Position.Distance):match'%d+.[%d]?';
            -- string.format('%s\n[%s]', DisplayText, tostring(Position.Distance):match'%d+.[%d]?');
        end

        TextObject.Visible = true;
        TextObject.Size = 17;
        TextObject.Text = DisplayText or 'undefined';
        TextObject.Color = DisplayColor or Colors.White;
        TextObject.Position = Position.ScreenPosition;
        TextObject.Outline = true;
        
        if syn then
            TextObject.OutlineColor = Color3.fromRGB(45, 45, 45);
			TextObject.Font = Drawing.Fonts.Monospace;
        end
    else
        TextObject.Visible = false;
    end
end

function DrawObjectTracer(Instance, DisplayColor)
    local LineObject = shared.RLDrawings:GetDrawing(Instance, 'Tracer')() or shared.RLDrawings:CreateDrawing('Line', Instance, 'Tracer');

    local Position = shared.RLDrawings:GetScreenPosition(Instance);

    if Position and Position.OVisible and Position.Distance < 1000 then
        LineObject.Visible = true;
        LineObject.Color = DisplayColor or Colors.White;
        LineObject.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y);
        LineObject.To = Position.ScreenPosition;
    else
        LineObject.Visible = false;
    end
end

function DrawObjectCircle(Instance, DisplayColor)
    local CircleObject = shared.RLDrawings:GetDrawing(Instance, 'Dot')() or shared.RLDrawings:CreateDrawing('Circle', Instance, 'Dot');

    local Position, Visible = shared.RLDrawings:GetScreenPosition(Instance);

    if Position and Position.Visible and Position.Distance < 1000 then
        local Distance = Position.Depth;

        if LocalPlayer.Character and LocalPlayer.Character.PrimaryPart then
            Distance = (LocalPlayer.Character.PrimaryPart.Position - Instance.Position).magnitude;
        end

        CircleObject.Visible = true;
        CircleObject.Color = DisplayColor or Colors.White;
        CircleObject.Radius = Clamp(650 / Distance, 3, 20);
        CircleObject.Filled = true;
        CircleObject.Transparency = CircleObject.Radius <= 7.5 and 1 or 0.35;
        CircleObject.Position = Position.ScreenPosition;
    else
        CircleObject.Visible = false;
    end
end

function DrawObjects(Table)
    if typeof(Table) ~= 'table' then return; end

    for i, v in pairs(Table) do
        -- print(i, v, typeof(v));
        if shared.ckill then break; end

        if typeof(v) == 'table' and v.Instance ~= nil then
            if v.Text then
                DrawObjectText(v.Instance, v.Text, v.Color, v.DrawDistance);
            end
            if v.DrawTracer then
                DrawObjectTracer(v.Instance, v.Color);
            end
            if v.DrawCircle then
                DrawObjectCircle(v.Instance, v.Color);
            end
        end
    end
end

RunService:BindToRenderStep('RLESP', 1, function()
    if (os.clock() - Updates.InvalidCheck) > 1 then
        Updates.InvalidCheck = os.clock();
        shared.RLDrawings:CheckForInvalidDrawings();
    end

    if (os.clock() - Updates.ESP) > RefreshRate then
        Updates.ESP = os.clock();
        for _, v in ipairs(shared.RLDrawings.ObjectsToDraw.Tables) do
            if shared.ckill then break; end

            CoroWrap(DrawObjects)(v);
        end
    end
end)

return shared.RLDrawings
