-- Função para criar a GUI do hub
local function createHub()
    -- Verifica se o hub já existe, se sim, não cria novamente
    if game.Players.LocalPlayer.PlayerGui:FindFirstChild("GhostlyAndAlexPro") then
        return
    end

    -- Criando a interface GUI
    local ScreenGui = Instance.new("ScreenGui")
    local Frame = Instance.new("Frame")
    local Title = Instance.new("TextLabel")
    local Button1 = Instance.new("TextButton")
    local Button2 = Instance.new("TextButton")
    local OnOffButton = Instance.new("TextButton")  -- Botão ON/OFF para ativar/desativar o Hub

    -- Set properties for the interface
    ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
    ScreenGui.Name = "GhostlyAndAlexPro"

    -- Frame do Hub
    Frame.Parent = ScreenGui
    Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 0)  -- Cor amarela para o fundo
    Frame.Size = UDim2.new(0, 400, 0, 250)  -- Tamanho médio do hub
    Frame.Position = UDim2.new(0.5, -200, 0.5, -125)  -- Centralizado na tela
    Frame.Visible = true  -- Tornando o hub visível imediatamente

    -- Título do Hub
    Title.Parent = Frame
    Title.Text = "Ghostly and Alex Pro"
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.TextColor3 = Color3.fromRGB(0, 0, 0)  -- Texto preto
    Title.TextSize = 24
    Title.TextAlign = Enum.TextXAlignment.Center

    -- Botão 1: Get Tools
    Button1.Parent = Frame
    Button1.Text = "Get Tools"
    Button1.Size = UDim2.new(0.45, -10, 0, 50)  -- Botão com largura de 45% do frame
    Button1.Position = UDim2.new(0, 10, 0.25, 0)  -- Posicionado no topo à esquerda
    Button1.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Cor preta para o botão
    Button1.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Texto branco
    Button1.TextSize = 18
    Button1.TextAlign = Enum.TextXAlignment.Center

    -- Botão 2: Anti Lag
    Button2.Parent = Frame
    Button2.Text = "Anti Lag"
    Button2.Size = UDim2.new(0.45, -10, 0, 50)  -- Botão com largura de 45% do frame
    Button2.Position = UDim2.new(0.55, 10, 0.25, 0)  -- Posicionado ao lado direito do Botão 1
    Button2.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Cor preta para o botão
    Button2.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Texto branco
    Button2.TextSize = 18
    Button2.TextAlign = Enum.TextXAlignment.Center

    -- Função do Botão 1: Get Tools
    Button1.MouseButton1Click:Connect(function()
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local RunService = game:GetService("RunService")

        local Get = function()
            local Root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not Root then return end  -- Se o personagem não estiver carregado

            -- Iterando pelas tycoons e ativando o GearGiver1
            for _, v in next, workspace.Tycoons:GetDescendants() do
                if v:IsA("TouchTransmitter") and v.Parent.Parent.Name:find("GearGiver1") then
                    firetouchinterest(Root, v.Parent, 0)
                    firetouchinterest(Root, v.Parent, 1)
                end
            end
        end

        LocalPlayer.CharacterAdded:Connect(function()
            LocalPlayer.Character:WaitForChild("HumanoidRootPart")
            RunService.Heartbeat:Wait()
            Get()
        end)

        if LocalPlayer.Character then
            Get()
        end
    end)

    -- Função do Botão 2: Anti Lag
    Button2.MouseButton1Click:Connect(function()
        local ToDisable = {
            Textures = true,
            VisualEffects = true,
            Parts = true,
            Particles = true,
            Sky = true
        }

        local ToEnable = {
            FullBright = false
        }

        local Stuff = {}

        for _, v in next, game:GetDescendants() do
            if ToDisable.Parts then
                if v:IsA("Part") or v:IsA("Union") or v:IsA("BasePart") then
                    v.Material = Enum.Material.SmoothPlastic
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Particles then
                if v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Explosion") or v:IsA("Sparkles") or v:IsA("Fire") then
                    v.Enabled = false
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.VisualEffects then
                if v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("DepthOfFieldEffect") or v:IsA("SunRaysEffect") then
                    v.Enabled = false
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Textures then
                if v:IsA("Decal") or v:IsA("Texture") then
                    v.Texture = ""
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Sky then
                if v:IsA("Sky") then
                    v.Parent = nil
                    table.insert(Stuff, 1, v)
                end
            end
        end

        game:GetService("TestService"):Message("Effects Disabler Script: Successfully disabled " .. #Stuff .. " assets / effects. Settings:")

        for i, v in next, ToDisable do
            print(tostring(i) .. ": " .. tostring(v))
        end

        if ToEnable.FullBright then
            local Lighting = game:GetService("Lighting")

            Lighting.FogColor = Color3.fromRGB(255, 255, 255)
            Lighting.FogEnd = math.huge
            Lighting.FogStart = math.huge
            Lighting.Ambient = Color3.fromRGB(255, 255, 255)
            Lighting.Brightness = 5
            Lighting.ColorShift_Bottom = Color3.fromRGB(255, 255, 255)
            Lighting.ColorShift_Top = Color3.fromRGB(255, 255, 255)
            Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
            Lighting.Outlines = true
        end
    end)

    -- Variável para controlar se o Hub está ativado ou não
    local hubActive = false

    -- Função para ativar/desativar o hub ao pressionar P (ativar) ou A (desativar)
    local function toggleHub(input)
        if input.KeyCode == Enum.KeyCode.P then
            if not hubActive then
                Frame.Visible = true  -- Torna o Hub visível
                hubActive = true
            end
        elseif input.KeyCode == Enum.KeyCode.A then
            if hubActive then
                Frame.Visible = false  -- Torna o Hub invisível
                hubActive = false
            end
        end
    end

    -- Conectar a função de entrada do teclado
    game:GetService("UserInputService").InputBegan:Connect(toggleHub)

    -- Criando o botão de ativar/desativar o hub, na parte superior da tela
    OnOffButton.Parent = ScreenGui
    OnOffButton.Text = "ATIVAR HUB"  -- Inicialmente, será o botão de ativar
    OnOffButton.Size = UDim2.new(0, 200, 0, 50)  -- Tamanho grande para o botão
    OnOffButton.Position = UDim2.new(0.5, -100, 0, 10)  -- Posicionado no topo da tela
    OnOffButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Cor preta para o botão
    OnOffButton.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Texto branco
    OnOffButton.TextSize = 18
    OnOffButton.TextAlign = Enum.TextXAlignment.Center

    -- Função para mudar o texto do botão ao pressionar P ou A
    OnOffButton.MouseButton1Click:Connect(function()
        if hubActive then
            Frame.Visible = false  -- Torna o hub invisível
            OnOffButton.Text = "ATIVAR HUB"  -- Texto para ativar
            hubActive = false
        else
            Frame.Visible = true  -- Torna o hub visível
            OnOffButton.Text = "DESATIVAR HUB"  -- Texto para desativar
            hubActive = true
        end
    end)
end

-- Chama a função para criar o hub quando o script for executado
createHub()
