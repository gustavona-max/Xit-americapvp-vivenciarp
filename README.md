# Xit-americapvp-vivenciarp
Xit pra jogos dps (Roblox)
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "CustomGui"
gui.Parent = player:WaitForChild("PlayerGui")

-- Criando o Frame principal (tamanho ajustado)
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0.3, 0, 0.45, 0)
frame.Position = UDim2.new(0.35, 0, 0.275, 0)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true
frame.Parent = gui

-- Barra superior
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0.1, 0)
topBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
topBar.BorderSizePixel = 0
topBar.Parent = frame

-- Botão de minimizar
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0.15, 0, 1, 0)
minimizeButton.Position = UDim2.new(0.7, 0, 0, 0)
minimizeButton.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
minimizeButton.Text = "-"
minimizeButton.TextScaled = true
minimizeButton.Parent = topBar

-- Botão de fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0.15, 0, 1, 0)
closeButton.Position = UDim2.new(0.85, 0, 0, 0)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeButton.Text = "X"
closeButton.TextScaled = true
closeButton.Parent = topBar

-- Título para quando estiver minimizado
local minimizedTitle = Instance.new("TextLabel")
minimizedTitle.Size = UDim2.new(1, 0, 0.9, 0)
minimizedTitle.Position = UDim2.new(0, 0, 0.1, 0)
minimizedTitle.BackgroundTransparency = 1
minimizedTitle.Text = "POWERED BY GUZIN"
minimizedTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizedTitle.TextScaled = true
minimizedTitle.Visible = false
minimizedTitle.Parent = frame

-- Criar layout para organizar os botões
local layout = Instance.new("UIListLayout")
layout.Parent = frame
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0.02, 0)

-- Criar função para botões organizados
local function createButton(text, color)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.9, 0, 0.12, 0)
    button.BackgroundColor3 = color
    button.Text = text
    button.TextScaled = true
    button.Parent = frame
    return button
end

-- Criar botões
local aimbotButton = createButton("AIMBOT: DESATIVADO", Color3.fromRGB(0, 255, 0))
local tpClosestButton = createButton("TELEPORTAR PARA PLAYER PRÓXIMO", Color3.fromRGB(0, 0, 255))
local playerNameBox = Instance.new("TextBox")
playerNameBox.Size = UDim2.new(0.9, 0, 0.12, 0)
playerNameBox.PlaceholderText = "Digite o nome do jogador"
playerNameBox.TextScaled = true
playerNameBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
playerNameBox.Parent = frame

local tpByNameButton = createButton("TELEPORTAR PARA PLAYER (NICK)", Color3.fromRGB(0, 255, 255))
local autoLootButton = createButton("Auto Coleta: DESATIVADO", Color3.fromRGB(0, 255, 0))
local espButton = createButton("ESP: DESATIVADO", Color3.fromRGB(0, 255, 0))  -- Botão do ESP

-- Variáveis de estado
local aimbotAtivo = false
local autoLootEnabled = false
local espAtivo = false
local isMinimized = false
local espParts = {}  -- Para armazenar os ESPs criados

-- Função para ocultar/mostrar botões ao minimizar
local function setGuiMinimized(state)
    isMinimized = state
    for _, child in pairs(frame:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("TextBox") then
            child.Visible = not state
        end
    end
    minimizedTitle.Visible = state
    frame.Size = state and UDim2.new(0.3, 0, 0.1, 0) or UDim2.new(0.3, 0, 0.45, 0)
end

-- Minimizar e restaurar GUI
minimizeButton.MouseButton1Click:Connect(function()
    setGuiMinimized(not isMinimized)
end)

closeButton.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Aimbot funcionalidade
local function getClosestEnemy()
    local char = player.Character
    if not char or not char:FindFirstChild("Head") then return nil end

    local closestEnemy = nil  
    local shortestDistance = math.huge  

    for _, v in pairs(game.Players:GetPlayers()) do  
        if v ~= player and v.Character and v.Character:FindFirstChild("Head") then  
            local enemyHead = v.Character.Head  
            local distance = (char.Head.Position - enemyHead.Position).Magnitude  

            if distance < shortestDistance then  
                shortestDistance = distance  
                closestEnemy = enemyHead  
            end  
        end  
    end  

    return closestEnemy
end

game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotAtivo then
        local enemy = getClosestEnemy()
        if enemy then
            local camera = game.Workspace.CurrentCamera
            camera.CFrame = CFrame.new(camera.CFrame.Position, enemy.Position)
        end
    end
end)

aimbotButton.MouseButton1Click:Connect(function()
    aimbotAtivo = not aimbotAtivo
    aimbotButton.Text = aimbotAtivo and "AIMBOT: ATIVADO" or "AIMBOT: DESATIVADO"
    aimbotButton.BackgroundColor3 = aimbotAtivo and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(0, 255, 0)
end)

tpClosestButton.MouseButton1Click:Connect(function()
    local closestEnemy = getClosestEnemy()
    if closestEnemy then
        player.Character:SetPrimaryPartCFrame(closestEnemy.CFrame)
    end
end)

tpByNameButton.MouseButton1Click:Connect(function()
    local playerName = playerNameBox.Text:match("^%s*(.-)%s*$")
    local targetPlayer = game.Players:FindFirstChild(playerName)

    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then  
        player.Character:SetPrimaryPartCFrame(targetPlayer.Character.HumanoidRootPart.CFrame)  
    else
        warn("Jogador não encontrado ou personagem não está carregado.")
    end
end)

autoLootButton.MouseButton1Click:Connect(function()
    autoLootEnabled = not autoLootEnabled
    autoLootButton.Text = autoLootEnabled and "Auto Coleta: ATIVADO" or "Auto Coleta: DESATIVADO"
    autoLootButton.BackgroundColor3 = autoLootEnabled and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(0, 255, 0)
end)

-- Função para ativar/desativar o ESP
local function toggleESP()
    espAtivo = not espAtivo
    espButton.Text = espAtivo and "ESP: ATIVADO" or "ESP: DESATIVADO"
    espButton.BackgroundColor3 = espAtivo and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(0, 255, 0)
    
    -- Criar ou remover o ESP para os jogadores
    for _, v in pairs(game.Players:GetPlayers()) do
        if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            if espAtivo then
                local espPart = Instance.new("BillboardGui")
                espPart.Adornee = v.Character:WaitForChild("Head")
                espPart.Size = UDim2.new(0, 200, 0, 50)
                espPart.StudsOffset = Vector3.new(0, 2, 0)
                espPart.AlwaysOnTop = true
                espPart.Parent = v.Character

                local textLabel = Instance.new("TextLabel")
                textLabel.Size = UDim2.new(1, 0, 0.5, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.Text = v.Name .. " - " .. math.floor(v.Character:WaitForChild("Humanoid").Health)
                textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                textLabel.TextScaled = true
                textLabel.Parent = espPart
                
                -- Armazenar a referência do ESP
                espParts[v.UserId] = espPart
            else
                -- Remover o ESP se estiver desativado
                if espParts[v.UserId] then
                    espParts[v.UserId]:Destroy()
                    espParts[v.UserId] = nil
                end
            end
        end
    end
end

-- Ativar/desativar ESP
espButton.MouseButton1Click:Connect(toggleESP
