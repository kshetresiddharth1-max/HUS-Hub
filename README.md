# HUS-Hub
This Script is Made by Me This Script iss So Powerfull now I want to Test Wthid Script 
-- ============================================
-- HUS HUB - BY SIDDHARTH
-- Optimized for High School Life RP
-- Key System Fixed: Siddharth1
-- ============================================

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")

-- CONFIGURATION
local CONFIG = {
    ValidKey = "Siddharth1", -- 🔑 KEY SET TO SIDDHARTH1
    Title = "HUS Hub",
    Subtitle = "High School Life Ultimate Hub | Made by Siddharth"
}

-- SMART REMOTE DETECTION (Auto-Fixes on Game Update)
local function findRemote(patterns)
    local found = nil
    for _, obj in pairs(ReplicatedStorage:GetDescendants()) do
        if obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
            for _, pat in ipairs(patterns) do
                if string.find(string.lower(obj.Name), string.lower(pat)) then
                    found = obj
                    break
                end
            end
        end
        if found then break end
    end
    return found
end

-- Detect Remotes immediately
local bioRemote = findRemote({"bio", "updatebio", "setbio"})
local nameRemote = findRemote({"name", "setname", "rpname", "updatename"})

print("[HUS Hub] System Initialized by Siddharth")
print("- Bio Remote: " .. (bioRemote and bioRemote.Name or "Not Found"))
print("- Name Remote: " .. (nameRemote and nameRemote.Name or "Not Found"))

-- KEY SYSTEM & UI LOADER
local success, rayfield = pcall(function()
    -- Load Rayfield Library from GitHub
    return loadstring(game:HttpGet('https://raw.githubusercontent.com/SiriusSoftware/Rayfield/main/Source.lua'))()
end)

if not success then
    warn("Rayfield Load Failed. Using fallback.")
    -- Fallback simple UI if Rayfield fails
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "HUS_Fallback"
    ScreenGui.Parent = game:GetService("CoreGui")
    
    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 300, 0, 50)
    Frame.Position = UDim2.new(0.5, -150, 0.1, 0)
    Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    Frame.BorderSizePixel = 0
    Frame.Parent = ScreenGui
    
    local TextBox = Instance.new("TextBox")
    TextBox.Size = UDim2.new(1, -20, 1, -10)
    TextBox.Position = UDim2.new(0, 10, 0, 5)
    TextBox.PlaceholderText = "Enter Key..."
    TextBox.BackgroundTransparency = 1
    TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextBox.Font = Enum.Font.GothamBold
    TextBox.TextSize = 18
    TextBox.Parent = Frame
    
    TextBox.FocusLost:Connect(function(enterPressed)
        if enterPressed and TextBox.Text == CONFIG.ValidKey then
            print("[HUS Hub] Key Accepted! Welcome, User.")
            ScreenGui:Destroy()
            loadHub() 
        else
            TextBox.Text = "Invalid Key"
            wait(1)
            TextBox.Text = ""
        end
    end)
else
    -- RAYFIELD UI IMPLEMENTATION
    local Window = rayfield:CreateWindow({
        Name = CONFIG.Title,
        LoadingTitle = "Loading " .. CONFIG.Title .. "...",
        Theme = "Amethyst",
        ConfigurationSaving = { Enabled = true, FolderName = "HUSHub", FileName = "Config" }
    })

    local MainTab = Window:CreateTab("Main Features")
    local VisualsTab = Window:CreateTab("Visuals")

    -- FEATURE: WALKSPEED
    MainTab:CreateSlider({
        Name = "WalkSpeed",
        Min = 16,
        Max = 150,
        Increment = 1,
        CurrentValue = 16,
        Callback = function(Value)
            LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end
    })

    -- FEATURE: TP GRAB
    MainTab:CreateButton({
        Name = "TP Grab Nearest Player",
        Callback = function()
            local nearestPlayer = nil
            local minDistance = math.huge
            
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                    if dist < minDistance then
                        minDistance = dist
                        nearestPlayer = player
                    end
                end
            end
            
            if nearestPlayer then
                LocalPlayer.Character.HumanoidRootPart.CFrame = nearestPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
                print("TP'd to " .. nearestPlayer.Name)
            else
                print("No players nearby!")
            end
        end
    })

    -- FEATURE: NAME CHANGER
    MainTab:CreateInput({
        Name = "RP Name",
        PlaceholderText = "Enter New Name",
        Callback = function(Text)
            if nameRemote then
                pcall(function()
                    nameRemote:FireServer(Text)
                end)
            else
                print("Name Remote not found!")
            end
        end
    })

    -- VISUALS: BIO GLOW
    VisualsTab:CreateToggle({
        Name = "Rainbow Bio Glow",
        CurrentValue = false,
        Callback = function(Value)
            if Value then
                spawn(function()
                    while Value do
                        task.wait(0.1)
                        local head = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Head")
                        if head then
                            local glow = Instance.new("PointLight", head)
                            glow.Color = Color3.fromHSV(math.random(), 1, 1)
                            glow.Range = 50
                            glow.Shadows = true
                            task.wait()
                            glow:Destroy()
                        end
                    end
                end)
            end
        end
    })
end

function loadHub()
    print("[HUS Hub] Loaded Successfully!")
end

loadHub()
