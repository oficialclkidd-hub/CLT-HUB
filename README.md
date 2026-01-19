-- [[ CLT HUB 📘 - VERSÃO FINAL PREMIUM ]] --
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local LPlayer = Players.LocalPlayer

-- Estados (Toggles)
local _G = {
    Fly = false,
    Troll = false,
    Rainbow = false,
    Esp = false,
    SelectedPlayer = nil,
    Viewing = false
}

-- Criando a Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CLT_Hub_Final"
ScreenGui.Parent = game.CoreGui

-- Botão de Abrir (Foto da Carteira)
local OpenBtn = Instance.new("ImageButton")
OpenBtn.Name = "OpenBtn"
OpenBtn.Parent = ScreenGui
OpenBtn.Size = UDim2.new(0, 65, 0, 65)
OpenBtn.Position = UDim2.new(0.02, 0, 0.45, 0)
OpenBtn.Image = "rbxassetid://6031094678" -- Ícone de Carteira
OpenBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
OpenBtn.Visible = false
Instance.new("UICorner", OpenBtn)

-- Janela Principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 20, 45)
MainFrame.Size = UDim2.new(0, 500, 0, 380)
MainFrame.Position = UDim2.new(0.5, -250, 1.2, 0) -- Inicia em baixo
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame)
Instance.new("UIStroke", MainFrame).Color = Color3.fromRGB(0, 170, 255)

-- Sidebar (Abas)
local Sidebar = Instance.new("Frame", MainFrame)
Sidebar.Size = UDim2.new(0, 130, 1, 0)
Sidebar.BackgroundColor3 = Color3.fromRGB(5, 10, 25)
Instance.new("UICorner", Sidebar)
local SidebarLayout = Instance.new("UIListLayout", Sidebar)
SidebarLayout.Padding = UDim.new(0, 5)

-- Container de Conteúdo
local Content = Instance.new("Frame", MainFrame)
Content.Position = UDim2.new(0, 135, 0, 10)
Content.Size = UDim2.new(1, -145, 1, -20)
Content.BackgroundTransparency = 1

local TrollTab = Instance.new("ScrollingFrame", Content)
TrollTab.Size = UDim2.new(1, 0, 1, 0); TrollTab.BackgroundTransparency = 1; TrollTab.Visible = true; TrollTab.CanvasSize = UDim2.new(0,0,1.5,0)
Instance.new("UIListLayout", TrollTab).Padding = UDim.new(0, 8)

local ScriptsTab = Instance.new("ScrollingFrame", Content)
ScriptsTab.Size = UDim2.new(1, 0, 1, 0); ScriptsTab.BackgroundTransparency = 1; ScriptsTab.Visible = false
Instance.new("UIListLayout", ScriptsTab).Padding = UDim.new(0, 8)

local DiscordTab = Instance.new("Frame", Content)
DiscordTab.Size = UDim2.new(1, 0, 1, 0); DiscordTab.BackgroundTransparency = 1; DiscordTab.Visible = false

-- Funções de Animação de Abrir (Arrastar Carteira)
local function ToggleUI()
    if MainFrame.Position.Y.Scale > 0.5 then
        OpenBtn.Visible = false
        MainFrame:TweenPosition(UDim2.new(0.5, -250, 0.25, 0), "Out", "Back", 0.6)
    else
        MainFrame:TweenPosition(UDim2.new(0.5, -250, 1.2, 0), "In", "Quad", 0.5)
        task.wait(0.5)
        OpenBtn.Visible = true
    end
end
OpenBtn.MouseButton1Click:Connect(ToggleUI)

-- Função para Criar Botões
local function CreateTabBtn(name, callback)
    local btn = Instance.new("TextButton", Sidebar)
    btn.Size = UDim2.new(1, -10, 0, 40); btn.BackgroundColor3 = Color3.fromRGB(0, 80, 220); btn.Text = name; btn.TextColor3 = Color3.new(1, 1, 1); btn.Font = Enum.Font.SourceSansBold
    btn.MouseButton1Click:Connect(callback); Instance.new("UICorner", btn)
end

local function CreateActionBtn(name, parent, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(1, -10, 0, 40); btn.BackgroundColor3 = Color3.fromRGB(25, 50, 100); btn.Text = name; btn.TextColor3 = Color3.new(1, 1, 1)
    btn.MouseButton1Click:Connect(callback); Instance.new("UICorner", btn)
    return btn
end

-- --- SELEÇÃO DE PLAYER ---
local PlayerLabel = Instance.new("TextLabel", TrollTab)
PlayerLabel.Size = UDim2.new(1, -10, 0, 30); PlayerLabel.Text = "Alvo: Nenhum Selecionado"; PlayerLabel.TextColor3 = Color3.new(0, 1, 1); PlayerLabel.BackgroundTransparency = 1

local PlayerList = Instance.new("ScrollingFrame", TrollTab)
PlayerList.Size = UDim2.new(1, -10, 0, 100); PlayerList.BackgroundColor3 = Color3.fromRGB(20, 30, 60); Instance.new("UIListLayout", PlayerList)

local function RefreshPlayers()
    for _, v in pairs(PlayerList:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
    for _, p in pairs(Players:GetPlayers()) do
        local pBtn = Instance.new("TextButton", PlayerList)
        pBtn.Size = UDim2.new(1, 0, 0, 25); pBtn.Text = p.DisplayName; pBtn.BackgroundColor3 = Color3.fromRGB(40, 70, 130); pBtn.TextColor3 = Color3.new(1, 1, 1)
        pBtn.MouseButton1Click:Connect(function()
            _G.SelectedPlayer = p
            PlayerLabel.Text = "Alvo: " .. p.DisplayName
        end)
    end
end
CreateActionBtn("🔄 Atualizar Lista de Players", TrollTab, RefreshPlayers)

-- --- ABA TROLL ---
CreateTabBtn("Troll", function() TrollTab.Visible = true; ScriptsTab.Visible = false; DiscordTab.Visible = false end)

CreateActionBtn("Fling Player", TrollTab, function()
    if _G.SelectedPlayer and _G.SelectedPlayer.Character then
        local target = _G.SelectedPlayer.Character.HumanoidRootPart
        LPlayer.Character.HumanoidRootPart.CFrame = target.CFrame
        local bf = Instance.new("BodyAngularVelocity", LPlayer.Character.HumanoidRootPart)
        bf.AngularVelocity = Vector3.new(0, 99999, 0); bf.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        task.wait(0.3); bf:Destroy()
    end
end)

CreateActionBtn("View (Espiar Jogador)", TrollTab, function()
    _G.Viewing = not _G.Viewing
    if _G.Viewing and _G.SelectedPlayer then
        workspace.CurrentCamera.CameraSubject = _G.SelectedPlayer.Character.Humanoid
    else
        workspace.CurrentCamera.CameraSubject = LPlayer.Character.Humanoid
    end
end)

CreateActionBtn("Fly CLT (Voo)", TrollTab, function()
    _G.Fly = not _G.Fly
    if _G.Fly then
        local clt = Instance.new("Part", LPlayer.Character); clt.Name = "CLT_Hand"; clt.Size = Vector3.new(0.6, 0.8, 0.2); clt.Color = Color3.new(0, 0, 0.8)
        local w = Instance.new("Weld", clt); w.Part0 = clt; w.Part1 = LPlayer.Character:FindFirstChild("Right Arm") or LPlayer.Character:FindFirstChild("RightHand"); w.C0 = CFrame.new(0, 1, 0)
        local bv = Instance.new("BodyVelocity", LPlayer.Character.HumanoidRootPart); bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        spawn(function()
            while _G.Fly do
                bv.Velocity = workspace.CurrentCamera.CFrame.LookVector * 100
                task.wait()
            end
            bv:Destroy(); clt:Destroy()
        end)
    end
end)

CreateActionBtn("ESP CLT All", TrollTab, function()
    _G.Esp = not _G.Esp
    if _G.Esp then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LPlayer and p.Character then
                local b = Instance.new("BillboardGui", p.Character.Head)
                b.Name = "CLT_Tag"; b.Size = UDim2.new(0, 200, 0, 50); b.AlwaysOnTop = true; b.ExtentsOffset = Vector3.new(0, 3, 0)
                local t = Instance.new("TextLabel", b)
                t.Size = UDim2.new(1, 0, 1, 0); t.BackgroundTransparency = 1; t.Text = "📘 CLT DESPREGADO"; t.TextScaled = true
                spawn(function()
                    while _G.Esp and b.Parent do
                        t.TextColor3 = Color3.new(0, 0, 1); task.wait(0.3); t.TextColor3 = Color3.new(0, 0, 0); task.wait(0.3)
                    end
                    b:Destroy()
                end)
            end
        end
    end
end)

CreateActionBtn("Troll Anim (Dance/Speed)", TrollTab, function()
    _G.Troll = not _G.Troll
    LPlayer.Character.Humanoid.WalkSpeed = _G.Troll and 150 or 16
    if _G.Troll then
        local a = Instance.new("Animation"); a.AnimationId = "rbxassetid://337960820"; local l = LPlayer.Character.Humanoid:LoadAnimation(a); l:Play()
        spawn(function() while _G.Troll do task.wait() end l:Stop() end)
    end
end)

CreateActionBtn("RPG Rainbow Name", TrollTab, function()
    _G.Rainbow = not _G.Rainbow
    print("Rainbow Mode Brookhaven: ", _G.Rainbow)
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

local DiscBtn = CreateActionBtn("Discord do CLT Hub (Copiar)", DiscordTab, function()
    setclipboard("https://discord.gg/7RSTZybTuz")
end)
DiscBtn.Position = UDim2.new(0, 5, 0, 10)

-- Finalização
RefreshPlayers()
MainFrame.Visible = true
ToggleUI() -- Animação inicial
