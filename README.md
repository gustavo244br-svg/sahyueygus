local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- CRIA GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MM2CheatGui"
ScreenGui.ResetOnSpawn = false

-- CRIA FRAME
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 220, 0, 210)
MainFrame.Position = UDim2.new(0, 20, 0, 120)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 1
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- TITULO
local title = Instance.new("TextLabel")
title.Text = "MM2 Hack GUI"
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0,0,0,0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(225,225,225)
title.Font = Enum.Font.SourceSansBold
title.TextScaled = true
title.Parent = MainFrame

-- Funções extras
local function createButton(name, position, callback)
    local btn = Instance.new("TextButton")
    btn.Name = name.."_Btn"
    btn.Text = name
    btn.Size = UDim2.new(0,200,0,40)
    btn.Position = UDim2.new(0,10,0,position)
    btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSans
    btn.TextScaled = true
    btn.Parent = MainFrame
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- [ESP] Highlight no Murder e Xerife
local highlightTable = {}
local espActive = false
function toggleESP()
    espActive = not espActive
    -- Remove highlights antigos
    for _, h in pairs(highlightTable) do
        if h then h:Destroy() end
    end
    highlightTable = {}
    if espActive then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr == LocalPlayer then continue end
            local char = plr.Character
            if char then
                -- Identifica roles
                if char:FindFirstChild("Knife") then -- Murder
                    local highlight = Instance.new("Highlight")
                    highlight.FillColor = Color3.new(1,0,0) -- Vermelho
                    highlight.OutlineColor = Color3.new(1,0,0)
                    highlight.Parent = char
                    highlight.Adornee = char
                    highlightTable[plr.Name] = highlight
                elseif char:FindFirstChild("Gun") or plr.Backpack:FindFirstChild("Gun") then -- Sheriff
                    local highlight = Instance.new("Highlight")
                    highlight.FillColor = Color3.new(0,0,1) -- Azul
                    highlight.OutlineColor = Color3.new(0,0,1)
                    highlight.Parent = char
                    highlight.Adornee = char
                    highlightTable[plr.Name] = highlight
                end
            end
        end
    end
end

-- [FLING] Arremessa Murder ou Xerife
function getRolePlayer(role)
    for _, plr in pairs(Players:GetPlayers()) do
        if plr == LocalPlayer then continue end
        if plr.Character then
            if role == "Murder" and plr.Character:FindFirstChild("Knife") then
                return plr
            elseif role == "Sheriff" and (plr.Character:FindFirstChild("Gun") or plr.Backpack:FindFirstChild("Gun")) then
                return plr
            end
        end
    end
end

function flingPlayer(player)
    local char = player and player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        local root = char.HumanoidRootPart
        root.AssemblyLinearVelocity = Vector3.new(1000,1000,1000) -- Fling
    end
end

-- [KILL INOCENTES]
function killInnocents()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr == LocalPlayer then continue end
        local char = plr.Character
        if char and char:FindFirstChild("Humanoid") then
            if not char:FindFirstChild("Knife") and not char:FindFirstChild("Gun") and not plr.Backpack:FindFirstChild("Gun") then
                char.Humanoid.Health = 0
            end
        end
    end
end

-- [BOTÕES]
createButton("ESP Murder/Xerife", 45, toggleESP)
createButton("Fling Murder", 95, function()
    local murder = getRolePlayer("Murder")
    if murder then
        flingPlayer(murder)
    end
end)
createButton("Fling Xerife", 145, function()
    local sheriff = getRolePlayer("Sheriff")
    if sheriff then
        flingPlayer(sheriff)
    end
end)
createButton("Kill Inocentes", 195, killInnocents)

-- Adiciona GUI na tela
ScreenGui.Parent = game:GetService("CoreGui")

-- Clean up highlights ao trocar de round/personagem
Players.PlayerRemoving:Connect(function(plr)
    if highlightTable[plr.Name] then
        highlightTable[plr.Name]:Destroy()
        highlightTable[plr.Name] = nil
    end
end)
