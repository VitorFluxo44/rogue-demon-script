local CollectionService = game:GetService("CollectionService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local G2L = {}

-- Key correta
local CORRECT_KEY = "key123"

-- ScreenGui principal
G2L["ScreenGui_1"] = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
G2L["ScreenGui_1"].ZIndexBehavior = Enum.ZIndexBehavior.Sibling
CollectionService:AddTag(G2L["ScreenGui_1"], "main")
G2L["ScreenGui_1"].Enabled = true -- deixa visível para digitar a key

-- Frame principal
G2L["Frame_2"] = Instance.new("Frame", G2L["ScreenGui_1"])
G2L["Frame_2"].BorderSizePixel = 0
G2L["Frame_2"].BackgroundColor3 = Color3.fromRGB(0,0,0)
G2L["Frame_2"].Size = UDim2.new(0.32297,0,0.45989,0)
G2L["Frame_2"].Position = UDim2.new(0.32468,0,0.24527,0)

-- Título
G2L["Titulo_3"] = Instance.new("Key", G2L["Frame_2"])
G2L["Titulo_3"].TextWrapped = true
G2L["Titulo_3"].TextScaled = true
G2L["Titulo_3"].BackgroundTransparency = 1
G2L["Titulo_3"].TextColor3 = Color3.fromRGB(255,255,255)
G2L["Titulo_3"].Text = "Rogue Demon Script"
G2L["Titulo_3"].Position = UDim2.new(-0.04762,0,0.1,0)
G2L["Titulo_3"].Size = UDim2.new(1.07407,0,0.325,0)
G2L["Titulo_3"].FontFace = Font.new([[rbxasset://fonts/families/FredokaOne.json]])

-- UICorner
Instance.new("UICorner", G2L["Frame_2"])

-- UIStroke
local stroke = Instance.new("UIStroke", G2L["Frame_2"])
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(10,200,255)

-- UIAspectRatio
local aspect = Instance.new("UIAspectRatioConstraint", G2L["Frame_2"])
aspect.AspectRatio = 1.575

-- Texto: Key no Discord
G2L["TextLabel_7"] = Instance.new("TextLabel", G2L["Frame_2"])
G2L["TextLabel_7"].TextWrapped = true
G2L["TextLabel_7"].TextScaled = true
G2L["TextLabel_7"].BackgroundTransparency = 1
G2L["TextLabel_7"].TextColor3 = Color3.fromRGB(255,252,252)
G2L["TextLabel_7"].Text = "Pegar Key no Discord"
G2L["TextLabel_7"].Position = UDim2.new(0.08466,0,0.63333,0)
G2L["TextLabel_7"].Size = UDim2.new(0.79365,0,0.09167,0)
G2L["TextLabel_7"].FontFace = Font.new([[rbxasset://fonts/families/FredokaOne.json]])

-- Botão copiar link Discord
G2L["ColocarLinkDaKeyDiscord_8"] = Instance.new("TextButton", G2L["Frame_2"])
G2L["ColocarLinkDaKeyDiscord_8"].TextWrapped = true
G2L["ColocarLinkDaKeyDiscord_8"].TextScaled = true
G2L["ColocarLinkDaKeyDiscord_8"].TextColor3 = Color3.fromRGB(255,255,255)
G2L["ColocarLinkDaKeyDiscord_8"].BackgroundColor3 = Color3.fromRGB(40,106,255)
G2L["ColocarLinkDaKeyDiscord_8"].Text = "Adquirir Key 🔗"
G2L["ColocarLinkDaKeyDiscord_8"].Position = UDim2.new(0.12169,0,0.75833,0)
G2L["ColocarLinkDaKeyDiscord_8"].Size = UDim2.new(0.60847,0,0.175,0)
Instance.new("UICorner", G2L["https://discord.gg/2WxRnXK4Jk"])

-- Botão enviar key
G2L["ClicarParaEnviarKey_a"] = Instance.new("TextButton", G2L["Frame_2"])
G2L["ClicarParaEnviarKey_a"].TextWrapped = true
G2L["ClicarParaEnviarKey_a"].TextSize = 20
G2L["ClicarParaEnviarKey_a"].BackgroundColor3 = Color3.fromRGB(81,81,81)
G2L["ClicarParaEnviarKey_a"].Text = "▶️"
G2L["ClicarParaEnviarKey_a"].Position = UDim2.new(0.77249,0,0.75833,0)
G2L["ClicarParaEnviarKey_a"].Size = UDim2.new(0.16402,0,0.16667,0)
Instance.new("UICorner", G2L["ClicarParaEnviarKey_a"])

-- Caixa de digitar a key
G2L["PutKey_c"] = Instance.new("TextBox", G2L["Frame_2"])
G2L["PutKey_c"].CursorPosition = -1
G2L["PutKey_c"].TextWrapped = true
G2L["PutKey_c"].TextScaled = true
G2L["PutKey_c"].BackgroundColor3 = Color3.fromRGB(255,255,255)
G2L["PutKey_c"].Size = UDim2.new(0.73016,0,0.16667,0)
G2L["PutKey_c"].Position = UDim2.new(0.12698,0,0.45,0)
Instance.new("UICorner", G2L["PutKey_c"])

-- BOTÕES
local keyBox = G2L["PutKey_c"]
local sendButton = G2L["ClicarParaEnviarKey_a"]
local discordButton = G2L["ColocarLinkDaKeyDiscord_8"]
local screenGui = G2L["ScreenGui_1"]

-- Clicar para enviar key
sendButton.MouseButton1Click:Connect(function()
    local playerKey = keyBox.Text
    if playerKey == CORRECT_KEY then

-- -- ROGUE DEMON

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================= CONFIG =================
local CAMLOCK_FOV = 250
local CAMLOCK_SMOOTH = 1.0
local HBE_SIZE = 15
local M1_RANGE = 15
local JUMP_UP = 60
local JUMP_SIDE = 60
local JUMP_COOLDOWN = 1.0

-- ================= STATE =================
local Camlock = false
local HBE = false
local LockedTarget = nil
local jumpCD = 0
local HBE_CACHE = {}

-- ================= UTILS =================
local function getHRP(c) return c and c:FindFirstChild("HumanoidRootPart") end
local function getHum(c) return c and c:FindFirstChildWhichIsA("Humanoid") end

-- ================= HBE (SUAVE + DINÂMICO) =================
local function applyHBE(char)
    local hrp = getHRP(char)
    local hum = getHum(char)
    if not hrp or not hum or hum.Health <= 0 then return end

    if HBE then
        if not HBE_CACHE[char] then
            HBE_CACHE[char] = hrp.Size
        end

        hrp.Size = Vector3.new(HBE_SIZE, HBE_SIZE, HBE_SIZE)
        hrp.Transparency = 0.65
        hrp.Material = Enum.Material.Plastic

        local myHRP = getHRP(player.Character)
        if myHRP then
            local dist = (myHRP.Position - hrp.Position).Magnitude

            if Camlock and LockedTarget == hrp and dist <= M1_RANGE then
                hrp.Color = Color3.fromRGB(0,190,90)      -- VERDE
            elseif dist <= M1_RANGE then
                hrp.Color = Color3.fromRGB(90,150,255)   -- AZUL
            else
                hrp.Color = Color3.fromRGB(170,80,80)    -- VERMELHO SUAVE
            end
        end
    else
        if HBE_CACHE[char] then
            hrp.Size = HBE_CACHE[char]
            HBE_CACHE[char] = nil
        end
        hrp.Transparency = 1
        hrp.Material = Enum.Material.Plastic
    end
end

-- ================= CAMLOCK AIM (PATCH SEGURO) =================
local AimCircle

local function removeAim()
    if AimCircle then
        AimCircle:Destroy()
        AimCircle = nil
    end
end

local function applyAim(hrp)
    if not hrp then return end
    if AimCircle and AimCircle.Adornee == hrp then return end

    removeAim()

    local gui = Instance.new("BillboardGui")
    gui.Name = "CamlockAim"
    gui.Adornee = hrp
    gui.Size = UDim2.new(3.2,0,3.2,0)
    gui.StudsOffset = Vector3.new(0,0,0)
    gui.AlwaysOnTop = true

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.fromScale(1,1)
    frame.BackgroundTransparency = 1

    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = Color3.fromRGB(0,0,0)
    stroke.Thickness = 2

    local corner = Instance.new("UICorner", frame)
    corner.CornerRadius = UDim.new(1,0)

    gui.Parent = hrp
    AimCircle = gui
end

-- ================= GRADIENT =================
local function addGradient(obj)
    local g = Instance.new("UIGradient")
    g.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(80,0,140)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(180,90,255))
    }
    g.Rotation = 90
    g.Parent = obj
end

-- ================= GUI =================
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.ResetOnSpawn = false

-- ===== AVMS =====
local avms = Instance.new("Frame", gui)
avms.Size = UDim2.new(0,420,0,26)
avms.Position = UDim2.new(0.5,-210,0,6)
avms.BackgroundColor3 = Color3.fromRGB(120,0,180)
Instance.new("UICorner", avms).CornerRadius = UDim.new(0,10)
addGradient(avms)

local avmsStroke = Instance.new("UIStroke", avms)
avmsStroke.Color = Color3.new(0,0,0)
avmsStroke.Thickness = 5

local avmsText = Instance.new("TextLabel", avms)
avmsText.Size = UDim2.fromScale(1,1)
avmsText.BackgroundTransparency = 1
avmsText.Font = Enum.Font.RobotoMono
avmsText.TextSize = 15
avmsText.TextColor3 = Color3.new(1,1,1)
avmsText.Text = "Romanov"

-- ===== TABELA =====
local panel = Instance.new("Frame", gui)
panel.Position = UDim2.new(0,12,0,40)
panel.Size = UDim2.new(0,300,0,155)
panel.BackgroundColor3 = Color3.fromRGB(120,0,180)
Instance.new("UICorner", panel).CornerRadius = UDim.new(0,12)
addGradient(panel)

local panelStroke = Instance.new("UIStroke", panel)
panelStroke.Color = Color3.new(0,0,0)
panelStroke.Thickness = 5

local function label(y,text)
    local t = Instance.new("TextLabel", panel)
    t.Position = UDim2.new(0,14,0,y)
    t.Size = UDim2.new(1,-28,0,22)
    t.BackgroundTransparency = 1
    t.Font = Enum.Font.RobotoMono
    t.TextSize = 16
    t.TextColor3 = Color3.new(1,1,1)
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.Text = text
    return t
end

local function divider(y)
    local d = Instance.new("Frame", panel)
    d.Position = UDim2.new(0,14,0,y)
    d.Size = UDim2.new(1,-28,0,2)
    d.BackgroundColor3 = Color3.new(0,0,0)
end

label(6,"💜 Rogue Demon")
divider(32)
local lblCam = label(38,"🎯 Camlock [E]: OFF")
divider(64)
local lblHBE = label(70,"📦 HBE [Y]: OFF")
divider(96)
local lblJump = label(102,"🦘 Jump [B]: READY")

-- ================= VIDA HUD =================
local function createHealthHUD(char)
    local hum = char:WaitForChild("Humanoid",5)
    local head = char:WaitForChild("Head",5)
    if not hum or not head then return end

    if head:FindFirstChild("HealthHUD") then
        head.HealthHUD:Destroy()
    end

    local hud = Instance.new("BillboardGui", head)
    hud.Name = "HealthHUD"
    hud.Size = UDim2.new(4.5,0,0.7,0)
    hud.StudsOffset = Vector3.new(0,2.9,0)
    hud.AlwaysOnTop = true

    local bg = Instance.new("Frame", hud)
    bg.Size = UDim2.fromScale(1,1)
    bg.BackgroundColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", bg).CornerRadius = UDim.new(0,8)

    local bar = Instance.new("Frame", bg)
    bar.BackgroundColor3 = Color3.fromRGB(140,0,200)
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0,8)
    addGradient(bar)

    local txt = Instance.new("TextLabel", bg)
    txt.Size = UDim2.fromScale(1,1)
    txt.BackgroundTransparency = 1
    txt.Font = Enum.Font.RobotoMono
    txt.TextScaled = true
    txt.TextColor3 = Color3.new(0,0,0)

    local function update()
        if hum.Health <= 0 then return end
        local ratio = math.clamp(hum.Health / hum.MaxHealth, 0, 1)
        bar.Size = UDim2.new(ratio,0,1,0)
        txt.Text = tostring(math.floor(hum.Health))
    end

    update()
    hum.HealthChanged:Connect(update)
end

local function setupPlayer(p)
    p.CharacterAdded:Connect(function(char)
        createHealthHUD(char)
        task.wait(0.1)
        applyHBE(char)
    end)
    if p.Character then
        createHealthHUD(p.Character)
        applyHBE(p.Character)
    end
end

for _,p in ipairs(Players:GetPlayers()) do
    if p ~= player then setupPlayer(p) end
end
Players.PlayerAdded:Connect(setupPlayer)

-- ================= CAMLOCK =================
local function acquireTarget()
    local best, dist = nil, math.huge
    local mouse = UIS:GetMouseLocation()

    for _,p in ipairs(Players:GetPlayers()) do
        if p ~= player then
            local hrp = getHRP(p.Character)
            local hum = getHum(p.Character)
            if hrp and hum and hum.Health > 0 then
                local pos,vis = camera:WorldToViewportPoint(hrp.Position)
                if vis then
                    local d = (Vector2.new(pos.X,pos.Y)-mouse).Magnitude
                    if d < dist and d <= CAMLOCK_FOV then
                        dist = d
                        best = hrp
                    end
                end
            end
        end
    end
    return best
end

-- ================= LOOP =================
RunService.RenderStepped:Connect(function(dt)
    lblCam.Text = "🎯 Camlock [E]: "..(Camlock and "ON" or "OFF")
    lblHBE.Text = "📦 HBE [Y]: "..(HBE and "ON" or "OFF")

    if jumpCD > 0 then
        jumpCD = math.max(0, jumpCD - dt)
        lblJump.Text = string.format("🦘 Jump: %.1fs", jumpCD)
    else
        lblJump.Text = "🦘 Jump [B]: READY"
    end

    if Camlock then
        if not LockedTarget or not LockedTarget.Parent then
            LockedTarget = acquireTarget()
        end
        if LockedTarget then
            camera.CFrame = camera.CFrame:Lerp(
                CFrame.new(camera.CFrame.Position, LockedTarget.Position),
                CAMLOCK_SMOOTH
            )
            applyAim(LockedTarget)
        end
    else
        LockedTarget = nil
        removeAim()
    end

    if HBE then
        for _,p in ipairs(Players:GetPlayers()) do
            if p ~= player and p.Character then
                applyHBE(p.Character)
            end
        end
    end
end)

-- ================= INPUT =================
UIS.InputBegan:Connect(function(i,gp)
    if gp then return end

    if i.KeyCode == Enum.KeyCode.E then
        Camlock = not Camlock
        LockedTarget = nil
        removeAim()

    elseif i.KeyCode == Enum.KeyCode.Y then
        HBE = not HBE
        for _,p in ipairs(Players:GetPlayers()) do
            if p ~= player and p.Character then
                applyHBE(p.Character)
            end
        end

    elseif i.KeyCode == Enum.KeyCode.B and jumpCD <= 0 then
        local hrp = getHRP(player.Character)
        local hum = getHum(player.Character)
        if hrp and hum then
            local dir = hum.MoveDirection
            hrp.AssemblyLinearVelocity = Vector3.new(
                dir.X * JUMP_SIDE,
                JUMP_UP,
                dir.Z * JUMP_SIDE
            )
            jumpCD = JUMP_COOLDOWN
        end
    end
end)

print("✔ SCRIPT ROGUE DEMON | SEM QUEBRAR HUD, AVMS, JUMP OU GRADIENTE")


-- Colar o script sempre acima disso 🔼
        
        print("Key correta! Abrindo seu Hub...")
        screenGui:Destroy() -- destrói a tela da key, opcional
    else
        keyBox.BackgroundColor3 = Color3.fromRGB(255,0,0)
        print("Key incorreta ❌")
    end
end)

-- Botão copiar link Discord
discordButton.MouseButton1Click:Connect(function()
    setclipboard("https://discord.gg/nSVkmuq65")
    print("Link copiado para o clipboard 🔗")
end)

return G2L["ScreenGui_1"], require
