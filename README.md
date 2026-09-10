-- ====================================================================
--            BRASILNICA BLOX FRUITS HUB [PREMIUM v4.0 - ORION UI]
-- ====================================================================

-- 1. Inicializa a Biblioteca de Interface Gráfica Avançada
local OrionLib = loadstring(game:HttpGet(('https://githubusercontent.com')))()

-- 2. Cria a Janela Principal com o nome atualizado
local Window = OrionLib:MakeWindow({
    Name = "Javier y Gui Hub | Blox Fruits Edition", 
    HidePremium = false, 
    SaveConfig = true, 
    ConfigFolder = "JavierGuiHubBF",
    IntroText = "Carregando Javier y Gui Hub..."
})

-- ====================================================================
--                         ABA 1: MAIN FARMS
-- ====================================================================
local Tab1 = Window:MakeTab({
    Name = "Main Farm",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

Tab1:AddLabel("Selecione o motor de Auto-Farm Avançado:")

-- Executa o motor Redz Hub integrado
Tab1:AddButton({
    Name = "Ativar Auto-Farm Level (Redz Engine)",
    Callback = function()
        OrionLib:MakeNotification({Name = "Javier y Gui Hub", Content = "Iniciando motor de Farm... Aguarde!", Time = 4})
        task.wait(1)
        loadstring(game:HttpGet("https://githubusercontent.com"))()
    end
})

-- Executa o motor Hoho Hub integrado
Tab1:AddButton({
    Name = "Ativar Auto-Farm Alternativo (Hoho Engine)",
    Callback = function()
        OrionLib:MakeNotification({Name = "Javier y Gui Hub", Content = "Iniciando Hoho Engine...", Time = 4})
        task.wait(1)
        loadstring(game:HttpGet('https://githubusercontent.com'))()
    end
})

-- ====================================================================
--                       ABA 2: PLAYER MODIFIERS
-- ====================================================================
local Tab2 = Window:MakeTab({
    Name = "Player",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Variáveis de Controle locais
getgenv().BFConfig = {
    InfJump = false,
    WalkSpeed = 16
}

-- Pulo Infinito integrado diretamente
Tab2:AddToggle({
    Name = "Pulo Infinito (Infinite Jump)",
    Default = false,
    Callback = function(Value)
        getgenv().BFConfig.InfJump = Value
    end    
})

-- Slider de Velocidade de Corrida
Tab2:AddSlider({
    Name = "Velocidade do Personagem",
    Min = 16,
    Max = 250,
    Default = 16,
    Color = Color3.fromRGB(0, 0, 139),
    Increment = 5,
    ValueName = "Speed",
    Callback = function(Value)
        getgenv().BFConfig.WalkSpeed = Value
    end    
})

-- ====================================================================
--                        ABA 3: CREDITS & INFO
-- ====================================================================
local Tab3 = Window:MakeTab({
    Name = "Créditos",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

Tab3:AddLabel("Donos do Script: Javier y Gui")
Tab3:AddLabel("Versão Atual: v4.0 Premium")

Tab3:AddButton({
    Name = "Copiar nosso Discord",
    Callback = function()
        if setclipboard then
            setclipboard("https://discord.gg")
            OrionLib:MakeNotification({Name = "Sucesso", Content = "Link do Discord copiado!", Time = 3})
        end
    end
})

-- ====================================================================
--                SISTEMAS FÍSICOS EM SEGUNDO PLANOS
-- ====================================================================

-- Loop de Loops de Atributos do Jogador
task.spawn(function()
    while task.wait(0.1) do
        pcall(function()
            local char = game.Players.LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.WalkSpeed = getgenv().BFConfig.WalkSpeed
            end
        end)
    end
end)

-- Interceptador do Pulo Infinito
game:GetService("UserInputService").JumpRequest:Connect(function()
    if getgenv().BFConfig.InfJump then
        pcall(function()
            local char = game.Players.LocalPlayer.Character
            if char and char:FindFirstChildOfClass("Humanoid") then
                char:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
            end
        end)
    end
end)

OrionLib:Init()
