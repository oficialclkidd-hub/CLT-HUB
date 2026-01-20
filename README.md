-- [[ CLT HUB 📘 - VERSÃO OFICIAL ]] --
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local LPlayer = Players.LocalPlayer

-- Configurações e Estados
local States = {
    Fly = false,
    Troll = false,
    Rainbow = false,
    Esp = false,
    SelectedPlayer = nil,
    Viewing = false
}

-- Criando a Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CLTHub_Final"
ScreenGui.Parent = game.CoreGui

-- Botão de Abrir (Foto da Carteira)
local OpenBtn = Instance.new("ImageButton")
OpenBtn.Name = "OpenBtn"
OpenBtn.Parent = ScreenGui
OpenBtn.Size = UDim2.new(0, 65, 0, 65)
OpenBtn.Position = UDim2.new(0.02, 0, 0.45, 0)
OpenBtn.Image = "rbxassetid://11234731338" -- Ícone de Carteira
OpenBtn.BackgroundColor3 = Color3.fromRGB(0, 80, 255)
OpenBtn.Visible = false
Instance.new("UICorner", OpenBtn)

-- Janela Principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 15) -- Fundo Preto/Azul Escuro
MainFrame.Size = UDim2.new(0, 520, 0, 380)
MainFrame.Position = UDim2.new(0.5, -260, 1.2, 0) -- Inicia em baixo
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame)
local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Color = Color3.fromRGB(0, 100, 255)
Stroke.Thickness = 2

-- Sidebar
local Sidebar = Instance.new("Frame", MainFrame)
Sidebar.Size = UDim2.new(0, 140, 1, 0)
Sidebar.BackgroundColor3 = Color3.fromRGB(10, 15, 30)
Instance.new("UICorner", Sidebar)
local SidebarLayout = Instance.new("UIListLayout", Sidebar)
SidebarLayout.Padding = UDim.new(0, 5)

-- Container de Conteúdo
local Content = Instance.new("Frame", MainFrame)
Content.Position = UDim2.new(0, 145, 0, 10)
Content.Size = UDim2.new(1, -155, 1, -20)
Content.BackgroundTransparency = 1

local TrollTab = Instance.new("ScrollingFrame", Content)
TrollTab.Size = UDim2.new(1, 0, 1, 0); TrollTab.BackgroundTransparency = 1; TrollTab.Visible = true; TrollTab.CanvasSize = UDim2.new(0,0,1.5,0)
Instance.new("UIListLayout", TrollTab).Padding = UDim.new(0, 8)

local ScriptsTab = Instance.new("ScrollingFrame", Content)
ScriptsTab.Size = UDim2.new(1, 0, 1, 0); ScriptsTab.BackgroundTransparency = 1; ScriptsTab.Visible = false
Instance.new("UIListLayout", ScriptsTab).Padding = UDim.new(0, 8)

local DiscordTab = Instance.new("Frame", Content)
DiscordTab.Size = UDim2.new(1, 0, 1, 0); DiscordTab.BackgroundTransparency = 1; DiscordTab.Visible = false

-- Animação de Abrir (Arrastar Carteira)
local function ToggleUI()
    if MainFrame.Position.Y.Scale > 0.5 then
        OpenBtn.Visible = false
        MainFrame:TweenPosition(UDim2.new(0.5, -260, 0.25, 0), "Out", "Back", 0.6)
    else
        MainFrame:TweenPosition(UDim2.new(0.5, -260, 1.2, 0), "In", "Quad", 0.5)
        task.wait(0.5)
        OpenBtn.Visible = true
    end
end
OpenBtn.MouseButton1Click:Connect(ToggleUI)

-- Funções para Criar Elementos
local function CreateTabBtn(name, callback)
    local btn = Instance.new("TextButton", Sidebar)
    btn.Size = UDim2.new(1, -10, 0, 40); btn.BackgroundColor3 = Color3.fromRGB(0, 60, 180); btn.Text = name; btn.TextColor3 = Color3.new(1, 1, 1); btn.Font = Enum.Font.SourceSansBold
    btn.MouseButton1Click:Connect(callback); Instance.new("UICorner", btn)
end

local function CreateActionBtn(name, parent, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(1, -10, 0, 35); btn.BackgroundColor3 = Color3.fromRGB(20, 40, 90); btn.Text = name; btn.TextColor3 = Color3.new(1, 1, 1)
    btn.MouseButton1Click:Connect(callback); Instance.new("UICorner", btn)
    return btn
end

-- --- SISTEMA DE SELEÇÃO DE PLAYER ---
local PlayerInfo = Instance.new("TextLabel", TrollTab)
PlayerInfo.Size = UDim2.new(1, -10, 0, 30); PlayerInfo.Text = "Alvo: Selecione um Jogador"; PlayerInfo.TextColor3 = Color3.fromRGB(0, 200, 255); PlayerInfo.BackgroundTransparency = 1

local ListFrame = Instance.new("ScrollingFrame", TrollTab)
ListFrame.Size = UDim2.new(1, -10, 0, 100); ListFrame.BackgroundColor3 = Color3.fromRGB(15, 20, 40); Instance.new("UIListLayout", ListFrame)

local function RefreshList()
    for _, v in pairs(ListFrame:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
    for _, p in pairs(Players:GetPlayers()) do
        local pBtn = Instance.new("TextButton", ListFrame)
        pBtn.Size = UDim2.new(1, 0, 0, 25); pBtn.Text = p.DisplayName; pBtn.BackgroundColor3 = Color3.fromRGB(30, 40, 80); pBtn.TextColor3 = Color3.new(1, 1, 1)
        pBtn.MouseButton1Click:Connect(function()
            States.SelectedPlayer = p
            PlayerInfo.Text = "Alvo: " .. p.DisplayName
        end)
    end
end
CreateActionBtn("🔄 Atualizar Lista de Players", TrollTab, RefreshList)

-- --- ABA TROLL ---
CreateTabBtn("Troll", function() TrollTab.Visible = true; ScriptsTab.Visible = false; DiscordTab.Visible = false end)

CreateActionBtn("Fling Player", TrollTab, function()
    if States.SelectedPlayer and States.SelectedPlayer.Character then
        local target = States.SelectedPlayer.Character.HumanoidRootPart
        LPlayer.Character.HumanoidRootPart.CFrame = target.CFrame
        local velocity = Instance.new("BodyAngularVelocity", LPlayer.Character.HumanoidRootPart)
        velocity.AngularVelocity = Vector3.new(0, 99999, 0); velocity.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        task.wait(0.3); velocity:Destroy()
    end
end)

CreateActionBtn("View (Espiar Jogador)", TrollTab, function()
    States.Viewing = not States.Viewing
    if States.Viewing and States.SelectedPlayer then
        workspace.CurrentCamera.CameraSubject = States.SelectedPlayer.Character.Humanoid
    else
        workspace.CurrentCamera.CameraSubject = LPlayer.Character.Humanoid
    end
end)

CreateActionBtn("Fly CLT (Voo)", TrollTab, function()
    States.Fly = not States.Fly
    if States.Fly then
        local clt = Instance.new("Part", LPlayer.Character); clt.Size = Vector3.new(0.6, 0.8, 0.2); clt.Color = Color3.new(0, 0, 1)
        local w = Instance.new("Weld", clt); w.Part0 = clt; w.Part1 = LPlayer.Character:FindFirstChild("Right Arm") or LPlayer.Character:FindFirstChild("RightHand")
        w.C0 = CFrame.new(0, 1, 0)
        local bv = Instance.new("BodyVelocity", LPlayer.Character.HumanoidRootPart); bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        spawn(function()
            while States.Fly do
                bv.Velocity = workspace.CurrentCamera.CFrame.LookVector * 100
                task.wait()
            end
            bv:Destroy(); clt:Destroy()
        end)
    end
end)

CreateActionBtn("ESP CLT All", TrollTab, function()
    States.Esp = not States.Esp
    if States.Esp then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LPlayer and p.Character then
                local b = Instance.new("BillboardGui", p.Character.Head)
                b.Name = "CLT_Tag"; b.Size = UDim2.new(0, 200, 0, 50); b.AlwaysOnTop = true; b.ExtentsOffset = Vector3.new(0, 3, 0)
                local t = Instance.new("TextLabel", b)
                t.Size = UDim2.new(1, 0, 1, 0); t.BackgroundTransparency = 1; t.Text = "📘 CLT DESPREGADO"; t.TextScaled = true
                spawn(function()
                    while States.Esp and b.Parent do
                        t.TextColor3 = Color3.fromRGB(0, 85, 255); task.wait(0.4)
                        t.TextColor3 = Color3.fromRGB(0, 0, 0); task.wait(0.4)
                    end
                    b:Destroy()
                end)
            end
        end
    end
end)

CreateActionBtn("Troll Anim (Dance/Speed)", TrollTab, function()
    States.Troll = not States.Troll
    LPlayer.Character.Humanoid.WalkSpeed = States.Troll and 150 or 16
    if States.Troll then
        local a = Instance.new("Animation"); a.AnimationId = "rbxassetid://337960820"; local l = LPlayer.Character.Humanoid:LoadAnimation(a); l:Play()
        spawn(function() while States.Troll do task.wait() end l:Stop() end)
    end
end)

CreateActionBtn("RPG Rainbow Name", TrollTab, function()
    States.Rainbow = not States.Rainbow
    print("Rainbow Brookhaven Mode: " .. tostring(States.Rainbow))
end)

-- --- ABA SCRIPTS ---
CreateTabBtn("Scripts", function() TrollTab.Visible = false; ScriptsTab.Visible = true; DiscordTab.Visible = false end)

CreateActionBtn("Cartola Hub (Copiar)", ScriptsTab, function()
    setclipboard('loadstring(game:HttpGet("https://raw.githubusercontent.com/Davi999z/Cartola-Hub/refs/heads/main/Brookhaven",true))()')
end)

CreateActionBtn("Lyra Hub (Copiar)", ScriptsTab, function()
    setclipboard('loadstring(game:HttpGet("https://raw.githubusercontent.com/kayrus999/Lyrapainel/refs/heads/main/Lyrabrookhaven"))()')
end)

-- --- ABA DISCORD ---
CreateTabBtn("Discord", function() TrollTab.Visible = false; ScriptsTab.Visible = false; DiscordTab.Visible = true end)

CreateActionBtn("Discord do CLT Hub (Copiar)", DiscordTab, function()
    setclipboard("https://discord.gg/gUJjJfD9f3")
end)

-- Finalização
RefreshList()
MainFrame.Visible = true
ToggleUI() -- Chama animação inicial
