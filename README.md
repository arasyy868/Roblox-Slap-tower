local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- Ensure PlayerGui is loaded
local PlayerGui = Player:WaitForChild("PlayerGui", 10)
if not PlayerGui then
    warn("PlayerGui could not be loaded!")
    return
end

-- Create GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SlapTowerGUI"
ScreenGui.ResetOnSpawn = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Enabled = true
ScreenGui.Parent = PlayerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 290)
Frame.Position = UDim2.new(0.5, -100, 0.5, -145)
Frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Frame.BorderSizePixel = 2
Frame.Active = true
Frame.Selectable = true
Frame.ZIndex = 10
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "Slap Tower "
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.TextScaled = true
Title.ZIndex = 11
Title.Parent = Frame

-- Speed Boost Button
local SpeedButton = Instance.new("TextButton")
SpeedButton.Size = UDim2.new(0.8, 0, 0, 40)
SpeedButton.Position = UDim2.new(0.1, 0, 0.15, 0)
SpeedButton.Text = "Increase Speed (x2)"
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
SpeedButton.TextScaled = true
SpeedButton.ZIndex = 11
SpeedButton.Parent = Frame

-- Double Jump Button
local DoubleJumpButton = Instance.new("TextButton")
DoubleJumpButton.Size = UDim2.new(0.8, 0, 0, 40)
DoubleJumpButton.Position = UDim2.new(0.1, 0, 0.30, 0)
DoubleJumpButton.Text = "Jump"
DoubleJumpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
DoubleJumpButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
DoubleJumpButton.TextScaled = true
DoubleJumpButton.ZIndex = 11
DoubleJumpButton.Parent = Frame

-- Teleport to End Button
local EndTeleportButton = Instance.new("TextButton")
EndTeleportButton.Size = UDim2.new(0.8, 0, 0, 40)
EndTeleportButton.Position = UDim2.new(0.1, 0, 0.45, 0)
EndTeleportButton.Text = "Teleport to End"
EndTeleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
EndTeleportButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
EndTeleportButton.TextScaled = true
EndTeleportButton.ZIndex = 11
EndTeleportButton.Parent = Frame

-- Sequential Teleport Button
local SequentialTeleportButton = Instance.new("TextButton")
SequentialTeleportButton.Size = UDim2.new(0.8, 0, 0, 40)
SequentialTeleportButton.Position = UDim2.new(0.1, 0, 0.60, 0)
SequentialTeleportButton.Text = "Sequential Teleport: Off"
SequentialTeleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SequentialTeleportButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
SequentialTeleportButton.TextScaled = true
SequentialTeleportButton.ZIndex = 11
SequentialTeleportButton.Parent = Frame

-- Spy Menu Button
local SpyMenuButton = Instance.new("TextButton")
SpyMenuButton.Size = UDim2.new(0.8, 0, 0, 40)
SpyMenuButton.Position = UDim2.new(0.1, 0, 0.75, 0)
SpyMenuButton.Text = "Spy Menu"
SpyMenuButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpyMenuButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
SpyMenuButton.TextScaled = true
SpyMenuButton.ZIndex = 11
SpyMenuButton.Parent = Frame

-- Spy Menu Frame
local SpyFrame = Instance.new("ScrollingFrame")
SpyFrame.Size = UDim2.new(0, 200, 0, 200)
SpyFrame.Position = UDim2.new(1.1, 0, 0, 0)
SpyFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
SpyFrame.BorderSizePixel = 2
SpyFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
SpyFrame.ScrollBarThickness = 8
SpyFrame.Visible = false
SpyFrame.ZIndex = 11
SpyFrame.Parent = Frame

local SpyTitle = Instance.new("TextLabel")
SpyTitle.Size = UDim2.new(1, 0, 0, 30)
SpyTitle.Text = "Spy on Players"
SpyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
SpyTitle.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
SpyTitle.TextScaled = true
SpyTitle.ZIndex = 12
SpyTitle.Parent = SpyFrame

-- Red × Button (For spying)
local SpyCloseButton = Instance.new("TextButton")
SpyCloseButton.Size = UDim2.new(0, 40, 0, 40)
SpyCloseButton.Position = UDim2.new(1, -50, 0, 10)
SpyCloseButton.Text = "×"
SpyCloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpyCloseButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
SpyCloseButton.TextScaled = true
SpyCloseButton.Visible = false
SpyCloseButton.ZIndex = 12
SpyCloseButton.Parent = ScreenGui

-- Sequential Teleport Variables
local sequentialTeleportEnabled = false
local sequentialTeleportConnection = nil
local currentCoordIndex = 1
local sequentialCoords = {
    Vector3.new(32.75, 168.77, 33.49),
    Vector3.new(33.11, 169.24, 59.72),
    Vector3.new(34.30, 168.78, 46.41)
}

-- Double Jump Variables
local canDoubleJump = false
local hasDoubleJumped = false
local doubleJumpEnabled = false

-- Spy Variables
local spyingPlayer = nil
local originalCamera = workspace.CurrentCamera

-- Functions
local function teleportToEnd()
    local endPos = Vector3.new(-185.69, 771.64, 51.45)
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.CFrame = CFrame.new(endPos)
    else
        warn("Character or HumanoidRootPart not found for teleportToEnd")
    end
end

local function sequentialTeleport()
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        local targetPos = sequentialCoords[currentCoordIndex]
        Player.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)
        currentCoordIndex = (currentCoordIndex % #sequentialCoords) + 1
        warn("Teleported to: " .. tostring(targetPos)) -- Debug
    else
        warn("Character or HumanoidRootPart not found for sequentialTeleport")
    end
end

local function startSequentialTeleport()
    if sequentialTeleportConnection then
        sequentialTeleportConnection:Disconnect()
    end
    sequentialTeleportConnection = RunService.Heartbeat:Connect(function()
        if sequentialTeleportEnabled then
            sequentialTeleport()
            wait(0.5) -- Fast teleport every 0.5 seconds
        end
    end)
end

local function stopSequentialTeleport()
    if sequentialTeleportConnection then
        sequentialTeleportConnection:Disconnect()
        sequentialTeleportConnection = nil
    end
end

local function increaseSpeed()
    if Player.Character and Player.Character:FindFirstChild("Humanoid") then
        Player.Character.Humanoid.WalkSpeed = Player.Character.Humanoid.WalkSpeed * 2
    else
        warn("Character or Humanoid not found for increaseSpeed")
    end
end

local function onJumpRequest()
    if not Player.Character or not Player.Character:FindFirstChild("Humanoid") then return end
    local humanoid = Player.Character.Humanoid
    if doubleJumpEnabled and canDoubleJump and not hasDoubleJumped and humanoid:GetState() == Enum.HumanoidStateType.Jumping then
        hasDoubleJumped = true
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(0, math.huge, 0)
        bodyVelocity.Velocity = Vector3.new(0, 50, 0)
        bodyVelocity.Parent = Player.Character.HumanoidRootPart
        game:GetService("Debris"):AddItem(bodyVelocity, 0.1)
    end
end

local function onCharacterAdded(character)
    hasDoubleJumped = false
    canDoubleJump = false
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.StateChanged:Connect(function(oldState, newState)
        if newState == Enum.HumanoidStateType.Landed then
            hasDoubleJumped = false
            canDoubleJump = doubleJumpEnabled
        elseif newState == Enum.HumanoidStateType.Freefall and not hasDoubleJumped then
            canDoubleJump = doubleJumpEnabled
        end
    end)
end

local function updateSpyMenu()
    for _, child in pairs(SpyFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    local yOffset = 35
    for _, targetPlayer in pairs(Players:GetPlayers()) do
        if targetPlayer ~= Player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local button = Instance.new("TextButton")
            button.Size = UDim2.new(0.8, 0, 0, 30)
            button.Position = UDim2.new(0.1, 0, 0, yOffset)
            button.Text = targetPlayer.Name
            button.TextColor3 = Color3.fromRGB(255, 255, 255)
            button.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
            button.TextScaled = true
            button.ZIndex = 12
            button.Parent = SpyFrame
            button.MouseButton1Click:Connect(function()
                spyingPlayer = targetPlayer
                SpyFrame.Visible = false
                SpyCloseButton.Visible = true
            end)
            yOffset = yOffset + 35
        end
    end
    SpyFrame.CanvasSize = UDim2.new(0, 0, 0, yOffset)
end

local function stopSpying()
    spyingPlayer = nil
    originalCamera.CameraSubject = Player.Character and Player.Character:FindFirstChild("Humanoid")
    SpyFrame.Visible = false
    SpyCloseButton.Visible = false
end

RunService.RenderStepped:Connect(function()
    if spyingPlayer and spyingPlayer.Character and spyingPlayer.Character:FindFirstChild("HumanoidRootPart") then
        originalCamera.CameraSubject = spyingPlayer.Character.Humanoid
        originalCamera.CFrame = spyingPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 3, 10)
    end
end)

-- Button Click Events
SpeedButton.MouseButton1Click:Connect(increaseSpeed)
EndTeleportButton.MouseButton1Click:Connect(teleportToEnd)
DoubleJumpButton.MouseButton1Click:Connect(function()
    doubleJumpEnabled = not doubleJumpEnabled
    DoubleJumpButton.Text = doubleJumpEnabled and " Jump: On" or " Jump: Off"
    if doubleJumpEnabled and Player.Character then
        canDoubleJump = true
    end
end)
SequentialTeleportButton.MouseButton1Click:Connect(function()
    sequentialTeleportEnabled = not sequentialTeleportEnabled
    SequentialTeleportButton.Text = sequentialTeleportEnabled and "Sequential Teleport: On" or "Sequential Teleport: Off"
    SequentialTeleportButton.BackgroundColor3 = sequentialTeleportEnabled and Color3.fromRGB(50, 255, 50) or Color3.fromRGB(255, 50, 50)
    if sequentialTeleportEnabled then
        startSequentialTeleport()
    else
        stopSequentialTeleport()
    end
end)
SpyMenuButton.MouseButton1Click:Connect(function()
    SpyFrame.Visible = not SpyFrame.Visible
    if SpyFrame.Visible then
        updateSpyMenu()
    end
end)
SpyCloseButton.MouseButton1Click:Connect(stopSpying)

-- Double Jump and Player Tracking
UserInputService.JumpRequest:Connect(onJumpRequest)
Player.CharacterAdded:Connect(onCharacterAdded)
if Player.Character then
    onCharacterAdded(Player.Character)
end
Players.PlayerAdded:Connect(updateSpyMenu)
Players.PlayerRemoving:Connect(updateSpyMenu)

-- Dragging for the Frame
local dragging = false
local dragStart = nil
local startPos = nil

Frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = Frame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        Frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

warn("Slap Tower GUI loaded successfully!")
