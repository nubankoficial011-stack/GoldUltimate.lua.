
-- GOLD ULTIMATE V1.0
-- Interface mobile para Roblox

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

local gold = Color3.fromRGB(218, 174, 65)
local dark = Color3.fromRGB(15, 15, 18)
local panelColor = Color3.fromRGB(25, 25, 29)
local white = Color3.fromRGB(245, 245, 245)

local gui = Instance.new("ScreenGui")
gui.Name = "GoldUltimate"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.fromOffset(340, 390)
main.Position = UDim2.new(0.5, -170, 0.5, -195)
main.BackgroundColor3 = dark
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = main

local stroke = Instance.new("UIStroke")
stroke.Color = gold
stroke.Thickness = 2
stroke.Parent = main

local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 48)
header.BackgroundColor3 = panelColor
header.BorderSizePixel = 0
header.Parent = main

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -85, 1, 0)
title.Position = UDim2.fromOffset(12, 0)
title.BackgroundTransparency = 1
title.Text = "GOLD ULTIMATE"
title.TextColor3 = gold
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

local minimize = Instance.new("TextButton")
minimize.Size = UDim2.fromOffset(32, 32)
minimize.Position = UDim2.new(1, -72, 0, 8)
minimize.Text = "-"
minimize.TextSize = 22
minimize.BackgroundColor3 = dark
minimize.TextColor3 = gold
minimize.Parent = header

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(32, 32)
close.Position = UDim2.new(1, -36, 0, 8)
close.Text = "X"
close.TextColor3 = gold
close.BackgroundColor3 = dark
close.Parent = header

local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, 105, 1, -55)
sidebar.Position = UDim2.fromOffset(5, 52)
sidebar.BackgroundColor3 = panelColor
sidebar.Parent = main

local sideLayout = Instance.new("UIListLayout")
sideLayout.Padding = UDim.new(0, 5)
sideLayout.Parent = sidebar

local content = Instance.new("Frame")
content.Size = UDim2.new(1, -120, 1, -60)
content.Position = UDim2.fromOffset(115, 55)
content.BackgroundColor3 = panelColor
content.Parent = main

local contentCorner = Instance.new("UICorner")
contentCorner.CornerRadius = UDim.new(0, 8)
contentCorner.Parent = content

local tabs = {
    "Home",
    "Players",
    "ESP",
    "Teleport",
    "Movement",
    "Sound",
    "Settings",
    "About"
}

local pages = {}

local function createPage(name)
    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.fromScale(1, 1)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 3
    page.Visible = false
    page.Parent = content

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 8)
    layout.Parent = page

    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        page.CanvasSize = UDim2.fromOffset(0, layout.AbsoluteContentSize.Y + 15)
    end)

    pages[name] = page
    return page
end

local function addLabel(page, text)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -12, 0, 35)
    label.BackgroundColor3 = dark
    label.TextColor3 = white
    label.Text = text
    label.TextSize = 13
    label.Font = Enum.Font.Gotham
    label.Parent = page
    return label
end

local function addButton(page, text, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -12, 0, 38)
    button.BackgroundColor3 = dark
    button.TextColor3 = gold
    button.Text = text
    button.TextSize = 13
    button.Font = Enum.Font.GothamBold
    button.Parent = page

    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)

    button.Activated:Connect(callback)
    return button
end

local function addSlider(page, text, min, max, initial, callback)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, -12, 0, 65)
    container.BackgroundColor3 = dark
    container.Parent = page

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -10, 0, 25)
    label.Position = UDim2.fromOffset(5, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = white
    label.Text = text .. ": " .. initial
    label.TextSize = 12
    label.Parent = container

    local slider = Instance.new("TextButton")
    slider.Size = UDim2.new(1, -20, 0, 12)
    slider.Position = UDim2.fromOffset(10, 38)
    slider.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    slider.Text = ""
    slider.Parent = container

    local fill = Instance.new("Frame")
    fill.BackgroundColor3 = gold
    fill.Size = UDim2.new((initial-min)/(max-min), 0, 1, 0)
    fill.Parent = slider

    local dragging = false

    local function update(x)
        local percent = math.clamp(
            (x - slider.AbsolutePosition.X) / slider.AbsoluteSize.X,
            0, 1
        )
        local value = math.floor(min + (max-min)*percent)
        fill.Size = UDim2.fromScale(percent, 1)
        label.Text = text .. ": " .. value
        callback(value)
    end

    slider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            update(input.Position.X)
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if dragging and (
            input.UserInputType == Enum.UserInputType.MouseMovement
            or input.UserInputType == Enum.UserInputType.Touch
        ) then
            update(input.Position.X)
        end
    end)

    game:GetService("UserInputService").InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

for _, name in ipairs(tabs) do
    local page = createPage(name)

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -6, 0, 38)
    button.BackgroundColor3 = dark
    button.TextColor3 = gold
    button.Text = name
    button.TextSize = 12
    button.Font = Enum.Font.GothamBold
    button.Parent = sidebar

    button.Activated:Connect(function()
        for _, p in pairs(pages) do
            p.Visible = false
        end
        page.Visible = true
    end)
end

addLabel(pages.Home, "Bem-vindo ao GOLD ULTIMATE!")
addLabel(pages.Home, "Versao 1.0 | Zombie War")

addLabel(pages.Players, "Jogadores nesta sessão:")
for _, p in ipairs(Players:GetPlayers()) do
    addLabel(pages.Players, p.Name)
end

Players.PlayerAdded:Connect(function(p)
    addLabel(pages.Players, p.Name)
end)

addLabel(pages.ESP, "Área de configuração ESP")
addLabel(pages.ESP, "ESP requer integração autorizada.")

addLabel(pages.Teleport, "Área de teleporte")
addLabel(pages.Teleport, "TP requer suporte do servidor.")

addLabel(pages.Movement, "Movimentação")

addSlider(pages.Movement, "Speed", 0, 500, 16, function(value)
    -- Valor visual; aplicação requer suporte do jogo.
end)

addSlider(pages.Movement, "Fly", 0, 100, 0, function(value)
    -- Controle reservado para integração autorizada.
end)

addSlider(pages.Movement, "Spin", 0, 100, 0, function(value)
    -- Controle reservado para integração autorizada.
end)

addLabel(pages.Sound, "Configurações de som")
addLabel(pages.Settings, "Tamanho do painel")

addSlider(pages.Settings, "Tamanho", 0, 100, 100, function(value)
    main.Size = UDim2.fromOffset(
        340 * value / 100,
        390 * value / 100
    )
    main.Visible = value > 0
end)

addLabel(pages.About, "GOLD ULTIMATE V1.0")
addLabel(pages.About, "Criado para testes autorizados.")

pages.Home.Visible = true

local minimized = false

minimize.Activated:Connect(function()
    minimized = not minimized
    sidebar.Visible = not minimized
    content.Visible = not minimized
    main.Size = minimized
        and UDim2.fromOffset(220, 48)
        or UDim2.fromOffset(340, 390)
end)

close.Activated:Connect(function()
    gui:Destroy()
end)
