-- CTW Mobile Auto-Farm - Delta Executor
-- Interface otimizada para mobile

if not game:IsLoaded() then
    game.Loaded:Wait()
end

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local TweenService = game:GetService("TweenService")

-- Configurações Mobile
getgenv().CTW_Settings = {
    AutoConquer = true,
    AutoCollect = true,
    AutoUpgrade = true,
    AutoAttack = true,
    FarmSpeed = 1,
    CollectRange = 50,
    ShowButtons = true,
    AntiAfk = true
}

-- Criar interface mobile simples
local function createMobileUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "CTWMobileUI"
    screenGui.Parent = game:GetService("CoreGui") or LocalPlayer:WaitForChild("PlayerGui")
    
    -- Container principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(0, 300, 0, 400)
    mainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
    mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    mainFrame.BackgroundTransparency = 0.2
    mainFrame.BorderSizePixel = 0
    mainFrame.Active = true
    mainFrame.Draggable = true
    mainFrame.Parent = screenGui
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Name = "Title"
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Position = UDim2.new(0, 0, 0, 0)
    title.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    title.Text = "CTW MOBILE FARM"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 20
    title.Font = Enum.Font.GothamBold
    title.Parent = mainFrame
    
    -- Botão de fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Name = "CloseBtn"
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -35, 0, 5)
    closeBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
    closeBtn.Text = "X"
    closeBtn.TextColor3 = Color3.white
    closeBtn.TextSize = 18
    closeBtn.Parent = mainFrame
    
    closeBtn.MouseButton1Click:Connect(function()
        screenGui:Destroy()
        getgenv().CTW_Settings.ShowButtons = false
    end)
    
    -- Lista de botões
    local buttonContainer = Instance.new("ScrollingFrame")
    buttonContainer.Name = "ButtonContainer"
    buttonContainer.Size = UDim2.new(1, -10, 1, -50)
    buttonContainer.Position = UDim2.new(0, 5, 0, 45)
    buttonContainer.BackgroundTransparency = 1
    buttonContainer.ScrollBarThickness = 6
    buttonContainer.Parent = mainFrame
    
    -- Função para criar botões
    local function createToggle(text, default, callback)
        local toggleFrame = Instance.new("Frame")
        toggleFrame.Size = UDim2.new(1, 0, 0, 50)
        toggleFrame.BackgroundTransparency = 1
        toggleFrame.Parent = buttonContainer
        
        local toggleBtn = Instance.new("TextButton")
        toggleBtn.Size = UDim2.new(0.8, 0, 0, 40)
        toggleBtn.Position = UDim2.new(0.1, 0, 0, 5)
        toggleBtn.BackgroundColor3 = default and Color3.fromRGB(60, 180, 80) or Color3.fromRGB(180, 60, 60)
        toggleBtn.Text = text
        toggleBtn.TextColor3 = Color3.white
        toggleBtn.TextSize = 16
        toggleBtn.Font = Enum.Font.Gotham
        toggleBtn.Parent = toggleFrame
        
        toggleBtn.MouseButton1Click:Connect(function()
            local newState = not (toggleBtn.BackgroundColor3 == Color3.fromRGB(60, 180, 80))
            toggleBtn.BackgroundColor3 = newState and Color3.fromRGB(60, 180, 80) or Color3.fromRGB(180, 60, 60)
            callback(newState)
        end)
        
        return toggleBtn
    end
    
    -- Criar botões de toggle
    createToggle("✅ AUTO-CONQUER", getgenv().CTW_Settings.AutoConquer, function(state)
        getgenv().CTW_Settings.AutoConquer = state
        warn("Auto-Conquer: " .. (state and "ON" or "OFF"))
    end)
    
    createToggle("💰 AUTO-COLLECT", getgenv().CTW_Settings.AutoCollect, function(state)
        getgenv().CTW_Settings.AutoCollect = state
        warn("Auto-Collect: " .. (state and "ON" or "OFF"))
    end)
    
    createToggle("⚡ AUTO-UPGRADE", getgenv().CTW_Settings.AutoUpgrade, function(state)
        getgenv().CTW_Settings.AutoUpgrade = state
        warn("Auto-Upgrade: " .. (state and "ON" or "OFF"))
    end)
    
    createToggle("⚔️ AUTO-ATTACK", getgenv().CTW_Settings.AutoAttack, function(state)
        getgenv().CTW_Settings.AutoAttack = state
        warn("Auto-Attack: " .. (state and "ON" or "OFF"))
    end)
    
    createToggle("🛡️ ANTI-AFK", getgenv().CTW_Settings.AntiAfk, function(state)
        getgenv().CTW_Settings.AntiAfk = state
        warn("Anti-AFK: " .. (state and "ON" or "OFF"))
    end)
    
    -- Botão de teleport
    local teleportBtn = Instance.new("TextButton")
    teleportBtn.Size = UDim2.new(0.8, 0, 0, 40)
    teleportBtn.Position = UDim2.new(0.1, 0, 0, 300)
    teleportBtn.BackgroundColor3 = Color3.fromRGB(80, 120, 200)
    teleportBtn.Text = "🚀 TELEPORT BOSS"
    teleportBtn.TextColor3 = Color3.white
    teleportBtn.TextSize = 16
    teleportBtn.Font = Enum.Font.GothamBold
    teleportBtn.Parent = buttonContainer
    
    teleportBtn.MouseButton1Click:Connect(function()
        teleportToBoss()
    end)
    
    -- Botão de status
    local statusBtn = Instance.new("TextButton")
    statusBtn.Size = UDim2.new(0.8, 0, 0, 40)
    statusBtn.Position = UDim2.new(0.1, 0, 0, 350)
    statusBtn.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    statusBtn.Text = "📊 STATUS DO FARM"
    statusBtn.TextColor3 = Color3.white
    statusBtn.TextSize = 14
    statusBtn.Font = Enum.Font.Gotham
    statusBtn.Parent = buttonContainer
    
    statusBtn.MouseButton1Click:Connect(function()
        showFarmStatus()
    end)
    
    return screenGui
end

-- Funções do farm
function teleportToBoss()
    if not LocalPlayer.Character then return end
    
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj.Name:lower():find("boss") and obj:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character:MoveTo(obj.HumanoidRootPart.Position + Vector3.new(0, 5, 0))
            warn("Teleportado para o boss: " .. obj.Name)
            return
        end
    end
    warn("Nenhum boss encontrado!")
end

function showFarmStatus()
    local status = ""
    status = status .. "Auto-Conquer: " .. (getgenv().CTW_Settings.AutoConquer and "✅ ON" or "❌ OFF") .. "\n"
    status = status .. "Auto-Collect: " .. (getgenv().CTW_Settings.AutoCollect and "✅ ON" or "❌ OFF") .. "\n"
    status = status .. "Auto-Upgrade: " .. (getgenv().CTW_Settings.AutoUpgrade and "✅ ON" or "❌ OFF") .. "\n"
    status = status .. "Script ATIVO"
    
    warn("=== STATUS CTW ===")
    warn(status)
    warn("=================")
end

-- Função de conquista
function conquerTerritory()
    for _, obj in pairs(Workspace:GetChildren()) do
        if (obj.Name:lower():find("territory") or obj.Name:lower():find("base") or obj.Name:lower():find("flag")) 
           and obj:FindFirstChild("Part") then
           
            local territoryPart = obj.Part
            if (territoryPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude < 30 then
                -- Clicar no território
                if obj:FindFirstChild("ClickDetector") then
                    fireclickdetector(obj.ClickDetector)
                end
                
                -- Tentar eventos
                for _, remote in pairs(ReplicatedStorage:GetDescendants()) do
                    if remote:IsA("RemoteEvent") and remote.Name:lower():find("conquer") then
                        pcall(function()
                            remote:FireServer(obj.Name)
                        end)
                    end
                end
                
                return true
            end
        end
    end
    return false
end

-- Coletar moedas
function collectCoins()
    local collected = 0
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj.Name:lower():find("coin") or obj.Name:lower():find("money") or obj.Name:lower():find("cash") then
            if obj:IsA("Part") and (obj.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude < getgenv().CTW_Settings.CollectRange then
                firetouchinterest(LocalPlayer.Character.HumanoidRootPart, obj, 0)
                firetouchinterest(LocalPlayer.Character.HumanoidRootPart, obj, 1)
                collected = collected + 1
            end
        end
    end
    return collected
end

-- Auto-upgrade
function autoUpgrade()
    -- Tentar via RemoteEvents
    for _, remote in pairs(ReplicatedStorage:GetDescendants()) do
        if remote:IsA("RemoteEvent") then
            local name = remote.Name:lower()
            if name:find("upgrade") or name:find("evolve") or name:find("enhance") then
                pcall(function()
                    remote:FireServer()
                    return true
                end)
            end
        end
    end
    return false
end

-- Auto-attack NPCs
function autoAttack()
    for _, npc in pairs(Workspace:GetChildren()) do
        if npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
            if npc.Humanoid.Health > 0 and npc.Name ~= LocalPlayer.Character.Name then
                local distance = (npc.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                
                if distance < 20 then
                    -- Mirar no NPC
                    LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(
                        LocalPlayer.Character.HumanoidRootPart.Position,
                        Vector3.new(npc.HumanoidRootPart.Position.X, LocalPlayer.Character.HumanoidRootPart.Position.Y, npc.HumanoidRootPart.Position.Z)
                    )
                    
                    -- Atacar
                    for _, remote in pairs(ReplicatedStorage:GetDescendants()) do
                        if remote:IsA("RemoteEvent") and remote.Name:lower():find("damage") then
                            pcall(function()
                                remote:FireServer(npc)
                            end)
                        end
                    end
                    
                    -- Simular clique
                    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 1)
                    task.wait(0.1)
                    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 1)
                    
                    return true
                end
            end
        end
    end
    return false
end

-- Loop principal
local MainLoop = RunService.Heartbeat:Connect(function()
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    -- Anti-AFK
    if getgenv().CTW_Settings.AntiAfk then
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end
    
    -- Auto-Conquer
    if getgenv().CTW_Settings.AutoConquer then
        conquerTerritory()
        task.wait(getgenv().CTW_Settings.FarmSpeed)
    end
    
    -- Auto-Collect
    if getgenv().CTW_Settings.AutoCollect then
        local collected = collectCoins()
        if collected > 0 then
            task.wait(0.3)
        end
    end
    
    -- Auto-Upgrade
    if getgenv().CTW_Settings.AutoUpgrade then
        autoUpgrade()
        task.wait(2)
    end
    
    -- Auto-Attack
    if getgenv().CTW_Settings.AutoAttack then
        autoAttack()
        task.wait(0.5)
    end
end)

-- Criar botão flutuante para abrir menu
local function createFloatingButton()
    local buttonGui = Instance.new("ScreenGui")
    buttonGui.Name = "CTWFloatingButton"
    buttonGui.Parent = game:GetService("CoreGui") or LocalPlayer:WaitForChild("PlayerGui")
    
    local floatBtn = Instance.new("TextButton")
    floatBtn.Name = "FloatBtn"
    floatBtn.Size = UDim2.new(0, 60, 0, 60)
    floatBtn.Position = UDim2.new(0, 20, 0.5, -30)
    floatBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    floatBtn.BackgroundTransparency = 0.3
    floatBtn.Text = "🎮"
    floatBtn.TextColor3 = Color3.white
    floatBtn.TextSize = 24
    floatBtn.Font = Enum.Font.GothamBold
    floatBtn.BorderSizePixel = 0
    floatBtn.ZIndex = 999
    floatBtn.Parent = buttonGui
    
    -- Arrastável
    local dragging = false
    local dragInput, dragStart, startPos
    
    floatBtn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = floatBtn.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    floatBtn.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    
    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if dragging and (input == dragInput) then
            local delta = input.Position - dragStart
            floatBtn.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    
    -- Clique para abrir menu
    floatBtn.MouseButton1Click:Connect(function()
        if getgenv().CTW_Settings.ShowButtons then
            createMobileUI()
        else
            getgenv().CTW_Settings.ShowButtons = true
            createMobileUI()
        end
    end)
    
    return floatBtn
end

-- Inicialização
task.wait(3)

-- Criar interface
createFloatingButton()

-- Notificação
warn("========================================")
warn("CTW MOBILE FARM CARREGADO!")
warn("Toque no botão azul 🎮 para abrir o menu")
warn("Botão é arrastável - mova para onde quiser")
warn("========================================")

-- Comandos no chat
LocalPlayer.Chatted:Connect(function(msg)
    msg = msg:lower()
    if msg == "/ctw on" then
        getgenv().CTW_Settings.AutoConquer = true
        getgenv().CTW_Settings.AutoCollect = true
        warn("CTW Farm: TUDO ATIVADO!")
    elseif msg == "/ctw off" then
        getgenv().CTW_Settings.AutoConquer = false
        getgenv().CTW_Settings.AutoCollect = false
        warn("CTW Farm: TUDO DESATIVADO!")
    elseif msg == "/ctw menu" then
        createMobileUI()
    end
end)
