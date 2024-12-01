if game.PlaceId == 4924922222 then
    -- Load
    local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

    -- Main
    local Window = Rayfield:CreateWindow({
        Name = "Vitin Hub 1.02",
        Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
        LoadingTitle = "Zark Hub",
        LoadingSubtitle = "by Sirius",
        Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes
        
        DisableRayfieldPrompts = false,
        DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface
        
        ConfigurationSaving = {
            Enabled = true,
            FolderName = nil, -- Create a custom folder for your hub/game
            FileName = "Big Hub"
        },
        
        Discord = {
            Enabled = false, -- Prompt the user to join your Discord server if their executor supports it
            Invite = "noinvitelink", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ABCD would be ABCD
            RememberJoins = true -- Set this to false to make them join the discord every time they load it up
        },
        
        KeySystem = true, -- Set this to true to use our key system
        KeySettings = {
            Title = "Vitin Hub Key System",
            Subtitle = "Vitin Key",
            Note = "Copy this link to get the key: https://discord.gg/xjxUEkCR", -- Use this to tell the user how to get a key
            FileName = "VitinHubKey", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
            SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
            GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
            Key = {"VitinDev"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
        }
    })

    -- Teleport Functionality
    local function Teleport(location)
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = CFrame.new(location)
        end
    end

    -- Auto Job Functionality
    local function AutoJob()
        while _G.AutoJobEnabled do
            -- Logic to accept and complete jobs automatically
            print("Auto Job is running...")
            wait(5) -- Interval between checks (adjust as necessary)
        end
    end

    -- ESP Functionality
    local function ESP()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = player.Character
                highlight.FillColor = Color3.new(1, 0, 0)
                highlight.OutlineColor = Color3.new(1, 1, 1)
                highlight.Parent = player.Character
            end
        end
    end

    -- Fly Functionality
    local flying = false
    local function Fly()
        local player = game.Players.LocalPlayer
        local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        local bodyVelocity = Instance.new("BodyVelocity")
        local bodyGyro = Instance.new("BodyGyro")
        bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000)
        bodyGyro.MaxTorque = Vector3.new(100000, 100000, 100000)
        bodyGyro.P = 3000

        flying = not flying
        if flying then
            bodyVelocity.Parent = humanoidRootPart
            bodyGyro.Parent = humanoidRootPart
            humanoid.PlatformStand = true

            local flyConnection
            flyConnection = game:GetService("RunService").Heartbeat:Connect(function()
                if flying then
                    local direction = Vector3.new()
                    if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
                        direction = direction + (workspace.CurrentCamera.CFrame.LookVector * 50)
                    end
                    if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
                        direction = direction - (workspace.CurrentCamera.CFrame.LookVector * 50)
                    end
                    if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.A) then
                        direction = direction - (workspace.CurrentCamera.CFrame.RightVector * 50)
                    end
                    if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.D) then
                        direction = direction + (workspace.CurrentCamera.CFrame.RightVector * 50)
                    end
                    bodyVelocity.Velocity = direction
                    bodyGyro.CFrame = workspace.CurrentCamera.CFrame
                else
                    flyConnection:Disconnect()
                    bodyVelocity:Destroy()
                    bodyGyro:Destroy()
                    humanoid.PlatformStand = false
                end
            end)
        else
            bodyVelocity:Destroy()
            bodyGyro:Destroy()
            humanoid.PlatformStand = false
        end
    end

    -- Noclip Functionality
    local noclip = false
    local function Noclip()
        noclip = not noclip
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()

        spawn(function()
            while noclip do
                for _, v in pairs(character:GetDescendants()) do
                    if v:IsA("BasePart") then
                        v.CanCollide = false
                    end
                end
                wait()
            end
            for _, v in pairs(character:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.CanCollide = true
                end
            end
        end)
    end

    -- Give Permission to All Houses Functionality
    local function GivePermission()
        for houseNumber = 1, 37 do
            local args = {
                [1] = "GivePermissionLoopToServer",
                [2] = game:GetService("Players").LocalPlayer,
                [3] = houseNumber
            }

            print("Granting permission for house number:", houseNumber) -- Debugging line

            game:GetService("ReplicatedStorage").RE:FindFirstChild("1Playe1rTrigge1rEven1t"):FireServer(unpack(args))
            print("Permission granted for house number:", houseNumber) -- Debugging line
        end
    end

    -- Create Tabs
    local TeleportTab = Window:CreateTab("Teleport")
    local JobTab = Window:CreateTab("Auto Job")
    local ESPTab = Window:CreateTab("ESP")
    local FlyTab = Window:CreateTab("Fly & Noclip")
    local PermissionTab = Window:CreateTab("Permissions")

    -- Teleport Button
    TeleportTab:CreateButton({
        Name = "Teleport to Spawn",
        Callback = function()
            Teleport(Vector3.new(0, 10, 0)) -- Example coordinates, adjust as necessary
        end
    })

    -- Auto Job Toggle
    JobTab:CreateToggle({
        Name = "Enable Auto Job",
        Default = false,
        Callback = function(Value)
            _G.AutoJobEnabled = Value
            if Value then
                spawn(AutoJob)
            end
        end
    })

    -- ESP Toggle
    ESPTab:CreateToggle({
        Name = "Enable ESP",
        Default = false,
        Callback = function(Value)
            if Value then
                ESP()
            end
        end
    })

    -- Fly Button
    FlyTab:CreateButton({
        Name = "Toggle Fly",
        Callback = function()
            Fly()
        end
    })

    -- Noclip Button
    FlyTab:CreateButton({
        Name = "Toggle Noclip",
        Callback = function()
            Noclip()
        end
    })

    -- Give Permission Button
    PermissionTab:CreateButton({
        Name = "Grant Permission to All Houses",
        Callback = function()
            GivePermission()
        end
    })
end
