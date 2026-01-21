-- Combined Script by cesar priv (FIXED)
-- UI: Fluent (sin cambios) | Lógica: Adaptada de Private Patrick
-- Optimized & merged
 
local http_get = game.HttpGetAsync or game.HttpGet
local fluent_code = http_get(game, "https://github.com/ActualMasterOogway/Fluent-Renewed/releases/latest/download/Fluent.luau")
local Fluent = loadstring(fluent_code)()
local save_manager = loadstring(http_get(game, "https://raw.githubusercontent.com/ActualMasterOogway/Fluent-Renewed/master/Addons/SaveManager.luau"))()
 
local Window = Fluent:CreateWindow({
    Title = "Combined Hub by cesar priv",
    SubTitle = "Muscle Legends Ultimate",
    TabWidth = 125,
    Size = UDim2.fromOffset(830, 525),
    MinSize = Vector2.new(470, 380),
    Resize = true,
    Acrylic = true,
    Theme = "DuoTone Dark Sea",
    MinimizeKey = Enum.KeyCode.RightControl,
})
 
local tabs = {
    Main = Window:CreateTab({ Title = "Main", Icon = "phosphor-house-bold" }),
    AutoFarm = Window:CreateTab({ Title = "Auto Farm", Icon = "phosphor-robot-bold" }),
    AutoBuy = Window:CreateTab({ Title = "Auto Buy", Icon = "phosphor-shopping-cart-bold" }),
    Rebirth = Window:CreateTab({ Title = "Rebirth", Icon = "phosphor-arrows-clockwise-bold" }),
    Killer = Window:CreateTab({ Title = "Killer", Icon = "phosphor-sword-bold" }),
    Teleport = Window:CreateTab({ Title = "Teleport", Icon = "phosphor-dog-bold" }),
    Stats = Window:CreateTab({ Title = "Stats", Icon = "phosphor-sparkle-bold" }),
    Misc = Window:CreateTab({ Title = "Misc", Icon = "phosphor-map-pin-bold" }),
    Settings = Window:CreateTab({ Title = "Settings", Icon = "phosphor-sliders-bold" }),
}
 
local Config = {
    SizeChanger = 2,
    AutoSize = false,
    SpeedChanger = 125,
    AutoSpeed = false,
    WalkOnWater = false,
    AutoFarm = {},
    ToolSpeed = false,
    _AutoRepFarmEnabled = false,
    AutoEatEnabled = false,
    AntiLag = false,
    AutoRebirth = false,
    AutoEquipBest = false,
    AutoEat = false,
    AutoBuyPet = false,
    AutoBuyAura = false,
    SelectedPet = "",
    SelectedAura = "",
    KillV2 = false,
    SelectedTarget = nil,
    EspEnabled = false,
    TeamCheckEsp = false,
    GodModeBrawl = false,
    AutoJoinBrawl = false,
    AntiAFK = false,
    AutoLift = false,
    FastStrength = false,
    FarmSpeed = 2500,
    AutoJungleBench = false,
    AutoJungleBarLift = false,
    AutoJungleSquat = false,
    AutoEquipWeight = false,
    AutoEquipPushups = false,
    AutoEquipSitups = false,
    AutoEquipHandstands = false,
    FastTools = false,
    AutoSpinWheel = false,
    AutoGoodKarma = false,
    AutoBadKarma = false,
    AutoPunch = false,
    AutoSlams = false,
    FollowSlam = false,
}
 
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StatsService = game:GetService("Stats")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local VirtualInputManager = game:GetService("VirtualInputManager")
 
-- Utility Functions
local function getPlayerByName(name)
    if not name or name == "" then return nil end
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Name == name or plr.DisplayName == name then
            return plr
        end
    end
    return nil
end
 
local function getStatValue(statName)
    local leaderstats = LocalPlayer:FindFirstChild("leaderstats")
    if leaderstats then
        local stat = leaderstats:FindFirstChild(statName)
        if stat and typeof(stat.Value) == "number" then return stat.Value end
    end
    local direct = LocalPlayer:FindFirstChild(statName)
    if direct and typeof(direct.Value) == "number" then return direct.Value end
    return 0
end
 
local function abbreviateNumber(num)
    if not num then return "0" end
    local suffixes = {"", "K", "M", "B", "T", "Qa"}
    local absNum = math.abs(num)
    if absNum < 1000 then return tostring(math.floor(num)) end
    local tier = math.floor((math.log(absNum) / math.log(10)) / 3)
    tier = math.min(tier, #suffixes - 1)
    local suffix = suffixes[tier + 1]
    local scale = 10 ^ (tier * 3)
    return string.format("%.2f%s", num / scale, suffix)
end
 
local function getPing()
    local success, ping = pcall(function()
        return StatsService.Network.ServerStatsItem["Data Ping"]:GetValue()
    end)
    return success and ping or 999
end
 
-- Stats Tracker Function (FIXED)
local function createStatsTracker()
    local ScreenGui = Instance.new("ScreenGui")
    local Frame = Instance.new("Frame")
    local TextLabel = Instance.new("TextLabel")
    local UICorner = Instance.new("UICorner")
    local CloseButton = Instance.new("TextButton")
    
    ScreenGui.Name = "StatsTracker"
    ScreenGui.Parent = game.CoreGui
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    Frame.Parent = ScreenGui
    Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    Frame.BorderSizePixel = 0
    Frame.Position = UDim2.new(0.75, 0, 0.1, 0)
    Frame.Size = UDim2.new(0, 250, 0, 180)
    Frame.Active = true
    Frame.Draggable = true
    
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Frame
    
    CloseButton.Parent = Frame
    CloseButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    CloseButton.Position = UDim2.new(1, -30, 0, 5)
    CloseButton.Size = UDim2.new(0, 25, 0, 25)
    CloseButton.Font = Enum.Font.SourceSansBold
    CloseButton.Text = "X"
    CloseButton.TextColor3 = Color3.new(1, 1, 1)
    CloseButton.TextSize = 16
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 4)
    closeCorner.Parent = CloseButton
    
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)
    
    TextLabel.Parent = Frame
    TextLabel.Size = UDim2.new(1, -10, 1, -40)
    TextLabel.Position = UDim2.new(0, 5, 0, 35)
    TextLabel.BackgroundTransparency = 1
    TextLabel.TextColor3 = Color3.new(1, 1, 1)
    TextLabel.TextSize = 16
    TextLabel.Font = Enum.Font.Code
    TextLabel.TextXAlignment = Enum.TextXAlignment.Left
    TextLabel.TextYAlignment = Enum.TextYAlignment.Top
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Parent = Frame
    titleLabel.Size = UDim2.new(1, -40, 0, 30)
    titleLabel.Position = UDim2.new(0, 5, 0, 5)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = "📊 Stats Tracker"
    titleLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
    titleLabel.TextSize = 18
    titleLabel.Font = Enum.Font.SourceSansBold
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    task.spawn(function()
        while ScreenGui.Parent do
            pcall(function()
                local strength = getStatValue("Strength")
                local rebirths = getStatValue("Rebirths")
                local durability = getStatValue("Durability")
                local chakra = getStatValue("Chakra")
                
                TextLabel.Text = string.format(
                    "💪 Strength: %s\n" ..
                    "🔄 Rebirths: %s\n" ..
                    "🛡️ Durability: %s\n" ..
                    "⚡ Chakra: %s\n" ..
                    "📶 Ping: %dms",
                    abbreviateNumber(strength),
                    abbreviateNumber(rebirths),
                    abbreviateNumber(durability),
                    abbreviateNumber(chakra),
                    math.floor(getPing())
                )
            end)
            task.wait(1)
        end
    end)
    
    Fluent:Notify({
        Title = "Stats Tracker",
        Content = "Stats tracker window opened!",
        Duration = 3
    })
end
 
-- Anti AFK
local antiAFKConn
local function toggleAntiAFK(state)
    if state then
        if antiAFKConn then antiAFKConn:Disconnect() end
        antiAFKConn = LocalPlayer.Idled:Connect(function()
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end)
    else
        if antiAFKConn then antiAFKConn:Disconnect() antiAFKConn = nil end
    end
end
 
-- Auto Lift (rep) simple
local autoLiftTask
local function toggleAutoLift(state)
    if autoLiftTask then
        task.cancel(autoLiftTask)
        autoLiftTask = nil
    end
    
    if state then
        autoLiftTask = task.spawn(function()
            while Config.AutoLift do
                pcall(function()
                    if LocalPlayer:FindFirstChild("muscleEvent") then
                        LocalPlayer.muscleEvent:FireServer("rep")
                    end
                end)
                task.wait(0.1)
            end
        end)
    end
end
 
-- Fast Strength (con velocidad configurable)
local fastStrengthTask
local function toggleFastStrength(state)
    if fastStrengthTask then
        task.cancel(fastStrengthTask)
        fastStrengthTask = nil
    end
    
    if state then
        fastStrengthTask = task.spawn(function()
            while Config.FastStrength do
                pcall(function()
                    for i = 1, Config.FarmSpeed do
                        if not Config.FastStrength then break end
                        if LocalPlayer:FindFirstChild("muscleEvent") then
                            LocalPlayer.muscleEvent:FireServer("rep")
                        end
                    end
                end)
                task.wait(0.1)
            end
        end)
    end
end
 
-- Strength OP High/Low Ping
getgenv()._AutoRepFarmHighPing = false
getgenv()._AutoRepFarmLowPing = false
 
local function equipPet(petName)
    pcall(function()
        local pets = LocalPlayer:FindFirstChild("petsFolder")
        if pets and pets:FindFirstChild("Unique") then
            for _, pet in pairs(pets.Unique:GetChildren()) do
                if pet.Name == petName then
                    ReplicatedStorage.rEvents.equipPetEvent:FireServer("equipPet", pet)
                    break
                end
            end
        end
    end)
end
 
local function hitRock(rockName)
    pcall(function()
        local rock = Workspace:FindFirstChild(rockName)
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if rock and hrp then
            hrp.CFrame = rock.CFrame * CFrame.new(0, 0, -5)
            ReplicatedStorage.rEvents.hitEvent:FireServer("hit", rock)
        end
    end)
end
 
local function strengthOPHighPing()
    task.spawn(function()
        equipPet("Swift Samurai")
        local lastRock = tick()
        while getgenv()._AutoRepFarmHighPing do
            pcall(function()
                if LocalPlayer:FindFirstChild("muscleEvent") then
                    for i = 1, 10 do 
                        LocalPlayer.muscleEvent:FireServer("rep") 
                    end
                end
                if tick() - lastRock >= 5 then
                    hitRock("Rock5M")
                    lastRock = tick()
                end
            end)
            task.wait(0.01)
        end
    end)
end
 
local function strengthOPLowPing()
    task.spawn(function()
        equipPet("Swift Samurai")
        local lastRock = tick()
        while getgenv()._AutoRepFarmLowPing do
            pcall(function()
                if LocalPlayer:FindFirstChild("muscleEvent") then
                    for i = 1, 40 do 
                        LocalPlayer.muscleEvent:FireServer("rep") 
                    end
                end
                if tick() - lastRock >= 5 then
                    hitRock("Rock5M")
                    lastRock = tick()
                end
            end)
            task.wait(0.01)
        end
    end)
end
 
-- Fast Tools
local function toggleFastTools(state)
    local tools = {
        {"Punch", "attackTime", state and 0 or 0.35},
        {"Ground Slam", "attackTime", state and 0 or 6},
        {"Handstands", "repTime", state and 0 or 1},
        {"Pushups", "repTime", state and 0 or 1},
        {"Weight", "repTime", state and 0 or 1},
        {"Situps", "repTime", state and 0 or 1}
    }
    for _, t in ipairs(tools) do
        pcall(function()
            local tool = LocalPlayer.Backpack:FindFirstChild(t[1]) or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild(t[1]))
            if tool and tool:FindFirstChild(t[2]) then
                tool[t[2]].Value = t[3]
            end
        end)
    end
end
 
-- Auto Equip tools
local autoEquipTasks = {}
local function autoEquipTool(toolName)
    if autoEquipTasks[toolName] then
        task.cancel(autoEquipTasks[toolName])
    end
    
    autoEquipTasks[toolName] = task.spawn(function()
        while Config["AutoEquip" .. toolName] do
            pcall(function()
                local tool = LocalPlayer.Backpack:FindFirstChild(toolName)
                if tool then 
                    tool.Parent = LocalPlayer.Character 
                end
            end)
            task.wait(0.1)
        end
    end)
end
 
-- Auto Punch
local autoPunchTask
local function toggleAutoPunch(state)
    if autoPunchTask then
        task.cancel(autoPunchTask)
        autoPunchTask = nil
    end
    
    if state then
        autoPunchTask = task.spawn(function()
            while Config.AutoPunch do
                pcall(function()
                    local punch = LocalPlayer.Backpack:FindFirstChild("Punch")
                    if punch then
                        punch.Parent = LocalPlayer.Character
                        if punch:FindFirstChild("attackTime") then 
                            punch.attackTime.Value = 0 
                        end
                    end
                    LocalPlayer.muscleEvent:FireServer("punch", "rightHand")
                    LocalPlayer.muscleEvent:FireServer("punch", "leftHand")
                end)
                task.wait(0.05)
            end
        end)
    end
end
 
-- Auto Slams
local autoSlamsTask
local function toggleAutoSlams(state)
    if autoSlamsTask then
        task.cancel(autoSlamsTask)
        autoSlamsTask = nil
    end
    
    if state then
        autoSlamsTask = task.spawn(function()
            while Config.AutoSlams do
                pcall(function()
                    local slam = LocalPlayer.Backpack:FindFirstChild("Ground Slam") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Ground Slam"))
                    if slam then
                        if slam.Parent == LocalPlayer.Backpack then 
                            slam.Parent = LocalPlayer.Character 
                        end
                        if slam:FindFirstChild("attackTime") then 
                            slam.attackTime.Value = 0 
                        end
                        LocalPlayer.muscleEvent:FireServer("slam")
                        slam:Activate()
                    end
                end)
                task.wait(0.1)
            end
        end)
    end
end
 
-- Auto Kill
local killTask
local function toggleAutoKill(state)
    if killTask then
        task.cancel(killTask)
        killTask = nil
    end
    
    if state then
        killTask = task.spawn(function()
            while Config.KillV2 do
                pcall(function()
                    local char = LocalPlayer.Character
                    if char then
                        local rh = char:FindFirstChild("RightHand")
                        local lh = char:FindFirstChild("LeftHand")
                        if rh and lh then
                            for _, plr in ipairs(Players:GetPlayers()) do
                                if plr ~= LocalPlayer and plr.Character then
                                    local root = plr.Character:FindFirstChild("HumanoidRootPart")
                                    if root then
                                        firetouchinterest(rh, root, 1)
                                        firetouchinterest(lh, root, 1)
                                        firetouchinterest(rh, root, 0)
                                        firetouchinterest(lh, root, 0)
                                    end
                                end
                            end
                        end
                    end
                end)
                task.wait(0.05)
            end
        end)
    end
end
 
-- Anti Lag
local function enableAntiLag(enabled)
    if enabled then
        pcall(function()
            local lighting = game:GetService("Lighting")
            
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if obj:IsA("ParticleEmitter") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
                    obj.Enabled = false
                end
            end
            
            lighting.GlobalShadows = false
            lighting.FogEnd = 9e9
            lighting.Brightness = 0
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
            
            for _, v in ipairs(lighting:GetChildren()) do
                if v:IsA("Sky") then v:Destroy() end
            end
            
            local sky = Instance.new("Sky")
            sky.SkyboxBk = "rbxassetid://0"
            sky.SkyboxDn = "rbxassetid://0"
            sky.SkyboxFt = "rbxassetid://0"
            sky.SkyboxLf = "rbxassetid://0"
            sky.SkyboxRt = "rbxassetid://0"
            sky.SkyboxUp = "rbxassetid://0"
            sky.Parent = lighting
            
            lighting.Brightness = 0
            lighting.ClockTime = 0
            lighting.OutdoorAmbient = Color3.new(0, 0, 0)
        end)
    end
end
 
-- ============================================
-- UI SETUP
-- ============================================
 
do -- Main Tab
    local section = tabs.Main:CreateSection("Basic Controls")
    
    section:AddInput("SizeChanger", {
        Title = "Size Changer",
        Default = "2",
        Numeric = true,
        Finished = true,
        Callback = function(value)
            Config.SizeChanger = tonumber(value) or 2
            if Config.AutoSize then
                pcall(function() 
                    LocalPlayer.Character.Humanoid.BodyDepthScale.Value = Config.SizeChanger
                    LocalPlayer.Character.Humanoid.BodyWidthScale.Value = Config.SizeChanger
                    LocalPlayer.Character.Humanoid.BodyHeightScale.Value = Config.SizeChanger
                    LocalPlayer.Character.Humanoid.HeadScale.Value = Config.SizeChanger
                end)
            end
        end,
    })
    
    section:AddToggle("AutoSize", {
        Title = "Auto Set Size",
        Default = false,
        Callback = function(v) 
            Config.AutoSize = v 
        end,
    })
    
    section:AddInput("SpeedChanger", {
        Title = "Speed Changer",
        Default = "125",
        Numeric = true,
        Finished = true,
        Callback = function(value)
            Config.SpeedChanger = tonumber(value) or 125
            if Config.AutoSpeed then
                pcall(function() 
                    LocalPlayer.Character.Humanoid.WalkSpeed = Config.SpeedChanger 
                end)
            end
        end,
    })
    
    section:AddToggle("AutoSpeed", {
        Title = "Auto Set Speed",
        Default = false,
        Callback = function(v) 
            Config.AutoSpeed = v 
        end,
    })
    
    section:AddButton("FreeAutoLiftGamepass", {
        Title = "Free AutoLift Gamepass",
        Description = "Unlock autolift gamepass",
        Callback = function()
            pcall(function()
                local gp = ReplicatedStorage.gamepassIds
                for _, v in pairs(gp:GetChildren()) do
                    local iv = Instance.new("IntValue", LocalPlayer.ownedGamepasses)
                    iv.Name = v.Name
                    iv.Value = v.Value
                end
                Fluent:Notify({
                    Title = "Success",
                    Content = "Gamepass unlocked!",
                    Duration = 3
                })
            end)
        end,
    })
    
    section:AddToggle("WalkOnWater", {
        Title = "Walk on Water",
        Default = false,
        Callback = function(v)
            Config.WalkOnWater = v
            if v then
                task.spawn(function()
                    while Config.WalkOnWater do
                        pcall(function()
                            local hrp = LocalPlayer.Character.HumanoidRootPart
                            if hrp.Position.Y < Workspace.FallenPartsDestroyHeight + 100 then
                                hrp.Velocity = Vector3.new(hrp.Velocity.X, 0, hrp.Velocity.Z)
                            end
                        end)
                        task.wait(0.5)
                    end
                end)
            end
        end,
    })
end
 
do -- AutoFarm Tab
    local farmSection = tabs.AutoFarm:CreateSection("Auto Farm Options")
    
    farmSection:AddToggle("Weight", {
        Title = "Auto Weight", 
        Default = false, 
        Callback = function(v) 
            Config.AutoEquipWeight = v 
            autoEquipTool("Weight") 
        end
    })
    
    farmSection:AddToggle("Pushups", {
        Title = "Auto Pushups", 
        Default = false, 
        Callback = function(v) 
            Config.AutoEquipPushups = v 
            autoEquipTool("Pushups") 
        end
    })
    
    farmSection:AddToggle("Handstands", {
        Title = "Auto Handstands", 
        Default = false, 
        Callback = function(v) 
            Config.AutoEquipHandstands = v 
            autoEquipTool("Handstands") 
        end
    })
    
    farmSection:AddToggle("Situps", {
        Title = "Auto Situps", 
        Default = false, 
        Callback = function(v) 
            Config.AutoEquipSitups = v 
            autoEquipTool("Situps") 
        end
    })
    
    farmSection:AddToggle("Punch", {
        Title = "Auto Punch", 
        Default = false, 
        Callback = function(v) 
            Config.AutoPunch = v 
            toggleAutoPunch(v) 
        end
    })
    
    farmSection:AddToggle("ToolSpeed", {
        Title = "Fast Tools", 
        Default = false, 
        Callback = function(v) 
            Config.FastTools = v 
            toggleFastTools(v) 
        end
    })
    
    farmSection:AddToggle("FuerzaOP", {
        Title = "Optimized Auto Farm",
        Description = "Farm with rock breaking",
        Default = false,
        Callback = function(v)
            Config._AutoRepFarmEnabled = v
            if v then
                local currentPing = getPing()
                if currentPing > 250 then
                    getgenv()._AutoRepFarmHighPing = true
                    getgenv()._AutoRepFarmLowPing = false
                    strengthOPHighPing()
                else
                    getgenv()._AutoRepFarmLowPing = true
                    getgenv()._AutoRepFarmHighPing = false
                    strengthOPLowPing()
                end
            else
                getgenv()._AutoRepFarmHighPing = false
                getgenv()._AutoRepFarmLowPing = false
            end
        end,
    })
    
    farmSection:AddToggle("AntiLag", {
        Title = "Anti Lag", 
        Description = "Reduces lag",
        Default = false, 
        Callback = function(v) 
            Config.AntiLag = v 
            enableAntiLag(v) 
        end
    })
end
 
do -- AutoBuy Tab
    local buySection = tabs.AutoBuy:CreateSection("Auto Buy")
    
    buySection:AddDropdown("SelectPet", {
        Title = "Select Pet",
        Values = {"Swift Samurai", "Tribal Overlord", "Eternal Legends"},
        Multi = false,
        Default = 1,
        Callback = function(v) 
            Config.SelectedPet = v 
        end,
    })
    
    buySection:AddToggle("AutoBuyPet", {
        Title = "Auto Buy Pet",
        Default = false,
        Callback = function(v)
            Config.AutoBuyPet = v
            if v and Config.SelectedPet ~= "" then
                task.spawn(function()
                    while Config.AutoBuyPet do
                        pcall(function()
                            local pet = ReplicatedStorage.cPetShopFolder:FindFirstChild(Config.SelectedPet)
                            if pet then 
                                ReplicatedStorage.cPetShopRemote:InvokeServer(pet) 
                            end
                        end)
                        task.wait(1)
                    end
                end)
            end
        end,
    })
    
    buySection:AddDropdown("SelectAura", {
        Title = "Select Aura",
        Values = {"Basic Aura", "Fire Aura", "Ice Aura"},
        Multi = false,
        Default = 1,
        Callback = function(v) 
            Config.SelectedAura = v 
        end,
    })
    
    buySection:AddToggle("AutoBuyAura", {
        Title = "Auto Buy Aura",
        Default = false,
        Callback = function(v)
            Config.AutoBuyAura = v
            if v and Config.SelectedAura ~= "" then
                task.spawn(function()
                    while Config.AutoBuyAura do
                        pcall(function()
                            local aura = ReplicatedStorage.cPetShopFolder:FindFirstChild(Config.SelectedAura)
                            if aura then 
                                ReplicatedStorage.cPetShopRemote:InvokeServer(aura) 
                            end
                        end)
                        task.wait(1)
                    end
                end)
            end
        end,
    })
end
 
do -- Rebirth Tab
    local rebirthSection = tabs.Rebirth:CreateSection("Rebirth Options")
    
    rebirthSection:AddToggle("AutoRebirth", {
        Title = "Auto Rebirth",
        Default = false,
        Callback = function(v)
            Config.AutoRebirth = v
            if v then
                task.spawn(function()
                    while Config.AutoRebirth do
                        pcall(function()
                            ReplicatedStorage.rEvents.rebirthRemote:InvokeServer("rebirthRequest")
                        end)
                        task.wait(0.5)
                    end
                end)
            end
        end,
    })
end
 
do -- Killer Tab
    local killerSection = tabs.Killer:CreateSection("Auto Kill")
    
    killerSection:AddToggle("KillV2", {
        Title = "Start Killing All",
        Description = "Kill all players",
        Default = false,
        Callback = function(v)
            Config.KillV2 = v
            toggleAutoKill(v)
        end,
    })
    
    local playerDropdown = killerSection:AddDropdown("PlayerList", {
        Title = "Select Target",
        Values = {},
        Multi = false,
        Callback = function(v) 
            Config.SelectedTarget = v 
        end,
    })
    
    local function refreshPlayers()
        local names = {}
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer then 
                table.insert(names, plr.Name) 
            end
        end
        playerDropdown:SetValues(names)
    end
    
    refreshPlayers()
    Players.PlayerAdded:Connect(refreshPlayers)
    Players.PlayerRemoving:Connect(refreshPlayers)
end
 
do -- Teleport Tab
    local teleSection = tabs.Teleport:CreateSection("Teleports")
    
    local places = {
        {Name = "Spawn", Pos = CFrame.new(2, 8, 115)},
        {Name = "Jungle Gym", Pos = CFrame.new(-8137, 28, 2820)},
        {Name = "Muscle King Island", Pos = CFrame.new(-8646, 17, -5738)},
        {Name = "Snow Area", Pos = CFrame.new(-4600, 4, -3650)},
        {Name = "City", Pos = CFrame.new(-5682, 4, -474)},
    }
    
    for _, p in ipairs(places) do
        teleSection:AddButton(p.Name, {
            Title = "TP to " .. p.Name,
            Callback = function()
                pcall(function() 
                    LocalPlayer.Character.HumanoidRootPart.CFrame = p.Pos 
                end)
            end,
        })
    end
end
 
do -- Stats Tab
    local statsSection = tabs.Stats:CreateSection("Stats Tracker")
    
    statsSection:AddButton("ShowTracker", {
        Title = "Show Stats Tracker",
        Description = "Display live stats window",
        Callback = function()
            createStatsTracker()
        end,
    })
    
    statsSection:AddParagraph({
        Title = "Stats Info",
        Content = "Click the button above to open a draggable stats window that shows your current stats in real-time."
    })
end
 
do -- Misc Tab
    local miscSection = tabs.Misc:CreateSection("Miscellaneous")
    
    miscSection:AddToggle("AntiAFK", {
        Title = "Anti AFK", 
        Description = "Prevents being kicked for inactivity",
        Default = false, 
        Callback = function(v) 
            Config.AntiAFK = v 
            toggleAntiAFK(v) 
        end
    })
    
    miscSection:AddToggle("AutoLift", {
        Title = "Auto Lift", 
        Description = "Auto rep farming",
        Default = false, 
        Callback = function(v) 
            Config.AutoLift = v 
            toggleAutoLift(v) 
        end
    })
    
    miscSection:AddToggle("FastStrength", {
        Title = "Fast Strength Farm",
        Description = "Farm strength quickly",
        Default = false,
        Callback = function(v)
            Config.FastStrength = v
            toggleFastStrength(v)
        end,
    })
    
    miscSection:AddSlider("FarmSpeed", {
        Title = "Farm Speed",
        Description = "Reps per iteration",
        Default = 2500,
        Min = 100,
        Max = 5000,
        Rounding = 100,
        Callback = function(v)
            Config.FarmSpeed = v
        end,
    })
end
 
do -- Settings Tab
    local settingsSection = tabs.Settings:CreateSection("Configuration")
    
    settingsSection:AddButton("SaveConfig", {
        Title = "Save Config",
        Description = "Save current settings",
        Callback = function() 
            save_manager:Save("Combined_Config", Config)
            Fluent:Notify({
                Title = "Config Saved",
                Content = "Your configuration has been saved!",
                Duration = 3
            })
        end
    })
    
    settingsSection:AddButton("LoadConfig", {
        Title = "Load Config",
        Description = "Load saved settings",
        Callback = function() 
            local loaded = save_manager:Load("Combined_Config")
            if loaded then
                for k, v in pairs(loaded) do 
                    Config[k] = v 
                end
                Fluent:Notify({
                    Title = "Config Loaded",
                    Content = "Your configuration has been loaded!",
                    Duration = 3
                })
            end
        end
    })
    
    settingsSection:AddButton("ResetConfig", {
        Title = "Reset Config",
        Description = "Reset to default settings",
        Callback = function()
            for k, v in pairs(Config) do
                if type(v) == "boolean" then
                    Config[k] = false
                elseif type(v) == "number" then
                    if k == "SizeChanger" then Config[k] = 2
                    elseif k == "SpeedChanger" then Config[k] = 125
                    elseif k == "FarmSpeed" then Config[k] = 2500
                    end
                elseif type(v) == "string" then
                    Config[k] = ""
                end
            end
            Fluent:Notify({
                Title = "Config Reset",
                Content = "Settings reset to default!",
                Duration = 3
            })
        end,
    })
end

-- ============================================
-- CONTINUOUS LOOPS (FIXED - Now active!)
-- ============================================

-- Loop para AutoSize y AutoSpeed
RunService.Heartbeat:Connect(function()
    if Config.AutoSize then
        pcall(function()
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.BodyDepthScale.Value = Config.SizeChanger
                char.Humanoid.BodyWidthScale.Value = Config.SizeChanger
                char.Humanoid.BodyHeightScale.Value = Config.SizeChanger
                char.Humanoid.HeadScale.Value = Config.SizeChanger
            end
        end)
    end
    
    if Config.AutoSpeed then
        pcall(function()
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.WalkSpeed = Config.SpeedChanger
            end
        end)
    end
end)

-- Notification final
Fluent:Notify({
    Title = "Script Loaded",
    Content = "Combined Hub by cesar priv loaded successfully!",
    Duration = 5
})

print("✅ Combined Script Loaded - All functions active!")
