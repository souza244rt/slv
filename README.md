# local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local AimbotEnabled = false
local AimbotKey = Enum.KeyCode.E
local AimFOV = 100 -- Campo de visão ajustável

function GetClosestEnemy()
    local closestEnemy = nil
    local shortestDistance = AimFOV
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team then
            local character = player.Character
            if character and character:FindFirstChild("Head") then
                local headPosition, onScreen = Camera:WorldToViewportPoint(character.Head.Position)
                if onScreen then
                    local mousePos = UserInputService:GetMouseLocation()
                    local distance = (Vector2.new(headPosition.X, headPosition.Y) - mousePos).Magnitude
                    
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestEnemy = character.Head
                    end
                end
            end
        end
    end
    return closestEnemy
end

UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == AimbotKey then
        AimbotEnabled = true
    end
end)

UserInputService.InputEnded:Connect(function(input, processed)
    if input.KeyCode == AimbotKey then
        AimbotEnabled = false
    end
end)

RunService.RenderStepped:Connect(function()
    if AimbotEnabled then
        local target = GetClosestEnemy()
        if target then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
        end
    end
end)
