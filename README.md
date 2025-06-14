-- Neon Red ESP para todos os jogadores no servidor Roblox
-- Este script deve ser executado no lado do cliente (local script)
-- Ele destaca todos os jogadores com uma cor vermelha neon, visível através das paredes

local ESP_COLOR = Color3.fromRGB(255, 0, 0) -- Vermelho neon
local ESP_TRANSPARENCY = 0.5 -- Transparência para não tampar tudo
local ESP_MATERIAL = Enum.Material.Neon

-- Função para aplicar o efeito ESP em um personagem
local function applyESP(character)
    for _, part in ipairs(character:GetDescendants()) do
        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
            -- Evita modificar partes invisíveis e a HumanoidRootPart
            part.Material = ESP_MATERIAL
            part.Color = ESP_COLOR
            part.Transparency = ESP_TRANSPARENCY
            part.CanCollide = part.CanCollide -- Força atualização visual
            -- Garante que o Highlight seja destruído se já existir
            if part:FindFirstChild("NeonESPHighlight") then
                part.NeonESPHighlight:Destroy()
            end
        end
    end
    -- Adiciona Highlight (contorno visível através das paredes)
    if not character:FindFirstChild("NeonESPHighlight") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "NeonESPHighlight"
        highlight.FillColor = ESP_COLOR
        highlight.OutlineColor = ESP_COLOR
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        highlight.Adornee = character
        highlight.Parent = character
    end
end

-- Aplica ESP em todos os jogadores existentes e nos que entrarem depois
local function applyESPToAll()
    for _, player in ipairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            applyESP(player.Character)
        end
        player.CharacterAdded:Connect(function(char)
            applyESP(char)
        end)
    end
end

-- Atualiza ESP quando novos jogadores entram
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(char)
        applyESP(char)
    end)
end)

-- Atualiza ESP periodicamente para garantir que permanece
while true do
    applyESPToAll()
    wait(2)
end
