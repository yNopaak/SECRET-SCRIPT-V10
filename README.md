-- SECRET BYPASS - Premium UI + ESP avançado + Auto-Return on Brainrot (ajustado)
-- Tema: Dark extremo (preto), fontes refinadas, Fly e Auto-Return corrigidos.
-- Warning: movement uses BodyVelocity/AssemblyLinearVelocity; may be detectable by server anti-cheat.

local Players        = game:GetService("Players")
local RunService     = game:GetService("RunService")
local UIS            = game:GetService("UserInputService")
local TweenService   = game:GetService("TweenService")
local Workspace      = game:GetService("Workspace")
local LocalPlayer    = Players.LocalPlayer

-- === Configs iniciais ===
local DEFAULT_FLY_SPEED   = 45
local DEFAULT_WALK_SPEED  = 45
local BRAINROT_TOOL_NAME  = "Brainrot"
local BASE_NAME_SUBSTRING = "base" -- procura objetos que tenham "base" no nome (case-insensitive)
local AUTOFLY_DEFAULT     = 200

-- === GUI root ===
local gui = Instance.new("ScreenGui")
gui.Name = "SecretBypass_Premium"
gui.ResetOnSpawn = false
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Paleta (tema totalmente dark / preto extremo)
local COLOR_BG    = Color3.fromRGB(6,6,6)
local COLOR_PANEL = Color3.fromRGB(12,12,12)
local COLOR_ACC   = Color3.fromRGB(185,185,185) -- acento neutro
local COLOR_TEXT  = Color3.fromRGB(240,240,240)
local COLOR_MUTED = Color3.fromRGB(120,120,120)

local FONT = Enum.Font.GothamBold -- fonte negrita, elegante para a UI
local CREDIT_FONT = Enum.Font.SourceSansSemibold -- fonte clean para créditos / notas

local function round(inst, r) local c = Instance.new("UICorner", inst); c.CornerRadius = UDim.new(0, r) return c end
local function uiStroke(inst, thickness, color, trans)
    local s = Instance.new("UIStroke", inst)
    s.Thickness = thickness or 1
    s.Color = color or Color3.fromRGB(20,20,20)
    s.Transparency = trans or 0
    return s
end

-- helper tween
local function tween(inst, props, info) TweenService:Create(inst, info or TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), props):Play() end

-- === Window (menor e mais compacto) ===
local window = Instance.new("Frame", gui)
window.Size = UDim2.new(0, 560, 0, 320) -- menor que antes
window.Position = UDim2.new(0.5, -280, 0.5, -160)
window.BackgroundColor3 = COLOR_BG
round(window, 12)
uiStroke(window, 1, Color3.fromRGB(18,18,18))

-- opening animation (scale)
window.AnchorPoint = Vector2.new(0.5,0.5)
window.Position = UDim2.new(0.5, 0, 0.5, 0)
window.Size = UDim2.new(0, 0, 0, 0)
tween(window, {Size = UDim2.new(0, 560, 0, 320)}, TweenInfo.new(0.38, Enum.EasingStyle.Back, Enum.EasingDirection.Out))

-- subtle header area
local bgTop = Instance.new("Frame", window)
bgTop.Size = UDim2.new(1,0,0,92)
bgTop.Position = UDim2.new(0,0,0,0)
bgTop.BackgroundColor3 = Color3.fromRGB(8,8,8)
bgTop.BackgroundTransparency = 0
round(bgTop, 12)

-- === Title Bar ===
local titleBar = Instance.new("Frame", window)
titleBar.Size = UDim2.new(1, -20, 0, 48)
titleBar.Position = UDim2.new(0, 10, 0, 12)
titleBar.BackgroundTransparency = 1

local titleLabel = Instance.new("TextLabel", titleBar)
titleLabel.Size = UDim2.new(0.7, 0, 1, 0)
titleLabel.Position = UDim2.new(0, 6, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Font = FONT
titleLabel.Text = "SECRET BYPASS"
titleLabel.TextColor3 = COLOR_TEXT
titleLabel.TextScaled = true
titleLabel.TextXAlignment = Enum.TextXAlignment.Left

local subtitle = Instance.new("TextLabel", titleBar)
subtitle.Size = UDim2.new(0.3, -6, 1, 0)
subtitle.Position = UDim2.new(0.7, 0, 0, 0)
subtitle.BackgroundTransparency = 1
subtitle.Font = FONT
subtitle.Text = "Premium Panel"
subtitle.TextColor3 = COLOR_MUTED
subtitle.TextScaled = true
subtitle.TextXAlignment = Enum.TextXAlignment.Right

local closeBtn = Instance.new("TextButton", titleBar)
closeBtn.Size = UDim2.new(0, 34, 0, 26)
closeBtn.Position = UDim2.new(1, -40, 0.5, -13)
closeBtn.Text = "X"
closeBtn.Font = FONT
closeBtn.TextSize = 18
closeBtn.TextColor3 = COLOR_TEXT
closeBtn.BackgroundColor3 = Color3.fromRGB(38,38,38)
round(closeBtn, 8)
closeBtn.MouseEnter:Connect(function() tween(closeBtn, {BackgroundTransparency = 0.2}, TweenInfo.new(0.12)) end)
closeBtn.MouseLeave:Connect(function() tween(closeBtn, {BackgroundTransparency = 0}, TweenInfo.new(0.12)) end)
closeBtn.MouseButton1Click:Connect(function()
    tween(window, {Size = UDim2.new(0,0,0,0)}, TweenInfo.new(0.26, Enum.EasingStyle.Quad))
    task.wait(0.26)
    gui:Destroy()
end)

-- Dragging the window (by titleBar)
do
    local dragging, startPos, startMouse
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            startMouse = input.Position
            startPos = window.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    UIS.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - startMouse
            window.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- === Left nav (tabs) ===
local side = Instance.new("ScrollingFrame", window)
side.ScrollBarThickness = 4
side.ScrollBarImageColor3 = Color3.fromRGB(80,80,80)
side.CanvasSize = UDim2.new(0,0,0,0)
side.AutomaticCanvasSize = Enum.AutomaticSize.Y
side.Size = UDim2.new(0, 128, 1, -120)
side.Position = UDim2.new(0, 10, 0, 100)
side.BackgroundColor3 = COLOR_PANEL
round(side, 10)
uiStroke(side, 1, Color3.fromRGB(16,16,16))

local sideLayout = Instance.new("UIListLayout", side)
sideLayout.SortOrder = Enum.SortOrder.LayoutOrder
sideLayout.Padding = UDim.new(0, 8)

local function makeNavButton(parent, text, y)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(1, -18, 0, 38)
    btn.Position = UDim2.new(0, 9, 0, y)
    btn.BackgroundColor3 = COLOR_BG
    btn.Text = text
    btn.Font = FONT
    btn.TextColor3 = COLOR_TEXT
    btn.TextSize = 14
    round(btn, 8)
    btn.AutoButtonColor = false
    btn.MouseEnter:Connect(function()
        local hoverColor = (COLOR_ACC or Color3.fromRGB(40,40,40))
        tween(btn, {BackgroundColor3 = hoverColor}, TweenInfo.new(0.15))
    end)
    btn.MouseLeave:Connect(function() tween(btn, {BackgroundColor3 = COLOR_BG}, TweenInfo.new(0.15)) end)
    return btn
end

-- page area
local pageArea = Instance.new("Frame", window)
pageArea.ClipsDescendants = true -- impede elementos de sair da área
pageArea.Size = UDim2.new(1, -156, 1, -120)
pageArea.Position = UDim2.new(0, 146, 0, 100)
pageArea.BackgroundColor3 = COLOR_PANEL
round(pageArea, 10)
uiStroke(pageArea, 1, Color3.fromRGB(16,16,16))

local pages = {}
local navButtons = {}

local function createPage(name)
    local p = Instance.new("Frame", pageArea)
    p.Size = UDim2.new(1, -18, 1, -18)
    p.Position = UDim2.new(0, 9, 0, 9)
    p.BackgroundTransparency = 1
    p.Visible = false
    local layout = Instance.new("UIListLayout", p)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 10)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    pages[name] = p
    return p
end

local function showPage(name)
    for n, p in pairs(pages) do p.Visible = (n == name) end
    for n, b in pairs(navButtons) do
        b.BackgroundColor3 = (n == name) and Color3.fromRGB(18,18,18) or COLOR_BG
        b.TextColor3 = (n == name) and Color3.new(1,1,1) or COLOR_TEXT
    end
end

-- Create nav buttons and pages
local btnMov = makeNavButton(side, "Movimento", 8); navButtons["Movimento"] = btnMov
local btnVis = makeNavButton(side, "Visual", 56); navButtons["Visual"] = btnVis
local btnExtra = makeNavButton(side, "Extras", 104); navButtons["Extras"] = btnExtra
local btnCred = makeNavButton(side, "Créditos", 152); navButtons["Créditos"] = btnCred
local btnBrainrot = makeNavButton(side, "Brainrot", 200); navButtons["Brainrot"] = btnBrainrot

local pagMov = createPage("Movimento")
local pagVis = createPage("Visual")
local pagExtra = createPage("Extras")
local pagCred = createPage("Créditos")
local pagBrainrot = createPage("Brainrot")

btnMov.MouseButton1Click:Connect(function() showPage("Movimento") end)
btnVis.MouseButton1Click:Connect(function() showPage("Visual") end)
btnExtra.MouseButton1Click:Connect(function() showPage("Extras") end)
btnCred.MouseButton1Click:Connect(function() showPage("Créditos") end)
btnBrainrot.MouseButton1Click:Connect(function() showPage("Brainrot") end)

-- default
showPage("Movimento")

-- === UI widgets: cards, toggles, sliders, keybind capture, notifications ===
local function makeCard(parent, height)
    local f = Instance.new("Frame", parent)
    f.Size = UDim2.new(1, -16, 0, height or 42) -- largura fluida e altura padronizada menor
    f.BackgroundColor3 = COLOR_BG
    round(f, 10)
    uiStroke(f, 1, Color3.fromRGB(18,18,18))
    return f
end

local function makeToggle(parent, label, callback)
    local c = makeCard(parent, 38) -- altura padronizada menor
    local txt = Instance.new("TextLabel", c)
    txt.Size = UDim2.new(0.68, -8, 1, 0)
    txt.Position = UDim2.new(0, 12, 0, 0)
    txt.BackgroundTransparency = 1
    txt.Font = FONT
    txt.Text = label
    txt.TextColor3 = COLOR_TEXT
    txt.TextScaled = true
    txt.TextWrapped = true -- impede texto de vazar
    txt.TextXAlignment = Enum.TextXAlignment.Left

    local btn = Instance.new("TextButton", c)
    btn.Size = UDim2.new(0, 92, 0, 30)
    btn.Position = UDim2.new(1, -106, 0.5, -15)
    btn.BackgroundColor3 = Color3.fromRGB(28,28,28)
    btn.Text = "OFF"
    btn.Font = FONT
    btn.TextColor3 = COLOR_TEXT
    round(btn, 8)
    local state = false

    btn.MouseEnter:Connect(function() tween(btn, {BackgroundColor3 = Color3.fromRGB(36,36,36)}, TweenInfo.new(0.12)) end)
    btn.MouseLeave:Connect(function() tween(btn, {BackgroundColor3 = Color3.fromRGB(28,28,28)}, TweenInfo.new(0.12)) end)

    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = state and "ON" or "OFF"
        btn.BackgroundColor3 = state and COLOR_ACC or Color3.fromRGB(28,28,28)
        pcall(callback, state)
    end)

    return c, function(s)
        state = s
        btn.Text = state and "ON" or "OFF"
        btn.BackgroundColor3 = state and COLOR_ACC or Color3.fromRGB(28,28,28)
    end
end

local function makeSlider(parent, label, min, max, default, callback)
    local c = makeCard(parent, 42) -- altura padronizada menor
    local txt = Instance.new("TextLabel", c)
    txt.Size = UDim2.new(1, -24, 0, 18) -- altura texto menor
    txt.Position = UDim2.new(0, 12, 0, 4) -- texto mais para cima
    txt.BackgroundTransparency = 1
    txt.Font = FONT
    txt.Text = string.format("%s: %d", label, default)
    txt.TextColor3 = COLOR_TEXT
    txt.TextXAlignment = Enum.TextXAlignment.Left
    txt.TextScaled = true
    txt.TextWrapped = true -- impede texto de vazar

    local bar = Instance.new("Frame", c)
    bar.Size = UDim2.new(1, -24, 0, 6) -- barra ainda mais fina
    bar.Position = UDim2.new(0, 12, 0, 26) -- barra mais alta dentro do card
    bar.BackgroundColor3 = Color3.fromRGB(32,32,32)
    round(bar, 6)

    local fill = Instance.new("Frame", bar)
    fill.Size = UDim2.new((default-min)/(max-min), 0, 1, 0)
    fill.BackgroundColor3 = COLOR_ACC
    round(fill, 6)

    local dragging = false
    bar.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true end end)
    bar.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
    UIS.InputChanged:Connect(function(i)
        if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
            local rel = math.clamp((i.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            fill.Size = UDim2.new(rel, 0, 1, 0)
            local val = math.floor(min + (max - min) * rel)
            txt.Text = string.format("%s: %d", label, val)
            pcall(callback, val)
        end
    end)

    return c, function(v)
        local rel = math.clamp((v - min) / (max - min), 0, 1)
        fill.Size = UDim2.new(rel, 0, 1, 0)
        txt.Text = string.format("%s: %d", label, v)
    end
end

local function makeKeybind(parent, label, callback)
    local c = makeCard(parent, 48)
    local txt = Instance.new("TextLabel", c)
    txt.Size = UDim2.new(0.56, -8, 1, 0)
    txt.Position = UDim2.new(0, 12, 0, 0)
    txt.BackgroundTransparency = 1
    txt.Font = FONT
    txt.Text = label
    txt.TextColor3 = COLOR_TEXT
    txt.TextScaled = true
    txt.TextWrapped = true -- impede texto de vazar
    txt.TextXAlignment = Enum.TextXAlignment.Left

    local btn = Instance.new("TextButton", c)
    btn.Size = UDim2.new(0, 120, 0, 30)
    btn.Position = UDim2.new(1, -136, 0.5, -15)
    btn.BackgroundColor3 = Color3.fromRGB(28,28,28)
    btn.Text = "Set Key"
    btn.Font = FONT
    btn.TextColor3 = COLOR_TEXT
    round(btn, 8)

    local bound = nil
    btn.MouseButton1Click:Connect(function()
        btn.Text = "Press a key..."
        local conn
        conn = UIS.InputBegan:Connect(function(input, gpe)
            if input.UserInputType == Enum.UserInputType.Keyboard then
                bound = input.KeyCode
                btn.Text = tostring(bound):gsub("Enum.KeyCode.", "")
                pcall(callback, bound)
                conn:Disconnect()
            end
        end)
    end)

    return c, function(k)
        bound = k
        btn.Text = (k and tostring(k):gsub("Enum.KeyCode.","")) or "Set Key"
    end
end

-- Notifications (toast)
local notifFolder = Instance.new("Frame", gui)
notifFolder.Size = UDim2.new(0, 260, 0, 220)
notifFolder.Position = UDim2.new(1, -280, 0.02, 0)
notifFolder.BackgroundTransparency = 1

local function notify(text, duration)
    duration = duration or 2.8
    local card = Instance.new("Frame", notifFolder)
    card.Size = UDim2.new(0, 240, 0, 44)
    card.Position = UDim2.new(0, 0, 0, 0)
    card.AnchorPoint = Vector2.new(1,0)
    card.BackgroundColor3 = Color3.fromRGB(22,22,22)
    round(card, 8)
    uiStroke(card, 1, Color3.fromRGB(12,12,12))
    local lbl = Instance.new("TextLabel", card)
    lbl.Size = UDim2.new(1, -12, 1, 0)
    lbl.Position = UDim2.new(0, 8, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Font = FONT
    lbl.Text = text
    lbl.TextColor3 = COLOR_TEXT
    lbl.TextScaled = true
    lbl.TextXAlignment = Enum.TextXAlignment.Left

    card.Position = UDim2.new(1, 280, 0, 0)
    tween(card, {Position = UDim2.new(1, -10, 0, 0)}, TweenInfo.new(0.28, Enum.EasingStyle.Quad))
    task.delay(duration, function()
        tween(card, {Position = UDim2.new(1, 280, 0, 0)}, TweenInfo.new(0.28, Enum.EasingStyle.Quad))
        task.wait(0.32)
        pcall(function() card:Destroy() end)
    end)
end

-- === Movement & ESP core logic ===
-- Movement variables
local FlyOn, SpeedOn = false, false
local FlyConn, SpeedConn = nil, nil
local FlySpeed = DEFAULT_FLY_SPEED
local SpeedValue = DEFAULT_WALK_SPEED
local AutoFlySpeed = AUTOFLY_DEFAULT

-- Fly loop (corrigido: usa AssemblyLinearVelocity em XZ e mantém Y separadamente, evita "voltar")
local function startFly()
    if FlyConn then FlyConn:Disconnect() end
    FlyConn = RunService.Heartbeat:Connect(function()
        if not FlyOn then return end
        local char = LocalPlayer.Character
        if not char then return end
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local cam = Workspace.CurrentCamera
        if not cam then return end

        -- horizontal directions projected on XZ plane (remove componente Y)
        local forward = Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z)
        local right   = Vector3.new(cam.CFrame.RightVector.X, 0, cam.CFrame.RightVector.Z)
        local move = Vector3.new(0,0,0)

        if UIS:IsKeyDown(Enum.KeyCode.W) then move += forward end
        if UIS:IsKeyDown(Enum.KeyCode.S) then move -= forward end
        if UIS:IsKeyDown(Enum.KeyCode.A) then move -= right end
        if UIS:IsKeyDown(Enum.KeyCode.D) then move += right end
        -- vertical
        local vy = 0
        if UIS:IsKeyDown(Enum.KeyCode.Space) then vy = vy + FlySpeed end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) or UIS:IsKeyDown(Enum.KeyCode.LeftShift) then vy = vy - FlySpeed end

        if move.Magnitude > 0 then
            local desired = move.Unit * FlySpeed
            -- set AssemblyLinearVelocity with XZ from desired and Y from vy (or keep current Y if none)
            local currentY = hrp.AssemblyLinearVelocity.Y
            local newY = (vy ~= 0) and vy or currentY
            hrp.AssemblyLinearVelocity = Vector3.new(desired.X, newY, desired.Z)
        else
            -- no horizontal input: keep Y velocity (allow falling/vertical control), reduce horizontal smoothly
            local cur = hrp.AssemblyLinearVelocity
            hrp.AssemblyLinearVelocity = Vector3.new(0, cur.Y, 0)
            if vy ~= 0 then
                local cur2 = hrp.AssemblyLinearVelocity
                hrp.AssemblyLinearVelocity = Vector3.new(cur2.X, vy, cur2.Z)
            end
        end
    end)
end

-- Speed loop (keep as before but tightened)
local function startSpeed()
    if SpeedConn then SpeedConn:Disconnect() end
    SpeedConn = RunService.Heartbeat:Connect(function()
        if not SpeedOn then return end
        local char = LocalPlayer.Character
        if not char then return end
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        if not Workspace.CurrentCamera then return end
        local camCF = Workspace.CurrentCamera.CFrame
        local forward = Vector3.new(camCF.LookVector.X, 0, camCF.LookVector.Z)
        local right = Vector3.new(camCF.RightVector.X, 0, camCF.RightVector.Z)
        local mv = Vector3.new()
        if UIS:IsKeyDown(Enum.KeyCode.W) then mv += forward end
        if UIS:IsKeyDown(Enum.KeyCode.S) then mv -= forward end
        if UIS:IsKeyDown(Enum.KeyCode.D) then mv += right end
        if UIS:IsKeyDown(Enum.KeyCode.A) then mv -= right end
        if mv.Magnitude > 0 then
            local v = mv.Unit * SpeedValue
            hrp.AssemblyLinearVelocity = Vector3.new(v.X, hrp.AssemblyLinearVelocity.Y, v.Z)
        else
            hrp.AssemblyLinearVelocity = Vector3.new(0, hrp.AssemblyLinearVelocity.Y, 0)
        end
    end)
end

-- === ESP system ===
local beams = {} -- map player -> beam instance
local function clearESPFor(plr)
    if not plr or not plr.Character then return end
    -- highlight
    local h = plr.Character:FindFirstChild("SB_Highlight")
    if h then pcall(function() h:Destroy() end) end
    -- Box
    local root = plr.Character:FindFirstChild("HumanoidRootPart")
    if root then
        local bb = root:FindFirstChild("SB_Box")
        if bb then pcall(function() bb:Destroy() end) end
        local attach = root:FindFirstChild("SB_Attach")
        if attach then pcall(function() attach:Destroy() end) end
    end
    -- Name
    local head = plr.Character:FindFirstChild("Head")
    if head then
        local n = head:FindFirstChild("SB_Name")
        if n then pcall(function() n:Destroy() end) end
    end
    -- Beam
    if beams[plr] then pcall(function() beams[plr]:Destroy() end) beams[plr] = nil end
end

local function applyHighlight(plr, on)
    if not plr.Character then return end
    if on then
        if not plr.Character:FindFirstChild("SB_Highlight") then
            local h = Instance.new("Highlight")
            h.Name = "SB_Highlight"
            h.FillColor = Color3.fromRGB(220, 70, 70)
            h.OutlineColor = Color3.fromRGB(255,255,255)
            h.Parent = plr.Character
        end
    else
        if plr.Character:FindFirstChild("SB_Highlight") then pcall(function() plr.Character.SB_Highlight:Destroy() end) end
    end
end

local function applyBox(plr, on)
    if not plr.Character then return end
    local root = plr.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    if on then
        if root:FindFirstChild("SB_Box") then return end
        local bb = Instance.new("BillboardGui")
        bb.Name = "SB_Box"
        bb.Adornee = root
        bb.AlwaysOnTop = true
        bb.Size = UDim2.new(0, 110, 0, 160)
        bb.StudsOffset = Vector3.new(0, 1.2, 0)
        bb.Parent = root
        local container = Instance.new("Frame", bb)
        container.Size = UDim2.new(1,0,1,0)
        container.BackgroundTransparency = 1
        local top = Instance.new("Frame", container)
        top.Size = UDim2.new(1,0,0,2); top.Position = UDim2.new(0,0,0,0); top.BackgroundColor3 = Color3.fromRGB(0,200,120); top.BorderSizePixel = 0
        local bottom = Instance.new("Frame", container)
        bottom.Size = UDim2.new(1,0,0,2); bottom.Position = UDim2.new(0,0,1,-2); bottom.BackgroundColor3 = Color3.fromRGB(0,200,120); bottom.BorderSizePixel = 0
        local left = Instance.new("Frame", container)
        left.Size = UDim2.new(0,2,1,0); left.Position = UDim2.new(0,0,0,0); left.BackgroundColor3 = Color3.fromRGB(0,200,120); left.BorderSizePixel = 0
        local right = Instance.new("Frame", container)
        right.Size = UDim2.new(0,2,1,0); right.Position = UDim2.new(1,-2,0,0); right.BackgroundColor3 = Color3.fromRGB(0,200,120); right.BorderSizePixel = 0
    else
        local bb = root:FindFirstChild("SB_Box")
        if bb then pcall(function() bb:Destroy() end) end
    end
end

local function applyName(plr, on)
    if not plr.Character then return end
    local head = plr.Character:FindFirstChild("Head")
    if not head then return end
    if on then
        if not head:FindFirstChild("SB_Name") then
            local gui = Instance.new("BillboardGui", head)
            gui.Name = "SB_Name"
            gui.Size = UDim2.new(0,120,0,22)
            gui.StudsOffset = Vector3.new(0,2.6,0)
            gui.AlwaysOnTop = true
            local txt = Instance.new("TextLabel", gui)
            txt.Size = UDim2.new(1,0,1,0)
            txt.BackgroundTransparency = 1
            txt.Text = plr.Name
            txt.Font = Enum.Font.GothamSemibold
            txt.TextColor3 = Color3.fromRGB(240,240,240)
            txt.TextScaled = false
            txt.TextSize = 14 -- fonte reduzida para não poluir
            txt.TextStrokeTransparency = 0.8
        end
    else
        local n = head:FindFirstChild("SB_Name")
        if n then pcall(function() n:Destroy() end) end
    end
end

-- Beam (smooth line)
local function applyLine(plr, on)
    if not plr.Character then return end
    local localHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local targetHRP = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
    if not localHRP or not targetHRP then return end
    if on then
        if not targetHRP:FindFirstChild("SB_Attach") then
            local a = Instance.new("Attachment", targetHRP); a.Name = "SB_Attach"
        end
        if not localHRP:FindFirstChild("SB_LocalAttach") then
            local la = Instance.new("Attachment", localHRP); la.Name = "SB_LocalAttach"
        end
        if not beams[plr] then
            local beam = Instance.new("Beam")
            beam.Name = "SB_Beam_"..plr.UserId
            beam.Attachment0 = localHRP:FindFirstChild("SB_LocalAttach")
            beam.Attachment1 = targetHRP:FindFirstChild("SB_Attach")
            beam.Width0 = 0.06; beam.Width1 = 0.06
            beam.Color = ColorSequence.new(Color3.fromRGB(0,200,140))
            beam.Transparency = NumberSequence.new(0.12)
            beam.FaceCamera = false
            beam.Parent = localHRP
            beams[plr] = beam
        end
    else
        if targetHRP:FindFirstChild("SB_Attach") then pcall(function() targetHRP.SB_Attach:Destroy() end) end
        if beams[plr] then pcall(function() beams[plr]:Destroy() end) beams[plr] = nil end
    end
end

local function updateESPAll(modes)
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            if plr.Character then
                applyHighlight(plr, modes.highlight)
                applyBox(plr, modes.box)
                applyName(plr, modes.name)
                applyLine(plr, modes.line)
            end
        end
    end
end

-- reapply on player spawn
Players.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function() task.wait(0.15); updateESPAll(currentESP) end)
end)

-- === Auto-move to base on Brainrot ===
local autoReturnEnabled = false
local autoConn = nil
local autoDebounce = false

-- find nearest base part whose name contains BASE_NAME_SUBSTRING (case-insensitive)
local function findNearestBase()
    local best, bestDist = nil, math.huge
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local myPos = hrp and hrp.Position
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and type(obj.Name) == "string" then
            if string.find(string.lower(obj.Name), BASE_NAME_SUBSTRING:lower()) then
                if myPos then
                    local d = (obj.Position - myPos).Magnitude
                    if d < bestDist then best = obj; bestDist = d end
                else
                    return obj -- fallback
                end
            end
        end
    end
    return best
end

local function smoothFlyTo(pos, speed)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return false end
    local hrp = char.HumanoidRootPart
    -- BodyVelocity-based movement with platformstand during travel (kept but made robust)
    if char:FindFirstChild("Humanoid") then pcall(function() char.Humanoid.PlatformStand = true end) end
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e7,1e7,1e7)
    bv.P = 4000
    bv.Parent = hrp
    local startTime = tick()
    local success = false
    while (pos - hrp.Position).Magnitude > 4 do
        if not autoReturnEnabled then break end
        if not (LocalPlayer and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")) then break end
        -- adjust if target moved slightly
        local dir = (pos - hrp.Position)
        if dir.Magnitude == 0 then break end
        bv.Velocity = dir.Unit * math.clamp(speed, 40, 1200)
        RunService.Heartbeat:Wait()
        -- safety timeout to avoid infinite loops
        if tick() - startTime > 12 then -- 12s timeout per attempt
            break
        end
    end
    pcall(function() bv:Destroy() end)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        pcall(function() LocalPlayer.Character.Humanoid.PlatformStand = false end)
    end
    if (pos - (hrp and hrp.Position or pos)).Magnitude <= 6 then success = true end
    return success
end

local function monitorBrainrot()
    if autoConn then return end
    autoConn = RunService.Heartbeat:Connect(function()
        if not autoReturnEnabled then return end
        if autoDebounce then return end
        local hasTool = false
        local char = LocalPlayer.Character
        if char then
            -- check equipped tools (case-insensitive)
            for _, c in pairs(char:GetChildren()) do
                if c:IsA("Tool") and string.lower(c.Name) == string.lower(BRAINROT_TOOL_NAME) then
                    hasTool = true; break
                end
            end
            -- check backpack
            if not hasTool and LocalPlayer.Backpack and LocalPlayer.Backpack:FindFirstChild(BRAINROT_TOOL_NAME) then hasTool = true end
        end
        if hasTool then
            local basePart = findNearestBase()
            if basePart then
                local target = basePart.Position + Vector3.new(0, 6, 0)
                notify("Brainrot detectado — indo para a base...", 2.2)
                autoDebounce = true
                -- run movement in coroutine to avoid blocking Heartbeat loop
                coroutine.wrap(function()
                    local ok = smoothFlyTo(target, math.clamp(AutoFlySpeed or AUTOFLY_DEFAULT, 60, 1200))
                    task.wait(0.8)
                    autoDebounce = false
                end)()
            end
        end
    end)
end

-- ensure disconnect when disabled (clean)
local function setAutoReturn(state)
    autoReturnEnabled = state
    if state then
        monitorBrainrot()
    else
        if autoConn then
            autoConn:Disconnect()
            autoConn = nil
        end
        autoDebounce = false
    end
end

-- === Build UI controls and wire callbacks ===
-- Movement page content
local flyToggle, setFlyState = makeToggle(pagMov, "Voar", function(state)
    FlyOn = state
    if state then
        -- disable Speed while flying to reduce conflicts
        if SpeedOn and SpeedConn then SpeedConn:Disconnect(); SpeedConn = nil end
        startFly()
    else
        if FlyConn then FlyConn:Disconnect(); FlyConn = nil end
    end
    notify("Voar " .. (state and "ativado" or "desativado"))
end)

local _, setFlySlider = makeSlider(pagMov, "Velocidade Fly", 10, 220, DEFAULT_FLY_SPEED, function(v) FlySpeed = v end)
setFlySlider(DEFAULT_FLY_SPEED)

local speedToggle, setSpeedState = makeToggle(pagMov, "Velocidade Corrida", function(state)
    SpeedOn = state
    if state then
        -- disable Fly while using speed to reduce conflicts
        if FlyOn and FlyConn then FlyConn:Disconnect(); FlyConn = nil; FlyOn = false end
        startSpeed()
    else
        if SpeedConn then SpeedConn:Disconnect(); SpeedConn = nil end
    end
    notify("Velocidade " .. (state and "ativada" or "desativada"))
end)

local _, setSpeedSlider = makeSlider(pagMov, "Velocidade Corrida (valor)", 10, 220, DEFAULT_WALK_SPEED, function(v) SpeedValue = v end)
setSpeedSlider(DEFAULT_WALK_SPEED)

-- Auto-return slider (Extras)
local autoToggle, _ = makeToggle(pagExtra, "Auto-Return on Brainrot", function(state)
    setAutoReturn(state)
    notify("Auto-Return " .. (state and "ativado" or "desativado"))
end)
local _, setAutoFlySlider = makeSlider(pagExtra, "Velocidade de voo Auto", 60, 1200, AUTOFLY_DEFAULT, function(v) AutoFlySpeed = v end)
setAutoFlySlider(AUTOFLY_DEFAULT)

-- Visual page: ESP toggles
local currentESP = {highlight = false, box = false, name = false, line = false}

local function toggleESPMode(modeKey, state)
    currentESP[modeKey] = state
    updateESPAll(currentESP)
    notify(("ESP %s %s"):format(modeKey, state and "ON" or "OFF"), 1.4)
end

makeToggle(pagVis, "ESP Brilho (Highlight)", function(state) toggleESPMode("highlight", state) end)
makeToggle(pagVis, "ESP Caixa (Box)", function(state) toggleESPMode("box", state) end)
makeToggle(pagVis, "ESP Nome (Name)", function(state) toggleESPMode("name", state) end)
makeToggle(pagVis, "ESP Linha (Beam)", function(state) toggleESPMode("line", state) end)


-- Tema manager
local THEMES = {
    Dark = {bg = Color3.fromRGB(6,6,6), panel = Color3.fromRGB(12,12,12), acc = Color3.fromRGB(185,185,185)},
    Purple = {bg = Color3.fromRGB(20,0,40), panel = Color3.fromRGB(40,0,70), acc = Color3.fromRGB(200,120,255)},
    Blue = {bg = Color3.fromRGB(0,20,40), panel = Color3.fromRGB(0,40,80), acc = Color3.fromRGB(100,180,255)}
}

local function applyTheme(name)
    local t = THEMES[name]
    if not t then return end
    COLOR_BG = t.bg
    COLOR_PANEL = t.panel
    COLOR_ACC = t.acc
    notify("Tema aplicado: " .. name, 2.0)
end

-- Tema page

-- Tema selector moderno
local themeCard = makeCard(pagExtra, 48)
local themeLabel = Instance.new("TextLabel", themeCard)
themeLabel.Size = UDim2.new(0.5, -10, 1, 0)
themeLabel.Position = UDim2.new(0, 10, 0, 0)
themeLabel.BackgroundTransparency = 1
themeLabel.Font = FONT
themeLabel.Text = "Tema"
themeLabel.TextColor3 = COLOR_TEXT
themeLabel.TextScaled = true
themeLabel.TextXAlignment = Enum.TextXAlignment.Left

local themeDrop = Instance.new("TextButton", themeCard)
themeDrop.Size = UDim2.new(0.5, -20, 1, -8)
themeDrop.Position = UDim2.new(0.5, 10, 0, 4)
themeDrop.BackgroundColor3 = Color3.fromRGB(28,28,28)
themeDrop.Text = "Selecionar"
themeDrop.Font = FONT
themeDrop.TextColor3 = COLOR_TEXT
round(themeDrop, 8)

local THEMES = {
    Dark = {bg = Color3.fromRGB(6,6,6), panel = Color3.fromRGB(12,12,12), acc = Color3.fromRGB(185,185,185)},
    Purple = {bg = Color3.fromRGB(20,0,40), panel = Color3.fromRGB(40,0,70), acc = Color3.fromRGB(200,120,255)},
    Blue = {bg = Color3.fromRGB(0,20,40), panel = Color3.fromRGB(0,40,80), acc = Color3.fromRGB(100,180,255)},
    Neon = {bg = Color3.fromRGB(0,0,20), panel = Color3.fromRGB(0,0,60), acc = Color3.fromRGB(0,255,180)}
}

local function applyTheme(name)
    local t = THEMES[name]
    if not t then return end
    COLOR_BG = t.bg
    COLOR_PANEL = t.panel
    COLOR_ACC = t.acc
    notify("Tema aplicado: " .. name, 2.0)
end


themeDrop.MouseButton1Click:Connect(function()
    -- Dropdown that appears on click. Clicking again closes it.
    if themeCard:FindFirstChild("SB_ThemeDropdown") then
        themeCard.SB_ThemeDropdown:Destroy()
        return
    end
    local options = {"Dark","Purple","Blue","Neon"}
    local dropdown = Instance.new("Frame", themeCard)
    dropdown.Name = "SB_ThemeDropdown"
    local w = themeDrop.AbsoluteSize.X
    local h = #options * 30 + 8
    dropdown.Size = UDim2.new(0, w, 0, h)
    dropdown.Position = UDim2.new(themeDrop.Position.X.Scale, themeDrop.Position.X.Offset, 1, 6)
    dropdown.BackgroundColor3 = COLOR_PANEL
    dropdown.ClipsDescendants = true
    round(dropdown, 8)
    uiStroke(dropdown, 1, Color3.fromRGB(12,12,12))
    -- create option buttons
    for i, opt in ipairs(options) do
        local b = Instance.new("TextButton", dropdown)
        b.Size = UDim2.new(1, -12, 0, 28)
        b.Position = UDim2.new(0, 6, 0, (i-1)*30 + 6)
        b.BackgroundColor3 = themeDrop.BackgroundColor3
        b.Font = FONT
        b.Text = opt
        b.TextColor3 = COLOR_TEXT
        b.TextScaled = true
        round(b, 6)
        b.MouseEnter:Connect(function() tween(b, {BackgroundColor3 = COLOR_ACC}, TweenInfo.new(0.12)) end)
        b.MouseLeave:Connect(function() tween(b, {BackgroundColor3 = themeDrop.BackgroundColor3}, TweenInfo.new(0.12)) end)
        b.MouseButton1Click:Connect(function()
            -- apply once when clicked
            themeDrop.Text = opt
            -- update colors immediately
            if opt == "Dark" then
                COLOR_BG = Color3.fromRGB(6,6,6); COLOR_PANEL = Color3.fromRGB(12,12,12); COLOR_ACC = Color3.fromRGB(185,185,185)
            elseif opt == "Purple" then
                COLOR_BG = Color3.fromRGB(20,0,40); COLOR_PANEL = Color3.fromRGB(40,0,70); COLOR_ACC = Color3.fromRGB(200,120,255)
            elseif opt == "Blue" then
                COLOR_BG = Color3.fromRGB(0,20,40); COLOR_PANEL = Color3.fromRGB(0,40,80); COLOR_ACC = Color3.fromRGB(100,180,255)
            elseif opt == "Neon" then
                COLOR_BG = Color3.fromRGB(0,0,20); COLOR_PANEL = Color3.fromRGB(0,0,60); COLOR_ACC = Color3.fromRGB(0,255,180)
            end
            -- apply to main UI elements
            pcall(function()
                window.BackgroundColor3 = COLOR_BG
                side.BackgroundColor3 = COLOR_PANEL
                pageArea.BackgroundColor3 = COLOR_PANEL
                bgTop.BackgroundColor3 = COLOR_PANEL
                titleLabel.TextColor3 = COLOR_ACC
                subtitle.TextColor3 = COLOR_ACC
                nameLbl.TextColor3 = COLOR_ACC
                closeBtn.TextColor3 = COLOR_TEXT
                closeBtn.BackgroundColor3 = COLOR_PANEL
                -- update nav buttons
                for _,btn in pairs(navButtons) do
                    btn.BackgroundColor3 = themeDrop.BackgroundColor3
                    btn.TextColor3 = COLOR_TEXT
                end
            end)
            -- store preference in GUI attribute (persist during session)
            pcall(function() gui:SetAttribute("LastTheme", opt) end)
            notify("Tema aplicado: " .. opt, 1.6)
            -- close dropdown
            dropdown:Destroy()
        end)
    end
end)



-- Extra: Transparência
makeToggle(pagExtra, "Painel Transparente", function(state)
    window.BackgroundTransparency = state and 0.3 or 0
end)

-- Extra: Painel Compacto
makeToggle(pagExtra, "Modo Compacto", function(state)
    if state then
        tween(window, {Size = UDim2.new(0, 420, 0, 260)}, TweenInfo.new(0.3))
    else
        tween(window, {Size = UDim2.new(0, 560, 0, 320)}, TweenInfo.new(0.3))
    end
end)

-- Extra: FPS/Ping Monitor
local fpsLabel = Instance.new("TextLabel", gui)
fpsLabel.Size = UDim2.new(0,120,0,20)
fpsLabel.Position = UDim2.new(1,-130,1,-30)
fpsLabel.BackgroundTransparency = 0.5
fpsLabel.BackgroundColor3 = Color3.fromRGB(20,20,20)
fpsLabel.TextColor3 = COLOR_TEXT
fpsLabel.Font = FONT
fpsLabel.TextScaled = true
round(fpsLabel,6)

local last = tick()
RunService.Heartbeat:Connect(function()
    local now = tick()
    local fps = math.floor(1/(now-last))
    last = now
    local ping = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValueString()
    fpsLabel.Text = "FPS: "..fps.." | Ping: "..ping
end)

-- Extra: Salvar configs (simples, em atributos)
local function saveConfig()
    gui:SetAttribute("LastTheme", themeDrop.Text)
    notify("Config salva!", 2.0)
end
local function loadConfig()
    local t = gui:GetAttribute("LastTheme")
    if t then applyTheme(t) end
end
makeToggle(pagExtra, "Salvar Configuração", function(state) if state then saveConfig() end end)
task.delay(1, loadConfig)

makeToggle(pagExtra, "Tema Blue", function(state) if state then applyTheme("Blue") end end)

-- Extras: keybind example (bind Fly toggle to a key)
local function bindToggleToKey(keyEnum, toggleSetter)
    if not keyEnum or not toggleSetter then return end
    UIS.InputBegan:Connect(function(input, gpe)
        if gpe then return end
        if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == keyEnum then
            -- try to flip visual state if toggleSetter supports it
            local ok, currentState = pcall(function() return toggleSetter.__state end)
            local newState = not (currentState or false)
            -- call with new state if function accepts it
            pcall(toggleSetter, newState)
        end
    end)
end

-- Credits page organized (clean font)

-- Brainrot page content (safe features)
makeToggle(pagBrainrot, "ESP Objetos Secret", function(state)
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and string.find(string.lower(obj.Name), "secret") then
            if state then
                if not obj:FindFirstChild("SB_BrainrotESP") then
                    local sel = Instance.new("SelectionBox", obj)
                    sel.Name = "SB_BrainrotESP"
                    sel.Adornee = obj
                    sel.LineThickness = 0.05
                    sel.Color3 = Color3.fromRGB(200, 50, 200)
                end
            else
                local sel = obj:FindFirstChild("SB_BrainrotESP")
                if sel then sel:Destroy() end
            end
        end
    end
end)

makeToggle(pagBrainrot, "Radar de Gold", function(state)
    if state then
        local count = 0
        for _, obj in ipairs(Workspace:GetDescendants()) do
            if obj:IsA("BasePart") and string.find(string.lower(obj.Name), "gold") then
                count += 1
            end
        end
        notify("Radar: encontrados " .. tostring(count) .. " objetos com 'Gold' no nome", 3.0)
    end
end)

makeToggle(pagBrainrot, "Alerta Sonoro Secret", function(state)
    if state then
        notify("Alerta ativo: aparecerá aviso quando objetos 'Secret' surgirem", 3.0)
        Workspace.DescendantAdded:Connect(function(obj)
            if obj:IsA("BasePart") and string.find(string.lower(obj.Name), "secret") then
                notify("Novo objeto 'Secret' detectado!", 3.5)
            end
        end)
    end
end)

local creditsFrame = Instance.new("Frame", pagCred)
creditsFrame.Size = UDim2.new(0, 380, 0, 140)
creditsFrame.Position = UDim2.new(0, 10, 0, 10)
creditsFrame.BackgroundTransparency = 1
local heading = Instance.new("TextLabel", creditsFrame)
heading.Size = UDim2.new(1, 0, 0, 28)
heading.Position = UDim2.new(0,0,0,0)
heading.BackgroundTransparency = 1
heading.Font = FONT
heading.Text = "Créditos"
heading.TextColor3 = COLOR_ACC
heading.TextScaled = true
heading.TextXAlignment = Enum.TextXAlignment.Left

local dev = Instance.new("TextLabel", creditsFrame)
dev.Size = UDim2.new(1,0,0,22)
dev.Position = UDim2.new(0,0,0,36)
dev.BackgroundTransparency = 1
dev.Font = CREDIT_FONT
dev.Text = "Desenvolvedor: ychoxdumal"
dev.TextColor3 = COLOR_TEXT
dev.TextScaled = false
dev.TextSize = 14
dev.TextXAlignment = Enum.TextXAlignment.Left

local proj = Instance.new("TextLabel", creditsFrame)
proj.Size = UDim2.new(1,0,0,20)
proj.Position = UDim2.new(0,0,0,62)
proj.BackgroundTransparency = 1
proj.Font = CREDIT_FONT
proj.Text = "Projeto: Secret Bypass (versão premium)"
proj.TextColor3 = COLOR_TEXT
proj.TextScaled = false
proj.TextSize = 13
proj.TextXAlignment = Enum.TextXAlignment.Left

local discord = Instance.new("TextLabel", creditsFrame)
discord.Size = UDim2.new(1,0,0,20)
discord.Position = UDim2.new(0,0,0,86)
discord.BackgroundTransparency = 1
discord.Font = CREDIT_FONT
discord.Text = "Discord: https://discord.gg/YmMp38bH"
discord.TextColor3 = COLOR_ACC
discord.TextScaled = false
discord.TextSize = 13
discord.TextXAlignment = Enum.TextXAlignment.Left

local note = Instance.new("TextLabel", creditsFrame)
note.Size = UDim2.new(1,0,0,28)
note.Position = UDim2.new(0,0,0,110)
note.BackgroundTransparency = 1
note.Font = CREDIT_FONT
note.Text = "Uso para testes e desenvolvimento. Não abuse em servidores públicos."
note.TextColor3 = COLOR_MUTED
note.TextScaled = false
note.TextSize = 12
note.TextXAlignment = Enum.TextXAlignment.Left

-- initial ESP sync
task.delay(0.2, function() updateESPAll(currentESP) end)

-- quick notify
notify("Secret Bypass carregado — painel premium pronto", 3.0)

-- Clean-up: if GUI removed destroy beams/attachments
gui.AncestryChanged:Connect(function(_,parent)
    if not parent then
        for p, b in pairs(beams) do pcall(function() b:Destroy() end) end
    end
end)

-- Finish: expose some debug commands in extras (optional)
local debugFrame = makeCard(pagExtra, 48)
local dbgLbl = Instance.new("TextLabel", debugFrame)
dbgLbl.Size = UDim2.new(1, -12, 1, 0)
dbgLbl.Position = UDim2.new(0, 8, 0, 0)
dbgLbl.BackgroundTransparency = 1
dbgLbl.Font = CREDIT_FONT
dbgLbl.Text = "Debug: pressionar [F] ativa/desativa Fly (apenas enquanto painel aberto)"
dbgLbl.TextColor3 = COLOR_MUTED
dbgLbl.TextScaled = false
dbgLbl.TextSize = 12
dbgLbl.TextXAlignment = Enum.TextXAlignment.Left

-- bind F to toggle fly for convenience (local only)
UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.UserInputType == Enum.UserInputType.Keyboard then
        if input.KeyCode == Enum.KeyCode.F then
            FlyOn = not FlyOn
            if FlyOn then
                if SpeedOn and SpeedConn then SpeedConn:Disconnect(); SpeedConn=nil; SpeedOn=false end
                startFly()
            else
                if FlyConn then FlyConn:Disconnect(); FlyConn=nil end
            end
            notify("Fly " .. (FlyOn and "ON" or "OFF"), 1.0)
        end
    end
end)

-- End of script.
