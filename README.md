local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

local Window = OrionLib:MakeWindow({
    Name = "Japa Hub - Self Controls",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "JapaConfigs",
    BackgroundColor = Color3.fromRGB(255, 255, 255), -- Cor de fundo branca
    TextColor = Color3.fromRGB(0, 0, 0), -- Cor do texto preto
})

--////////////////////////// VARIÁVEIS /////////////////////////////////////
local plr = game.Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local flyEnabled = false
local noClipEnabled = false
local speedEnabled = false
local godModeEnabled = false
local speedValue = 50 -- Velocidade padrão
local bodyGyro, bodyVelocity
local selectedPlayerName -- Variável para armazenar o nome do jogador selecionado
local maxHealthValue = 99999999 -- Valor máximo de vida
local aimbotEnabled = false
local aimSensitivity = 0.3
local fovSize = 100
local fovCircle = Drawing.new("Circle")
fovCircle.Color = Color3.fromRGB(255, 255, 255)
fovCircle.Thickness = 1
fovCircle.NumSides = 100
fovCircle.Radius = fovSize
fovCircle.Filled = false
fovCircle.Visible = false
local aimDistance = 300 -- Valor padrão

-- Atualizar o FOV Circle
function updateFovCircle()
    fovCircle.Radius = fovSize
    fovCircle.Position = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)
end

-- Função de Aimbot (atualizada com a distância máxima)
function enableAimbot(state)
    aimbotEnabled = state
    fovCircle.Visible = state
    if aimbotEnabled then
        game:GetService("RunService").RenderStepped:Connect(function()
            if aimbotEnabled then
                local camera = workspace.CurrentCamera
                local closestTarget = nil
                local shortestDistance = math.huge

                for _, player in pairs(game.Players:GetPlayers()) do
                    if player ~= plr and player.Character and player.Character:FindFirstChild("Head") then
                        local head = player.Character.Head
                        local screenPoint, onScreen = camera:WorldToScreenPoint(head.Position)
                        local mousePosition = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
                        local distanceFromMouse = (Vector2.new(screenPoint.X, screenPoint.Y) - mousePosition).Magnitude

                        -- Verifica se o jogador está dentro do FOV e dentro da distância máxima
                        local playerDistance = (head.Position - camera.CFrame.Position).Magnitude
                        if onScreen and distanceFromMouse < fovSize and distanceFromMouse < shortestDistance and playerDistance <= aimDistance then
                            shortestDistance = distanceFromMouse
                            closestTarget = head
                        end
                    end
                end

                if closestTarget then
                    local targetPosition = closestTarget.Position
                    local aimDirection = (targetPosition - camera.CFrame.Position).unit
                    camera.CFrame = CFrame.new(camera.CFrame.Position, camera.CFrame.Position + aimDirection * aimSensitivity)
                end
            end
        end)
    end
end


-- esp

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

local Window = OrionLib:MakeWindow({
    Name = "Japa Hub - Self Controls",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "JapaConfigs",
    BackgroundColor = Color3.fromRGB(255, 255, 255), -- Cor de fundo branca
    TextColor = Color3.fromRGB(0, 0, 0), -- Cor do texto preto
})

--////////////////////////// VARIÁVEIS /////////////////////////////////////
local plr = game.Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local flyEnabled = false
local noClipEnabled = false
local speedEnabled = false
local godModeEnabled = false
local speedValue = 50 -- Velocidade padrão
local bodyGyro, bodyVelocity
local selectedPlayerName -- Variável para armazenar o nome do jogador selecionado
local maxHealthValue = 99999999 -- Valor máximo de vida


-- esp

local localPlayer = game.Players.LocalPlayer

function toggleNametags(state)
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("Head") then
            if state then
                -- Desativa a Nametag para o jogador local (você)
                if player ~= localPlayer then
                    if not player.Character.Head:FindFirstChild("Nametag") then
                        local bill = Instance.new("BillboardGui", player.Character.Head)
                        bill.Name = "Nametag"
                        bill.Size = UDim2.new(0, 100, 0, 50)
                        bill.AlwaysOnTop = true
                        bill.StudsOffset = Vector3.new(0, 2, 0)

                        local nameLabel = Instance.new("TextLabel", bill)
                        nameLabel.Text = player.Name
                        nameLabel.Size = UDim2.new(1, 0, 1, 0)
                        nameLabel.TextColor3 = Color3.new(1, 1, 1)
                        nameLabel.BackgroundTransparency = 1
                        nameLabel.TextStrokeTransparency = 0
                        nameLabel.Font = Enum.Font.SourceSansBold
                        nameLabel.TextScaled = true
                    end
                end
            else
                -- Remove o Nametag
                if player.Character.Head:FindFirstChild("Nametag") then
                    player.Character.Head.Nametag:Destroy()
                end
            end
        end
    end
end



-- Função para ativar/desativar o voo
function toggleFly(state)
    flyEnabled = state
    local player = game.Players.LocalPlayer

    if flyEnabled then
        local humanoid = player.Character:WaitForChild("Humanoid")
        humanoid.PlatformStand = true

        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.CFrame = player.Character.HumanoidRootPart.CFrame
        bodyGyro.P = 9e4
        bodyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
        bodyGyro.Parent = player.Character.HumanoidRootPart

        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
        bodyVelocity.Parent = player.Character.HumanoidRootPart

        -- Controlando o movimento durante o voo
        game:GetService("RunService").RenderStepped:Connect(function()
            if flyEnabled then
                local camera = workspace.CurrentCamera
                local moveDirection = Vector3.new(0, 0, 0)

                if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
                    moveDirection = moveDirection + camera.CFrame.LookVector
                end
                if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
                    moveDirection = moveDirection - camera.CFrame.LookVector
                end
                if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.A) then
                    moveDirection = moveDirection - camera.CFrame.RightVector
                end
                if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.D) then
                    moveDirection = moveDirection + camera.CFrame.RightVector
                end

                bodyVelocity.Velocity = moveDirection * speedValue -- Ajusta a velocidade do voo aqui
                bodyGyro.CFrame = camera.CFrame
            end
        end)
    else
        if bodyGyro then bodyGyro:Destroy() end
        if bodyVelocity then bodyVelocity:Destroy() end
        player.Character.Humanoid.PlatformStand = false
    end
end

-- Função para ativar/desativar o NoClip
function toggleNoClip(state)
    noClipEnabled = state
    local character = plr.Character or plr.CharacterAdded:Wait()

    if noClipEnabled then
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false -- Desativa a colisão das partes do personagem
            end
        end
    else
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true -- Reativa a colisão das partes do personagem
            end
        end
    end
end

-- Função para ativar/desativar a velocidade
function toggleSpeed(state)
    speedEnabled = state
    local player = game.Players.LocalPlayer

    if speedEnabled then
        player.Character.Humanoid.WalkSpeed = speedValue
    else
        player.Character.Humanoid.WalkSpeed = 16 -- Velocidade padrão
    end
end

-- Função para ativar/desativar o God Mode
function toggleGodMode(state)
    godModeEnabled = state
    local humanoid = plr.Character:WaitForChild("Humanoid")

    if godModeEnabled then
        humanoid.MaxHealth = maxHealthValue
        humanoid.Health = maxHealthValue
    else
        humanoid.MaxHealth = 100 -- Valor padrão, ajuste conforme necessário
        humanoid.Health = humanoid.Health -- Restaura saúde ao valor atual
    end
end

-- Função para teleportar para um jogador
function teleportToPlayer(targetPlayer)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        plr.Character.HumanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame
    end
end

-- Função para explodir um jogador
function explodePlayer(targetPlayer)
    if targetPlayer and targetPlayer.Character then
        local explosion = Instance.new("Explosion")
        explosion.Position = targetPlayer.Character.HumanoidRootPart.Position
        explosion.BlastRadius = 10
        explosion.BlastPressure = 5000
        explosion.Parent = workspace
    end
end

-- Função para crashar um jogador
function crashPlayer(targetPlayer)
    if targetPlayer then
        targetPlayer:Kick("Você foi crashado pelo Japa Hub.") -- Faz o jogador sair do jogo
    end
end

-- Função para limpar o inventário do jogador
function clearPlayer(targetPlayer)
    if targetPlayer then
        for _, item in pairs(targetPlayer.Backpack:GetChildren()) do
            item:Destroy() -- Remove os itens do inventário
        end
    end
end

-- Função para bugar o teleporte
function bugTeleport(targetPlayer)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        targetPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0, 500, 0) -- Teleporta para uma posição alta
    end
end

-- Função para matar um jogador
function killPlayer(targetPlayer)
    if targetPlayer and targetPlayer.Character then
        targetPlayer.Character:FindFirstChild("Humanoid").Health = 0 -- Mata o jogador
    end
end

-- Interface gráfica
local selfTab = Window:MakeTab({
    Name = "Self",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Adicionando o aimbot à interface gráfica
selfTab:AddToggle({
    Name = "Ativar Aimbot",
    Default = false,
    Callback = function(state)
        enableAimbot(state)
    end    
})

-- Slider para ajustar a sensibilidade do Aimbot
selfTab:AddSlider({
    Name = "Ajustar Sensibilidade do Aimbot",
    Min = 0.1,
    Max = 1,
    Default = aimSensitivity,
    Color = Color3.fromRGB(255, 0, 0),
    Increment = 0.1,
    Callback = function(value)
        aimSensitivity = value
    end    
})

-- Slider para ajustar o tamanho do FOV
selfTab:AddSlider({
    Name = "Ajustar Tamanho do FOV",
    Min = 50,
    Max = 500,
    Default = fovSize,
    Color = Color3.fromRGB(255, 0, 0),
    Increment = 10,
    Callback = function(value)
        fovSize = value
        updateFovCircle()
    end    
})

-- Slider para ajustar a distância máxima do Aimbot
selfTab:AddSlider({
    Name = "Ajustar Distância Máxima do Aimbot",
    Min = 1,
    Max = 10000,
    Default = aimDistance,
    Color = Color3.fromRGB(0, 255, 0),
    Increment = 50,
    Callback = function(value)
        aimDistance = value
    end    
})

selfTab:AddSection({
    Name = "Fly & No Clip"
})

-- Checkbox para ativar/desativar Fly
selfTab:AddToggle({
    Name = "Ativar Fly",
    Default = false,
    Callback = function(state)
        toggleFly(state) -- Ativa/desativa o voo
    end    
})

-- Checkbox para ativar/desativar NoClip
selfTab:AddToggle({
    Name = "Nc (Atravessar Paredes)",
    Default = false,
    Callback = function(state)
        toggleNoClip(state) -- Ativa/desativa o NoClip
    end    
})

-- Seção de Speed
selfTab:AddSection({
    Name = "Speed"
})

-- Checkbox para ativar/desativar Speed
selfTab:AddToggle({
    Name = "Ativar Speed",
    Default = false,
    Callback = function(state)
        toggleSpeed(state) -- Ativa/desativa a velocidade
    end    
})

-- Slider para ajustar a velocidade
selfTab:AddSlider({
    Name = "Ajustar Velocidade",
    Min = 16,
    Max = 200,
    Default = speedValue,
    Color = Color3.fromRGB(255, 0, 0),
    Increment = 1,
    Callback = function(value)
        speedValue = value
        if speedEnabled then
            plr.Character.Humanoid.WalkSpeed = speedValue
        end
    end    
})

-- Seção de God Mode
selfTab:AddSection({
    Name = "God Mode"
})

-- Checkbox para ativar/desativar God Mode
selfTab:AddToggle({
    Name = "Ativar God Mode",
    Default = false,
    Callback = function(state)
        toggleGodMode(state) -- Ativa/desativa o God Mode
    end    
})

-- Nova seção para Players
local playersTab = Window:MakeTab({
    Name = "Players",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Campo de texto para inserir o nickname do jogador
playersTab:AddTextbox({
    Name = "Insira o Nickname do Jogador",
    Default = "",
    TextDisappear = false,
    Callback = function(value)
        selectedPlayerName = value
    end    
})

-- Funções para interagir com o jogador
playersTab:AddButton({
    Name = "Teletransportar para Jogador",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        teleportToPlayer(targetPlayer) -- Teleporta para o jogador
    end    
})

playersTab:AddButton({
    Name = "Explodir Jogador",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        explodePlayer(targetPlayer) -- Explode o jogador
    end    
})

playersTab:AddButton({
    Name = "Crashar Jogador",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        crashPlayer(targetPlayer) -- Crasha o jogador
    end    
})

playersTab:AddButton({
    Name = "Limpar Jogador",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        clearPlayer(targetPlayer) -- Limpa o jogador
    end    
})

playersTab:AddButton({
    Name = "Bugar Teleporte",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        bugTeleport(targetPlayer) -- Buga o teleporte do jogador
    end    
})

playersTab:AddButton({
    Name = "Matar Jogador",
    Callback = function()
        local targetPlayer = game.Players:FindFirstChild(selectedPlayerName)
        killPlayer(targetPlayer) -- Mata o jogador
    end    
})

-- Nova aba ESP
local espTab = Window:MakeTab({
    Name = "ESP",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Checkbox para ativar/desativar Nametags
espTab:AddToggle({
    Name = "Ativar Nametags",
    Default = false,
    Callback = function(state)
        toggleNametags(state) -- Ativa/desativa as nametags
    end    
})

-- Exibe a janela
OrionLib:Init()
