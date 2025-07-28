# scni1

-- Variáveis de controle
local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local UserInputService = game:GetService("UserInputService")

local flying = false  -- Estado de voo
local flightSpeed = 50  -- Velocidade do voo
local bodyVelocity  -- A variável que controla a velocidade do voo

-- Função para alternar o voo
local function toggleFly()
    if flying then
        flying = false
        -- Remover a parte de voo
        if bodyVelocity then
            bodyVelocity:Destroy()
        end
        print("Voo desativado.")
    else
        flying = true
        -- Criar um BodyVelocity para permitir o voo
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000)  -- Força máxima para o voo
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)  -- Inicialmente sem movimento
        bodyVelocity.Parent = Character:WaitForChild("HumanoidRootPart")
        print("Voo ativado.")
    end
end

-- Função para atualizar o voo
local function updateFly(input)
    if flying then
        -- A direção de voo será baseada na entrada do usuário (teclado/mouse)
        local direction = Vector3.new(0, 0, 0)  -- Direção inicial (sem movimento)
        
        -- Verifica teclas de movimento, ajustando a direção de voo
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            direction = direction + Character.HumanoidRootPart.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            direction = direction - Character.HumanoidRootPart.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            direction = direction - Character.HumanoidRootPart.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            direction = direction + Character.HumanoidRootPart.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            direction = direction + Vector3.new(0, 1, 0)  -- Subir
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            direction = direction - Vector3.new(0, 1, 0)  -- Descer
        end
        
        bodyVelocity.Velocity = direction * flightSpeed  -- Aplica a direção de voo
    end
end

-- Detecção de pressionamento de tecla
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end  -- Ignora se o jogo já processou a entrada

    -- Verifica se a tecla pressionada foi "F" e alterna o voo
    if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == Enum.KeyCode.F then
        toggleFly()
    end
end)

-- Atualiza o voo enquanto ele estiver ativo
game:GetService("RunService").Heartbeat:Connect(function()
    updateFly()
end)
