local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Local Player
local player = Players.LocalPlayer

-- References to remote events (may vary by server, adjust as necessary)
local RemoteEvents = ReplicatedStorage:WaitForChild("RemoteEvents")

local StealEvent = RemoteEvents:WaitForChild("Steal") -- Hypothetical event name for stealing
local TransferEvent = RemoteEvents:WaitForChild("TransferKnife") -- Hypothetical event for transfer

-- UI Creation --
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MM2StealTransferGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 300, 0, 150)
Frame.Position = UDim2.new(0, 50, 0, 50)
Frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.Text = "MM2 Auto Steal & Transfer"
Title.TextColor3 = Color3.new(1,1,1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 20
Title.Parent = Frame

local stealToggle = Instance.new("TextButton")
stealToggle.Size = UDim2.new(0, 120, 0, 40)
stealToggle.Position = UDim2.new(0, 10, 0, 50)
stealToggle.Text = "Toggle Auto-Steal: OFF"
stealToggle.BackgroundColor3 = Color3.fromRGB(50,50,50)
stealToggle.TextColor3 = Color3.new(1,1,1)
stealToggle.Font = Enum.Font.SourceSans
stealToggle.TextSize = 18
stealToggle.Parent = Frame

local transferToggle = Instance.new("TextButton")
transferToggle.Size = UDim2.new(0, 120, 0, 40)
transferToggle.Position = UDim2.new(0, 150, 0, 50)
transferToggle.Text = "Toggle Auto-Transfer: OFF"
transferToggle.BackgroundColor3 = Color3.fromRGB(50,50,50)
transferToggle.TextColor3 = Color3.new(1,1,1)
transferToggle.Font = Enum.Font.SourceSans
transferToggle.TextSize = 18
transferToggle.Parent = Frame

-- Caixa de texto para inserir o nome da conta que receberá as facas e armas
local recipientBox = Instance.new("TextBox")
recipientBox.Size = UDim2.new(1, -20, 0, 30)
recipientBox.Position = UDim2.new(0, 10, 0, 100)
recipientBox.PlaceholderText = "Enter recipient username"
recipientBox.BackgroundColor3 = Color3.fromRGB(40,40,40)
recipientBox.TextColor3 = Color3.new(1,1,1)
recipientBox.Font = Enum.Font.SourceSans
recipientBox.TextSize = 16
recipientBox.ClearTextOnFocus = false
recipientBox.Parent = Frame

-- Variables
local autoSteal = false
local autoTransfer = false

-- Functions to send steal and transfer requests (These are placeholders,
-- Please adjust the exact remote event names and parameters according to the MM2 game environment.)
local function stealWeaponsAndKnives()
    -- Example of firing remote event to steal a knife or weapon.
    -- The actual arguments must match MM2's back-end API.
    pcall(function()
        StealEvent:FireServer()
    end)
end

local function transferKnivesAndWeaponsTo(recipient)
    -- Example sending transfer request for items.
    -- The actual parameters depend on game APIs.
    pcall(function()
        TransferEvent:FireServer(recipient)
    end)
end

-- Button events
stealToggle.MouseButton1Click:Connect(function()
    autoSteal = not autoSteal
    stealToggle.Text = "Toggle Auto-Steal: " .. (autoSteal and "ON" or "OFF")
end)

transferToggle.MouseButton1Click:Connect(function()
    autoTransfer = not autoTransfer
    transferToggle.Text = "Toggle Auto-Transfer: " .. (autoTransfer and "ON" or "OFF")
end)

-- Main loop for automation
spawn(function()
    while true do
        wait(1) -- delay between each cycle
        if autoSteal then
            stealWeaponsAndKnives()
        end
        if autoTransfer then
            local recipientName = recipientBox.Text
            if recipientName and recipientName ~= "" then
                transferKnivesAndWeaponsTo(recipientName)
            end
        end
    end
end)

-- Draggable Frame
local UserInputService = game:GetService("User InputService")
local runService = game:GetService("RunService")

local dragging = false
local dragInput
local dragStart
local startPos

local function updateFrame(input)
    local delta = input.Position - dragStart
    Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
                              startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

Frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Frame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

User InputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        updateFrame(input)
    end
end)

print("MM2 Auto Steal & Transfer script loaded successfully.")
