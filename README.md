local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-- CONFIG ------------------------------------------------
local CORRECT_KEY = "key123" -- <-- coloque aqui a sua key real
local DISCORD_LINK = "https://discord.gg/2WxRnXK4Jk" -- <-- SEU LINK (já inserido)
-- -------------------------------------------------------

-- Função para mostrar notificação temporária na tela
local function showNotification(text, duration)
    duration = duration or 2
    local notifGui = Instance.new("ScreenGui")
    notifGui.Name = "TempNotification"
    notifGui.ResetOnSpawn = false
    notifGui.Parent = PlayerGui

    local frame = Instance.new("Frame", notifGui)
    frame.AnchorPoint = Vector2.new(0.5, 0)
    frame.Size = UDim2.new(0.3, 0, 0.06, 0)
    frame.Position = UDim2.new(0.5, 0, 0.02, 0)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
    frame.BackgroundTransparency = 0.15
    Instance.new("UICorner", frame)

    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.fromScale(1,1)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255,255,255)
    label.TextScaled = true
    label.Font = Enum.Font.SourceSansBold
    label.Text = text

    task.delay(duration, function()
        pcall(function() notifGui:Destroy() end)
    end)
end

-- Função utilitária para copiar o link do Discord (uso pcall para evitar erros)
local function copyDiscordLink()
    local ok, err = pcall(function()
        setclipboard(DISCORD_LINK)
    end)
    if ok then
        showNotification("Link do Discord copiado!", 2)
        print("Link do Discord copiado para o clipboard.")
    else
        warn("Não foi possível copiar para o clipboard:", err)
        showNotification("Falha ao copiar link.", 2)
    end
end

-- Helpers para gerenciamento de conexões do Rogue
local RogueStarted = false
local rogueConnections = {}

local function addRogueConnection(conn)
    if conn then table.insert(rogueConnections, conn) end
end

local function clearRogueConnections()
    for _,c in ipairs(rogueConnections) do
        if c then
            pcall(function()
                if type(c.Disconnect) == "function" then
                    c:Disconnect()
                elseif type(c.disconnect) == "function" then
                    c:disconnect()
                end
            end)
        end
    end
    rogueConnections = {}
end

-- ======= CRIA O MENU (seu menu, adaptado) =======
local function createMenuGui()
    local rootGui = Instance.new("ScreenGui")
    rootGui.Name = "HubMenu"
    rootGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    local frame = Instance.new("Frame", rootGui)
    frame.Name = "MainFrame"
    frame.BorderSizePixel = 0
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    frame.Size = UDim2.new(0.49722, 0, 0.47743, 0)
    frame.Position = UDim2.new(0.2875, 0, 0.25178, 0)
    Instance.new("UICorner", frame)

    -- Título
    local title = Instance.new("TextLabel", frame)
    title.TextWrapped = true
    title.BorderSizePixel = 0
    title.TextSize = 30
    title.BackgroundColor3 = Color3.fromRGB(0,0,0)
    title.FontFace = Font.new([[rbxasset://fonts/families/Arial.json]], Enum.FontWeight.Bold, Enum.FontStyle.Normal)
    title.TextColor3 = Color3.fromRGB(255,0,0)
    title.Size = UDim2.new(0.95531, 0, 0.12935, 0)
    title.Position = UDim2.new(0.01397, 0, 0.00995, 0)
    title.Text = "Rogue Demon Script"

    -- Painel esquerdo (botões)
    local left = Instance.new("Frame", frame)
    left.BorderSizePixel = 0
    left.BackgroundColor3 = Color3.fromRGB(71,71,71)
    left.Size = UDim2.new(0.2514, 0, 0.73134, 0)
    left.Position = UDim2.new(0.03911, 0, 0.13433, 0)
    Instance.new("UICorner", left)

    -- Botão Inicio
    local btnMain = Instance.new("TextButton", frame)
    btnMain.Name = "Main"
    btnMain.BorderSizePixel = 0
    btnMain.TextSize = 30
    btnMain.TextColor3 = Color3.fromRGB(255,255,255)
    btnMain.BackgroundColor3 = Color3.fromRGB(50,0,255)
    btnMain.FontFace = Font.new([[rbxasset://fonts/families/Arial.json]], Enum.FontWeight.Bold, Enum.FontStyle.Normal)
    btnMain.Size = UDim2.new(0.18715, 0, 0.08458, 0)
    btnMain.Text = "Inicio"
    btnMain.Position = UDim2.new(0.06145, 0, 0.18408, 0)
    Instance.new("UICorner", btnMain)

    -- Botão Key (reabre o campo de key)
    local btnKey = Instance.new("TextButton", frame)
    btnKey.Name = "BotaoKey"
    btnKey.BorderSizePixel = 0
    btnKey.TextSize = 30
    btnKey.TextColor3 = Color3.fromRGB(255,255,255)
    btnKey.BackgroundColor3 = Color3.fromRGB(50,0,255)
    btnKey.FontFace = Font.new([[rbxasset://fonts/families/Arial.json]], Enum.FontWeight.Bold, Enum.FontStyle.Normal)
    btnKey.Size = UDim2.new(0.18715, 0, 0.08955, 0)
    btnKey.Text = "Key"
    btnKey.Position = UDim2.new(0.06145, 0, 0.28856, 0)
    Instance.new("UICorner", btnKey)

    -- Botão Pegar Key (transparent) - mantém, mas usamos copy function também
    local pegarKeyBtn = Instance.new("TextButton", frame)
    pegarKeyBtn.Name = "PegarKey"
    pegarKeyBtn.BorderSizePixel = 0
    pegarKeyBtn.TextSize = 22
    pegarKeyBtn.TextColor3 = Color3.fromRGB(255,255,255)
    pegarKeyBtn.BackgroundTransparency = 1
    pegarKeyBtn.Size = UDim2.new(0.2, 0, 0.08, 0)
    pegarKeyBtn.Text = "Pegar Key"
    pegarKeyBtn.Position = UDim2.new(0.52793, 0, 0.24876, 0)

    -- Painel direito (área principal)
    local content = Instance.new("Frame", frame)
    content.Name = "Content"
    content.Size = UDim2.new(0.6, 0, 0.7, 0)
    content.Position = UDim2.new(0.35, 0, 0.18, 0)
    content.BackgroundColor3 = Color3.fromRGB(71,71,71)
    Instance.new("UICorner", content)

    local contentLabel = Instance.new("TextLabel", content)
    contentLabel.Size = UDim2.new(1,0,0.2,0)
    contentLabel.Position = UDim2.new(0,0,0,0)
    contentLabel.BackgroundTransparency = 1
    contentLabel.TextColor3 = Color3.fromRGB(255,255,255)
    contentLabel.Text = "Aperte Abaixo"
    contentLabel.TextScaled = true

    -- Área de key (integrada ao menu)
    local keyFrame = Instance.new("Frame", content)
    keyFrame.Name = "KeyFrame"
    keyFrame.Size = UDim2.new(0.9,0,0.25,0)
    keyFrame.Position = UDim2.new(0.05,0,0.25,0)
    keyFrame.BackgroundTransparency = 1

    local keyBox = Instance.new("TextBox", keyFrame)
    keyBox.Name = "KeyBox"
    keyBox.Size = UDim2.new(0.72,0,0.5,0)
    keyBox.Position = UDim2.new(0,0,0.25,0)
    keyBox.BackgroundColor3 = Color3.fromRGB(255,255,255)
    keyBox.Text = ""
    keyBox.ClearTextOnFocus = false
    keyBox.PlaceholderText = "Digite sua key aqui"
    keyBox.TextScaled = true
    Instance.new("UICorner", keyBox)

    local sendBtn = Instance.new("TextButton", keyFrame)
    sendBtn.Name = "SendKey"
    sendBtn.Size = UDim2.new(0.24,0,0.5,0)
    sendBtn.Position = UDim2.new(0.76,0,0.25,0)
    sendBtn.BackgroundColor3 = Color3.fromRGB(81,81,81)
    sendBtn.Text = "▶️"
    sendBtn.TextScaled = true
    Instance.new("UICorner", sendBtn)

    -- BOTÃO: Copiar (abaixo da caixa de key)
    local copyBtn = Instance.new("TextButton", keyFrame)
    copyBtn.Name = "CopyDiscord"
    copyBtn.Size = UDim2.new(0.44, 0, 0.25, 0)
    copyBtn.Position = UDim2.new(0, 0, 0.78, 0)
    copyBtn.BackgroundColor3 = Color3.fromRGB(40,106,255)
    copyBtn.TextColor3 = Color3.fromRGB(255,255,255)
    copyBtn.Text = "Copiar Discord Para Pegar Key"
    copyBtn.TextScaled = true
    Instance.new("UICorner", copyBtn)

    -- Rodapé
    local footer = Instance.new("TextLabel", frame)
    footer.Size = UDim2.new(0.94972, 0, 0.07463, 0)
    footer.Position = UDim2.new(0.02514, 0, 0.9005, 0)
    footer.BackgroundColor3 = Color3.fromRGB(0,0,0)
    footer.FontFace = Font.new([[rbxasset://fonts/families/Arial.json]], Enum.FontWeight.Bold, Enum.FontStyle.Normal)
    footer.TextColor3 = Color3.fromRGB(255,6,6)
    footer.TextSize = 25
    footer.Text = "Script Desenvolvido por Misterio e Avatar"
    footer.BackgroundTransparency = 1

    -- Return useful refs
    return {
        ScreenGui = rootGui,
        KeyBox = keyBox,
        SendKeyButton = sendBtn,
        KeyFrame = keyFrame,
        MainButton = btnMain,
        KeyButton = btnKey,
        PegarKeyButton = pegarKeyBtn,
        CopyButton = copyBtn,
        Content = content
    }
end

-- ======= ROGUE DEMON (seu script completo) =======
local function startRogueDemon()
    if RogueStarted then return end
    RogueStarted = true
    clearRogueConnections()

    -- garantir GUI anterior removida
    local existing = PlayerGui:FindFirstChild("RogueDemonClient")
    if existing then existing:Destroy() end

    -- helpers
    local function addGradient(obj)
        local g = Instance.new("UIGradient")
        g.Color = ColorSequence.new{
            ColorSequenceKeypoint.new(0, Color3.fromRGB(80,0,140)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(180,90,255))
        }
        g.Rotation = 90
        g.Parent = obj
    end

    -- cria GUI principal do Rogue
    local gui = Instance.new("ScreenGui", PlayerGui)
    gui.Name = "RogueDemonClient"
    gui.ResetOnSpawn = false

    -- AVMS
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
    avmsText.Text = "Rogue Demon"

    -- Panel
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

    -- STATE e CONFIG do Rogue
    local CAMLOCK_FOV = 250
    local CAMLOCK_SMOOTH = 1.0
    local HBE_SIZE = 15
    local M1_RANGE = 15
    local JUMP_UP = 60
    local JUMP_SIDE = 60
    local JUMP_COOLDOWN = 1.0

    local Camlock = false
    local HBE = false
    local LockedTarget = nil
    local jumpCD = 0
    local HBE_CACHE = {}

    local function getHRP(c) return c and c:FindFirstChild("HumanoidRootPart") end
    local function getHum(c) return c and c:FindFirstChildWhichIsA("Humanoid") end

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
                    hrp.Color = Color3.fromRGB(0,190,90)
                elseif dist <= M1_RANGE then
                    hrp.Color = Color3.fromRGB(90,150,255)
                else
                    hrp.Color = Color3.fromRGB(170,80,80)
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
        local gui2 = Instance.new("BillboardGui")
        gui2.Name = "CamlockAim"
        gui2.Adornee = hrp
        gui2.Size = UDim2.new(3.2,0,3.2,0)
        gui2.StudsOffset = Vector3.new(0,0,0)
        gui2.AlwaysOnTop = true

        local frame2 = Instance.new("Frame", gui2)
        frame2.Size = UDim2.fromScale(1,1)
        frame2.BackgroundTransparency = 1

        local stroke = Instance.new("UIStroke", frame2)
        stroke.Color = Color3.fromRGB(0,0,0)
        stroke.Thickness = 2

        local corner = Instance.new("UICorner", frame2)
        corner.CornerRadius = UDim.new(1,0)

        gui2.Parent = hrp
        AimCircle = gui2
    end

    -- Health HUD
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
        local conn = hum.HealthChanged:Connect(update)
        addRogueConnection(conn)
    end

    local function setupPlayer(p)
        local conn = p.CharacterAdded:Connect(function(char)
            createHealthHUD(char)
            task.wait(0.1)
            applyHBE(char)
        end)
        addRogueConnection(conn)
        if p.Character then
            createHealthHUD(p.Character)
            applyHBE(p.Character)
        end
    end

    for _,p in ipairs(Players:GetPlayers()) do
        if p ~= player then setupPlayer(p) end
    end
    addRogueConnection(Players.PlayerAdded:Connect(setupPlayer))

    local function acquireTarget()
        local best, dist = nil, math.huge
        local mouse = UIS:GetMouseLocation()
        local cam = workspace.CurrentCamera
        for _,p in ipairs(Players:GetPlayers()) do
            if p ~= player then
                local hrp = getHRP(p.Character)
                local hum = getHum(p.Character)
                if hrp and hum and hum.Health > 0 then
                    local pos,vis = cam:WorldToViewportPoint(hrp.Position)
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

    -- Render loop
    local renderConn = RunService.RenderStepped:Connect(function(dt)
        if lblCam then lblCam.Text = "🎯 Camlock [E]: "..(Camlock and "ON" or "OFF") end
        if lblHBE then lblHBE.Text = "📦 HBE [Y]: "..(HBE and "ON" or "OFF") end

        if jumpCD > 0 then
            jumpCD = math.max(0, jumpCD - dt)
            if lblJump then lblJump.Text = string.format("🦘 Jump: %.1fs", jumpCD) end
        else
            if lblJump then lblJump.Text = "🦘 Jump [B]: READY" end
        end

        if Camlock then
            if not LockedTarget or not LockedTarget.Parent then
                LockedTarget = acquireTarget()
            end
            if LockedTarget then
                local cam = workspace.CurrentCamera
                cam.CFrame = cam.CFrame:Lerp(
                    CFrame.new(cam.CFrame.Position, LockedTarget.Position),
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
    addRogueConnection(renderConn)

    -- Input
    local inputConn = UIS.InputBegan:Connect(function(i,gp)
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
    addRogueConnection(inputConn)

    print("✔ SCRIPT ROGUE DEMON | INICIADO")
end

local function stopRogueDemon()
    local gui = PlayerGui:FindFirstChild("RogueDemonClient")
    if gui then gui:Destroy() end
    clearRogueConnections()
    RogueStarted = false
    print("✖ SCRIPT ROGUE DEMON | PARADO")
end

-- ======= ORQUESTRAÇÃO: menu aparece primeiro =======
local menu = createMenuGui()
menu.ScreenGui.Parent = PlayerGui

local function showKeyFrame()
    if menu.KeyFrame then
        menu.KeyFrame.Visible = true
        pcall(function() menu.KeyBox:CaptureFocus() end)
    end
end

-- Enviar key: SE CORRETA -> DESTROI O MENU E INICIA O ROGUE DEMON
menu.SendKeyButton.MouseButton1Click:Connect(function()
    local typed = (menu.KeyBox.Text or "")
    if typed == CORRECT_KEY then
        if menu.ScreenGui and menu.ScreenGui.Parent then
            menu.ScreenGui:Destroy()
        end
        print("Key correta! Menu removido. Iniciando Rogue Demon...")
        startRogueDemon()
    else
        menu.KeyBox.BackgroundColor3 = Color3.fromRGB(255,0,0)
        print("Key incorreta ❌")
        showNotification("Key incorreta ❌", 2)
    end
end)

-- Botão Key: reabre o campo de key (se o menu existir). Se o menu foi destruído, re-cria o menu.
menu.KeyButton.MouseButton1Click:Connect(function()
    if not menu.ScreenGui.Parent then
        menu = createMenuGui()
        menu.ScreenGui.Parent = PlayerGui
    end
    showKeyFrame()
    if RogueStarted then
        stopRogueDemon()
    end
end)

-- Botão Pegar Key (lado esquerdo) mantém comportamento de copiar
menu.PegarKeyButton.MouseButton1Click:Connect(function()
    copyDiscordLink()
end)

-- BOTÃO Copiar (na área do key)
menu.CopyButton.MouseButton1Click:Connect(function()
    copyDiscordLink()
end)

-- Inicia com campo de key visível
showKeyFrame()
