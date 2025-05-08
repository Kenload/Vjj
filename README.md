-- Criar GUI universal
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
local frame = Instance.new("Frame")
local textBox = Instance.new("TextBox")
local applyButton = Instance.new("TextButton")
local resetButton = Instance.new("TextButton")
local transparencyButton = Instance.new("TextButton")
local sizeLabel = Instance.new("TextLabel")

screenGui.Parent = player.PlayerGui
frame.Parent = screenGui
frame.Size = UDim2.new(0.3, 0, 0.4, 0) -- Ajustado para telas de diferentes dispositivos
frame.Position = UDim2.new(0.35, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

-- Configurar botões e caixa de texto
local function setupUIElement(element, parent, text, position, size)
    element.Parent = parent
    element.Size = size
    element.Position = position
    element.Text = text
    element.TextScaled = true
    element.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    element.TextColor3 = Color3.new(1, 1, 1)
    element.Font = Enum.Font.SourceSansBold
end

setupUIElement(textBox, frame, "Digite o tamanho", UDim2.new(0, 0, 0.05, 0), UDim2.new(1, 0, 0.2, 0))
setupUIElement(applyButton, frame, "Aplicar Tamanho", UDim2.new(0, 0, 0.3, 0), UDim2.new(1, 0, 0.2, 0))
setupUIElement(resetButton, frame, "Resetar Hitbox", UDim2.new(0, 0, 0.55, 0), UDim2.new(1, 0, 0.2, 0))
setupUIElement(transparencyButton, frame, "Alternar Transparência", UDim2.new(0, 0, 0.75, 0), UDim2.new(1, 0, 0.2, 0))

sizeLabel.Parent = frame
sizeLabel.Size = UDim2.new(1, 0, 0.15, 0)
sizeLabel.Position = UDim2.new(0, 0, 0.9, 0)
sizeLabel.Text = "Tamanho: Padrão"
sizeLabel.TextScaled = true
sizeLabel.TextColor3 = Color3.new(1, 1, 1)
sizeLabel.BackgroundTransparency = 1
sizeLabel.Font = Enum.Font.SourceSansBold

-- Valores padrão
local defaultSize = Vector3.new(2, 2, 1)
local isTransparent = false

-- Função para modificar a hitbox com valor digitado
local function updateHitboxSize()
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local hitbox = character.HumanoidRootPart
        local newSize = tonumber(textBox.Text)
        
        if newSize and newSize > 0 then
            hitbox.Size = Vector3.new(newSize, newSize, newSize)
            sizeLabel.Text = "Tamanho: " .. tostring(hitbox.Size)
        else
            textBox.Text = "Valor Inválido!"
        end
    end
end

-- Função para resetar a hitbox
local function resetHitboxSize()
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local hitbox = character.HumanoidRootPart
        hitbox.Size = defaultSize
        sizeLabel.Text = "Tamanho: Padrão"
    end
end

-- Função para alternar transparência da hitbox
local function toggleTransparency()
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local hitbox = character.HumanoidRootPart
        isTransparent = not isTransparent
        hitbox.Transparency = isTransparent and 0.5 or 0
        transparencyButton.Text = isTransparent and "Transparência: ON" or "Transparência: OFF"
    end
end

applyButton.MouseButton1Click:Connect(updateHitboxSize)
resetButton.MouseButton1Click:Connect(resetHitboxSize)
transparencyButton.MouseButton1Click:Connect(toggleTransparency)
