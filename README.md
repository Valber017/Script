-- Script avançado de ESP e aimbot para PvP Krush no Roblox

-- Configurações de cor e outros parâmetros
local arrowColor = Color3.fromRGB(0, 0, 255) -- Azul
local aimPart = "Head" -- Parte do corpo para mirar
local oneShotKill = true -- Eliminar com um tiro

-- Variável para ativar/desativar ESP e aimbot
_G.ESPToggle = false
_G.AimbotToggle = false

-- Serviços e jogador local
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")

-- Função para obter o personagem do jogador
local function getCharacter(player)
    return Workspace:FindFirstChild(player.Name)
end

-- Adicionar seta aos inimigos
local function addArrowToEnemy(player, character)
    if player == LocalPlayer then return end -- Ignorar o jogador local
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if humanoidRootPart and not humanoidRootPart:FindFirstChild("Arrow") then
        local arrow = Instance.new("BillboardGui")
        arrow.Name = "Arrow"
        arrow.Adornee = humanoidRootPart
        arrow.Size = UDim2.new(1, 0, 1, 0)
        arrow.AlwaysOnTop = true

        local arrowImage = Instance.new("ImageLabel", arrow)
        arrowImage.Size = UDim2.new(1, 0, 1, 0)
        arrowImage.BackgroundTransparency = 1
        arrowImage.Image = "rbxassetid://YOUR_ARROW_IMAGE_ID" -- Substitua pelo ID da imagem da seta
        arrowImage.ImageColor3 = arrowColor

        arrow.Parent = humanoidRootPart
    end
end

-- Remover seta dos inimigos
local function removeArrowFromEnemy(character)
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if humanoidRootPart then
        local arrowInstance = humanoidRootPart:FindFirstChild("Arrow")
        if arrowInstance then
            arrowInstance:Destroy()
        end
    end
end

-- Função de aimbot
local function aimAt(target)
    if not target then return end
    local camera = Workspace.CurrentCamera
    camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
end

-- Atualizar destaques e aimbot com base no valor de _G.ESPToggle e _G.AimbotToggle
local function updateFeatures()
    for _, player in pairs(Players:GetPlayers()) do
        local character = getCharacter(player)
        if character then
            if _G.ESPToggle then
                addArrowToEnemy(player, character)
            else
                removeArrowFromEnemy(character)
            end

            if _G.AimbotToggle and player.Team ~= LocalPlayer.Team then
                local targetPart = character:FindFirstChild(aimPart)
                if targetPart then
                    aimAt(targetPart)
                    if oneShotKill then
                        -- Simular um tiro
                        local args = {
                            [1] = targetPart.Position,
                            [2] = targetPart
                        }
                        game:GetService("ReplicatedStorage").Events.Shoot:FireServer(unpack(args))
                    end
                end
            end
        end
    end
end

-- Conectar a função de atualização ao evento de renderização
RunService.RenderStepped:Connect(updateFeatures)

-- Alternar ESP e aimbot ao pressionar a tela (toque)
UserInputService.TouchTap:Connect(function()
    _G.ESPToggle = not _G.ESPToggle
    _G.AimbotToggle = not _G.AimbotToggle
end)
