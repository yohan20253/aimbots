local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- Configurações
local SPEED = 16
local JUMP_POWER = humanoid.JumpPower
local RAY_LENGTH = 5

-- Controlador da AI
local enabled = false

print("Pressione 'C' para ligar/desligar o AutoParkour AI")

-- Função para detectar chão na frente
local function hasGroundAhead()
    local rayOrigin = rootPart.Position + (rootPart.CFrame.LookVector * 2)
    local rayDirection = Vector3.new(0, -RAY_LENGTH, 0)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {character}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

    local rayResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
    return rayResult and rayResult.Instance ~= nil
end

-- Função para detectar obstáculo na frente
local function isObstacleAhead()
    local rayOrigin = rootPart.Position
    local rayDirection = rootPart.CFrame.LookVector * 3
    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {character}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

    local rayResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
    return rayResult and rayResult.Instance ~= nil
end

-- Alterna o estado do bot ao pressionar a tecla C
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.C then
        enabled = not enabled
        print("AutoParkour AI " .. (enabled and "ativado" or "desativado"))
    end
end)

-- Loop principal do bot
RunService.Heartbeat:Connect(function()
    if not enabled then return end
    if humanoid.Health <= 0 then return end

    -- Move para frente
    humanoid:Move(Vector3.new(0, 0, -1), false)

    -- Detecta chão e obstáculo
    if not hasGroundAhead() then
        humanoid.Jump = true
    elseif isObstacleAhead() then
        humanoid.Jump = true
    end
end)
