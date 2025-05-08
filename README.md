local PhysicsService = game:GetService("PhysicsService")
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
local frame = Instance.new("Frame")
local toggleButton = Instance.new("TextButton") -- Botão móvel
local textBox = Instance.new("TextBox")
local applyButton = Instance.new("TextButton")
local resetButton = Instance.new("TextButton")
local transparencyButton = Instance.new("TextButton")
local sizeLabel = Instance.new("TextLabel")
local dragging = false
local dragInput = nil
local dragStart = nil
local startPos = nil
local isTransparent = false

screenGui.Parent = player.PlayerGui
frame.Parent = screenGui
frame.Size = UDim2.new(0.3, 0, 0.4, 0)
frame.Position = UDim2.new(0.35, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.Visible = false -- Inicialmente escondido

-- Botão móvel para abrir/fechar menu
toggleButton.Parent = screenGui
toggleButton.Size = UDim2.new(0.15, 0, 0.08, 0)
toggleButton.Position = UDim2.new(0.05, 0, 0.05, 0)
toggleButton.Text = "Menu"
toggleButton.TextScaled = true
toggleButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.Active = true
toggleButton.Draggable = true -- Permitir movimentação

-- Configurar movimentação do botão móvel
toggleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = toggleButton.Position
    end
end)

toggleButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

toggleButton.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        toggleButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Configurar elementos da interface
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

-- Configuração dos grupos de colisão
local function setupCollisionGroups()
    PhysicsService:CreateCollisionGroup("PlayerHitbox")
    PhysicsService:CreateCollisionGroup("PhysicalObjects") -- Grupo para Part e MeshPart

    PhysicsService:CollisionGroupSetCollidable("PlayerHitbox", "PhysicalObjects", true)
    PhysicsService:CollisionGroupSetCollidable("PlayerHitbox", "PlayerHitbox", false)
    PhysicsService:CollisionGroupSetCollidable("PlayerHitbox", "Default", false)
end

setupCollisionGroups()

-- Aplicar grupo de colisão automaticamente a Part e MeshPart
local function setCollisionForObjects()
    for _, obj in pairs(game.Workspace:GetDescendants()) do
        if obj:IsA("Part") or obj:IsA("MeshPart") then
            obj.CollisionGroup = "PhysicalObjects"
        end
    end
end

setCollisionForObjects()

-- Função para abrir/fechar o menu com debug
local function toggleMenu()
    frame.Visible = not frame.Visible
    toggleButton.Text = frame.Visible and "Fechar Menu" or "Abrir Menu"
    print("Menu agora está:", frame.Visible and "Aberto" or "Fechado")
end

toggleButton.MouseButton1Click:Connect(toggleMenu)
applyButton.MouseButton1Click:Connect(toggleMenu)
resetButton.MouseButton1Click:Connect(toggleMenu)
transparencyButton.MouseButton1Click:Connect(toggleMenu)
