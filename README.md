-- ====================================================================
--            BRASILNICA UNIVERSAL HUB [PREMIUM v3.0 - ORION UI]
-- ====================================================================

-- 1. Carrega a Biblioteca Visual Profissional (Orion Library)
local OrionLib = loadstring(game:HttpGet(('https://githubusercontent.com')))()

-- Configurações Globais salvando os estados
getgenv().BrasilnicaConfig = {
    AutoChest = false,
    FarmDistance = 5,
    TweenSpeed = 120,
    InfiniteJump = false,
    Noclip = false,
    WalkSpeedActive = false,
    SpeedValue = 16,
    JumpPowerActive = false,
    JumpValue = 50
}

-- 2. Cria a Janela Principal que o jogador vai ver na tela
local Window = OrionLib:MakeWindow({
    Name = "Brasilnica Pro Hub v3.0", 
    HidePremium = false, 
    SaveConfig = true, 
    ConfigFolder = "BrasilnicaConfig",
    IntroText = "Carregando Brasilnica..."
})

-- ====================================================================
--                         ABA 1: AUTOMATIONS
-- ====================================================================
local Tab1 = Window:MakeTab({
    Name = "Automations",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Botão de Liga/Desliga para Coletar Baús
Tab1:AddToggle({
    Name = "Auto Farm: Coletar Baús",
    Default = false,
    Callback = function(Value)
        getgenv().BrasilnicaConfig.AutoChest = Value
    end    
})

-- Slider para ajustar a velocidade do teleporte até o baú
Tab1:AddSlider({
    Name = "Velocidade do Farm (Tween)",
    Min = 50,
    Max = 300,
    Default = 120,
    Color = Color3.fromRGB(0, 0, 139),
    Increment = 10,
    ValueName = "studs/s",
    Callback = function(Value)
        getgenv().BrasilnicaConfig.TweenSpeed = Value
    end    
})

-- ====================================================================
--                          ABA 2: PLAYER
-- ====================================================================
local Tab2 = Window:MakeTab({
    Name = "Player Modifiers",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Botão Liga/Desliga para Atravessar Paredes
Tab2:AddToggle({
    Name = "Atravessar Paredes (Noclip)",
    Default = false,
    Callback = function(Value)
        getgenv().BrasilnicaConfig.Noclip = Value
    end    
})

-- Botão Liga/Desliga para Pulo Infinito
Tab2:AddToggle({
    Name = "Pulo Infinito (Inf Jump)",
    Default = false,
    Callback = function(Value)
        getgenv().BrasilnicaConfig.InfiniteJump = Value
    end    
})

-- Modificador de Velocidade de Corrida
Tab2:AddSlider({
    Name = "Velocidade de Corrida",
    Min = 16,
    Max = 250,
    Default = 16,
    Color = Color3.fromRGB(0, 0, 139),
    Increment = 5,
    ValueName = "Speed",
    Callback = function(Value)
        if Value > 16 then
            getgenv().BrasilnicaConfig.WalkSpeedActive = true
            getgenv().BrasilnicaConfig.SpeedValue = Value
        else
            getgenv().BrasilnicaConfig.WalkSpeedActive = false
            local char = game.Players.LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then char.Humanoid.WalkSpeed = 16 end
        end
    end    
})

-- Modificador de Força do Pulo
Tab2:AddSlider({
    Name = "Força do Pulo",
    Min = 50,
    Max = 300,
    Default = 50,
    Color = Color3.fromRGB(0, 0, 139),
    Increment = 5,
    ValueName = "Power",
    Callback = function(Value)
        if Value > 50 then
            getgenv().BrasilnicaConfig.JumpPowerActive = true
            getgenv().BrasilnicaConfig.JumpValue = Value
        else
            getgenv().BrasilnicaConfig.JumpPowerActive = false
            local char = game.Players.LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then char.Humanoid.JumpPower = 50 end
        end
    end    
})

-- ====================================================================
--               MÓDULOS DE ENGENHARIA DE TRÁS DOS PANOS
-- ====================================================================

-- Sistema de Movimentação por Tween (Bypass)
local function SafeTween(targetCFrame)
    local character = game.Players.LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end
    local rootPart = character.HumanoidRootPart
    local distance = (rootPart.Position - targetCFrame.Position).Magnitude
    local duration = distance / getgenv().BrasilnicaConfig.TweenSpeed
    local tweenInfo = TweenInfo.new(duration, Enum.EasingStyle.Linear)
    local tween = game:GetService("TweenService"):Create(rootPart, tweenInfo, {CFrame = targetCFrame})
    tween:Play()
    return tween
end

-- Monitor do Noclip
game:GetService("RunService").Stepped:Connect(function()
    if getgenv().BrasilnicaConfig.Noclip or getgenv().BrasilnicaConfig.AutoChest then
        local character = game.Players.LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then part.CanCollide = false end
            end
        end
    end
end)

-- Monitor do Pulo Infinito
game:GetService("UserInputService").JumpRequest:Connect(function()
    if getgenv().BrasilnicaConfig.InfiniteJump then
        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChildOfClass("Humanoid") then
            character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
        end
    end
end)

-- Loop do Auto Farm de Baús
task.spawn(function()
    while task.wait(0.5) do
        if getgenv().BrasilnicaConfig.AutoChest then
            local targets = {}
            for _, obj in pairs(workspace:GetDescendants()) do
                if (obj:IsA("Part") or obj:IsA("MeshPart") or obj:IsA("Model")) and 
                   (string.find(string.lower(obj.Name), "chest") or string.find(string.lower(obj.Name), "bau")) then
                    local cframe = obj:IsA("Model") and obj:GetPivot() or obj.CFrame
                    table.insert(targets, {Object = obj, CFrame = cframe})
                end
            end
            for _, target in ipairs(targets) do
                if not getgenv().BrasilnicaConfig.AutoChest then break end
                if target.Object and target.Object.Parent then
                    local currentTween = SafeTween(target.CFrame * CFrame.new(0, getgenv().BrasilnicaConfig.FarmDistance, 0))
                    if currentTween then currentTween.Completed:Wait() task.wait(0.1) end
                end
            end
        end
    end
end)

-- Loop dos Atributos do Humanoide
task.spawn(function()
    while task.wait(0.1) do
        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("Humanoid") then
            local humanoid = character.Humanoid
            if getgenv().BrasilnicaConfig.WalkSpeedActive then humanoid.WalkSpeed = getgenv().BrasilnicaConfig.SpeedValue end
            if getgenv().BrasilnicaConfig.JumpPowerActive then humanoid.JumpPower = getgenv().BrasilnicaConfig.JumpValue humanoid.UseJumpPower = true end
        end
    end
end)

OrionLib:Init()
