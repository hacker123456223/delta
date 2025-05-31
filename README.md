local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")

-- GUI erstellen
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = StarterGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 150, 0, 50)
ToggleButton.Position = UDim2.new(0, 10, 0, 320)
ToggleButton.Text = "GUI anzeigen"
ToggleButton.Parent = ScreenGui

local PlayerList = Instance.new("Frame")
PlayerList.Size = UDim2.new(0, 200, 0, 300)
PlayerList.Position = UDim2.new(0, 10, 0, 10)
PlayerList.Parent = ScreenGui
PlayerList.Visible = false -- Startet versteckt

ToggleButton.MouseButton1Click:Connect(function()
    PlayerList.Visible = not PlayerList.Visible
    ToggleButton.Text = PlayerList.Visible and "GUI schließen" or "GUI anzeigen"
end)

-- GUI für Spieleraktionen
local function ShowPlayerGui(player)
    local PlayerGui = Instance.new("Frame")
    PlayerGui.Size = UDim2.new(0, 300, 0, 400)
    PlayerGui.Position = UDim2.new(0, 220, 0, 10)
    PlayerGui.Parent = ScreenGui
    
    local CloseButton = Instance.new("TextButton")
    CloseButton.Size = UDim2.new(0, 100, 0, 40)
    CloseButton.Position = UDim2.new(1, -110, 0, 10)
    CloseButton.Text = "Schließen"
    CloseButton.Parent = PlayerGui
    
    CloseButton.MouseButton1Click:Connect(function()
        PlayerGui:Destroy()
    end)

    local Avatar = Instance.new("ImageLabel")
    Avatar.Size = UDim2.new(0, 100, 0, 100)
    Avatar.Position = UDim2.new(0, 10, 0, 10)
    Avatar.Image = "rbxthumb://type=AvatarHeadShot&id="..player.UserId.."&w=100&h=100"
    Avatar.Parent = PlayerGui

    local NameLabel = Instance.new("TextLabel")
    NameLabel.Size = UDim2.new(1, -20, 0, 30)
    NameLabel.Position = UDim2.new(0, 10, 0, 120)
    NameLabel.Text = "Name: " .. player.Name
    NameLabel.Parent = PlayerGui

    local HealthLabel = Instance.new("TextLabel")
    HealthLabel.Size = UDim2.new(1, -20, 0, 30)
    HealthLabel.Position = UDim2.new(0, 10, 0, 160)
    HealthLabel.Text = "Leben: " .. (player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health or "N/A")
    HealthLabel.Parent = PlayerGui

    -- Funktionen für Buttons
    local function ResetPlayer()
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.Health = 0
        end
    end

    local function RemoveTools()
        if player.Backpack then
            for _, tool in ipairs(player.Backpack:GetChildren()) do
                if tool:IsA("Tool") or tool:IsA("HopperBin") then
                    tool.Parent = nil
                end
            end
        end
    end

    local function JumpPlayer()
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.Jump = true
        end
    end

    local function SitPlayer()
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.Sit = true
        end
    end

    local function KickPlayer()
        if player then
            player:Kick("Du wurdest aus dem Spiel entfernt.")
        end
    end

    -- Buttons erstellen
    local actions = {
        {"Reset", ResetPlayer},
        {"Werkzeuge entfernen", RemoveTools},
        {"Springen", JumpPlayer},
        {"Sitzen", SitPlayer},
        {"Kick", KickPlayer}
    }

    for i, action in ipairs(actions) do
        local ActionButton = Instance.new("TextButton")
        ActionButton.Size = UDim2.new(0.8, 0, 0, 40)
        ActionButton.Position = UDim2.new(0.1, 0, 0, 200 + (i * 45))
        ActionButton.Text = action[1]
        ActionButton.Parent = PlayerGui
        
        ActionButton.MouseButton1Click:Connect(action[2])
    end
end

-- Spielerliste aktualisieren
local function UpdatePlayerList()
    for _, player in ipairs(Players:GetPlayers()) do
        local Button = Instance.new("TextButton")
        Button.Text = player.Name
        Button.Size = UDim2.new(1, 0, 0, 30)
        Button.Parent = PlayerList
        
        Button.MouseButton1Click:Connect(function()
            ShowPlayerGui(player)
        end)
    end
end

UpdatePlayerList()

Players.PlayerAdded:Connect(UpdatePlayerList)
Players.PlayerRemoving:Connect(UpdatePlayerList)
