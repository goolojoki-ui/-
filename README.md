# -
-- Survive the Night in the Mega Store Delta Version
-- Infinity Jump, Speed Hack, TP к аптечкам и еде (адаптация для Delta)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Быстрый UI (Delta поддерживает basic GUI)
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "DeltaMegaStoreGui"

local function makeButton(text, pos, callback)
    local btn = Instance.new("TextButton", ScreenGui)
    btn.Size = UDim2.new(0, 200, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, pos)
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 22
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- Infinity Jump
local infJump = false
makeButton("Infinity Jump (Toggle)", 10, function()
    infJump = not infJump
end)

UserInputService.JumpRequest:Connect(function()
    if infJump and LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- Speed Hack
local speedValue = 16
makeButton("Speed Up (↑)", 60, function()
    speedValue = math.clamp(speedValue + 5, 16, 100)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = speedValue
    end
end)
makeButton("Speed Down (↓)", 110, function()
    speedValue = math.clamp(speedValue - 5, 0, 100)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = speedValue
    end
end)

-- Телепорт к предметам
local function teleportItems(itemNames)
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("Model") and table.find(itemNames, v.Name) then
            local hrp = v:FindFirstChild("HumanoidRootPart") or v.PrimaryPart
            if hrp and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = hrp.CFrame + Vector3.new(0, 3, 0)
                task.wait(0.3)
            end
        end
    end
end

makeButton("TP к аптечкам", 160, function()
    teleportItems({"MedKit", "Bandage"})
end)
makeButton("TP к еде", 210, function()
    teleportItems({"Burger", "HotDog"})
end)

-- Анти-краш: сброс скорости при смерти
LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild("Humanoid").WalkSpeed = speedValue
end)
