local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "JAKARTA_HUB"
ScreenGui.Parent = game.CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Size = UDim2.new(0, 300, 0, 400)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
MainFrame.Active = true
MainFrame.Draggable = true

local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = MainFrame
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.Position = UDim2.new(1, -20, 0, 0)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.SourceSans
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14
CloseButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
end)

local ScrollingFrame = Instance.new("ScrollingFrame")
ScrollingFrame.Parent = MainFrame
ScrollingFrame.Size = UDim2.new(1, 0, 1, -40)
ScrollingFrame.Position = UDim2.new(0, 0, 0, 40)
ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollingFrame.ScrollBarThickness = 8
ScrollingFrame.BackgroundTransparency = 1
ScrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollingFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 5)

local LogoButton = Instance.new("TextButton")
LogoButton.Name = "LogoButton"
LogoButton.Parent = ScreenGui
LogoButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
LogoButton.Position = UDim2.new(0, 10, 0, 10)
LogoButton.Size = UDim2.new(0, 50, 0, 50)
LogoButton.Font = Enum.Font.Arcade
LogoButton.Text = "JKT"
LogoButton.TextSize = 24
LogoButton.TextColor3 = Color3.fromRGB(0, 0, 0)
LogoButton.Visible = true
LogoButton.Active = true
LogoButton.Draggable = true
LogoButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
end)

local function createButton(name, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollingFrame
    Button.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    Button.Size = UDim2.new(1, 0, 0, 40)
    Button.Font = Enum.Font.SourceSans
    Button.Text = name .. " [OFF]"
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 20
    
    local isActive = false
    Button.MouseButton1Click:Connect(function()
        isActive = not isActive
        Button.Text = name .. (isActive and " [ON]" or " [OFF]")
        Button.BackgroundColor3 = isActive and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        callback(isActive)
    end)
end

local function createSlider(name, min, max, callback)
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Parent = ScrollingFrame
    SliderFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    SliderFrame.Size = UDim2.new(1, 0, 0, 40)
    
    local SliderLabel = Instance.new("TextLabel")
    SliderLabel.Parent = SliderFrame
    SliderLabel.Size = UDim2.new(1, -20, 0, 20)
    SliderLabel.Position = UDim2.new(0, 10, 0, 0)
    SliderLabel.Text = name .. " (" .. min .. ")"
    SliderLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    SliderLabel.TextSize = 14
    
    local Slider = Instance.new("TextBox")
    Slider.Parent = SliderFrame
    Slider.Size = UDim2.new(0, 40, 0, 20)
    Slider.Position = UDim2.new(1, -50, 0, 0)
    Slider.Text = tostring(min)
    Slider.FocusLost:Connect(function()
        local value = tonumber(Slider.Text)
        if value and value >= min and value <= max then
            SliderLabel.Text = name .. " (" .. value .. ")"
            callback(value)
        else
            Slider.Text = tostring(min)
        end
    end)
end

createButton("God Mode", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.Humanoid.MaxHealth = math.huge
        game.Players.LocalPlayer.Character.Humanoid.Health = math.huge
    else
        game.Players.LocalPlayer.Character.Humanoid.MaxHealth = 100
        game.Players.LocalPlayer.Character.Humanoid.Health = 100
    end
end)

createButton("Infinity Jump", function(isActive)
    local jumpConnection
    if isActive then
        jumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
            game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
        end)
    else
        if jumpConnection then jumpConnection:Disconnect() end
    end
end)

createButton("Speed Boost", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 100
    else
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end)

createButton("Fly", function(isActive)
    local flySpeed = 50
    local bodyGyro, bodyVelocity

    local function startFlying()
        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.P = 9e4
        bodyGyro.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart

        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
        bodyVelocity.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart

        game:GetService("RunService").RenderStepped:Connect(function()
            if bodyGyro and bodyVelocity then
                bodyGyro.CFrame = game.Workspace.CurrentCamera.CFrame
                bodyVelocity.Velocity = game.Workspace.CurrentCamera.CFrame.LookVector * flySpeed
            end
        end)
    end

    local function stopFlying()
        if bodyGyro then bodyGyro:Destroy() end
        if bodyVelocity then bodyVelocity:Destroy() end
    end

    if isActive then
        startFlying()
    else
        stopFlying()
    end
end)

createButton("Noclip", function(isActive)
    local noclipConnection

    local function startNoclip()
        noclipConnection = game:GetService("RunService").Stepped:Connect(function()
            for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end)
    end

    local function stopNoclip()
        if noclipConnection then noclipConnection:Disconnect() end
    end

    if isActive then
        startNoclip()
    else
        stopNoclip()
    end
end)

createButton("Teleport to Random Player", function(isActive)
    if isActive then
        local players = game.Players:GetPlayers()
        local randomPlayer = players[math.random(#players)]
        if randomPlayer.Character and randomPlayer.Character:FindFirstChild("HumanoidRootPart") then
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = randomPlayer.Character.HumanoidRootPart.CFrame
        end
    end
end)

createSlider("WalkSpeed", 16, 100, function(value)
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value
end)

createSlider("JumpPower", 50, 200, function(value)
    game.Players.LocalPlayer.Character.Humanoid.JumpPower = value
end)

createSlider("Gravity", 0, 196.2, function(value)
    game.Workspace.Gravity = value
end)

createButton("ESP", function(isActive)
    local function createESP(player)
        local Box = Instance.new("BoxHandleAdornment")
        Box.Name = "ESP"
        Box.Adornee = player.Character
        Box.Size = player.Character:GetExtentsSize()
        Box.Color3 = Color3.new(1, 0, 0)
        Box.AlwaysOnTop = true
        Box.ZIndex = 5
        Box.Transparency = 0.7
        Box.Parent = player.Character
    end
    
    if isActive then
        for _, v in pairs(game.Players:GetPlayers()) do
            if v ~= game.Players.LocalPlayer and v.Character then
                createESP(v)
            end
        end
    else
        for _, v in pairs(game.Players:GetPlayers()) do
            if v ~= game.Players.LocalPlayer and v.Character then
                if v.Character:FindFirstChild("ESP") then
                    v.Character.ESP:Destroy()
                end
            end
        end
    end
end)

createButton("Kill Aura", function(isActive)
    local killAuraRadius = 100
    local function hasWeapon()
        for _, item in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
            if item:IsA("Tool") then
                return true
            end
        end
        for _, item in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if item:IsA("Tool") then
                return true
            end
        end
        return false
    end
    
    local function startKillAura()
        while isActive do
            if hasWeapon() then
                for _, targetPlayer in pairs(game.Players:GetPlayers()) do
                    if targetPlayer ~= game.Players.LocalPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChildOfClass("Humanoid") then
                        local distance = (targetPlayer.Character.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                        if distance < killAuraRadius then
                            pcall(function()
                                game:GetService("ReplicatedStorage").Events.Damage:FireServer(targetPlayer.Character.Humanoid)
                            end)
                        end
                    end
                end
            end
            wait(0.1)
        end
    end
    
    if isActive then
        startKillAura()
    end
end)

createButton("Invisible", function(isActive)
    if isActive then
        for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 1
            end
        end
    else
        for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 0
            end
        end
    end
end)

createButton("Super Strength", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.Humanoid.Health = game.Players.LocalPlayer.Character.Humanoid.Health * 10
    else
        game.Players.LocalPlayer.Character.Humanoid.Health = game.Players.LocalPlayer.Character.Humanoid.Health / 10
    end
end)

createSlider("Field of View", 70, 120, function(value)
    game.Workspace.CurrentCamera.FieldOfView = value
end)

createButton("Remove Limbs", function(isActive)
    if isActive then
        for _, limb in pairs({"LeftArm", "RightArm", "LeftLeg", "RightLeg"}) do
            if game.Players.LocalPlayer.Character:FindFirstChild(limb) then
                game.Players.LocalPlayer.Character[limb]:Destroy()
            end
        end
    end
end)

createButton("Auto Heal", function(isActive)
    while isActive do
        if game.Players.LocalPlayer.Character.Humanoid.Health < 100 then
            game.Players.LocalPlayer.Character.Humanoid.Health = 100
        end
        wait(1)
    end
end)

createButton("No Fall Damage", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Freefall, false)
    else
        game.Players.LocalPlayer.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Freefall, true)
    end
end)

createButton("Anti Ragdoll", function(isActive)
    if isActive then
        for _, v in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
            if v:IsA("HingeConstraint") or v:IsA("BallSocketConstraint") then
                v:Destroy()
            end
        end
    end
end)

createButton("Teleport to Spawn", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Workspace.SpawnLocation.CFrame
    end
end)

createButton("Infinite Stamina", function(isActive)
    if isActive then
        game.Players.LocalPlayer.Character.Humanoid.MaxStamina = math.huge
    else
        game.Players.LocalPlayer.Character.Humanoid.MaxStamina = 100
    end
end)

createButton("Night Vision", function(isActive)
    if isActive then
        game.Lighting.Brightness = 2
        game.Lighting.TimeOfDay = "00:00:00"
    else
        game.Lighting.Brightness = 1
        game.Lighting.TimeOfDay = "14:00:00"
    end
end)