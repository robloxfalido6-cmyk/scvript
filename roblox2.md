-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- GUI principal
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.ResetOnSpawn = false

-- Função de drag
local function MakeDraggable(obj)
    local dragging, dragInput, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = obj.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    obj.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 320, 0, 420)
MainFrame.Position = UDim2.new(0.5, -160, 0.5, -210)
MainFrame.BackgroundColor3 = Color3.fromRGB(25,25,25)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MakeDraggable(MainFrame)

-- Botão fechar
local CloseButton = Instance.new("TextButton", MainFrame)
CloseButton.Size = UDim2.new(0, 25, 0, 25)
CloseButton.Position = UDim2.new(1, -30, 0, 5)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255,255,255)
CloseButton.BackgroundColor3 = Color3.fromRGB(200,50,50)
CloseButton.BorderSizePixel = 0
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.TextSize = 18

-- Botão abrir "+"
local OpenButton = Instance.new("TextButton", ScreenGui)
OpenButton.Size = UDim2.new(0, 40, 0, 40)
OpenButton.Position = UDim2.new(0.5, -20, 0.5, -20)
OpenButton.Text = "+"
OpenButton.TextColor3 = Color3.fromRGB(255,255,255)
OpenButton.BackgroundColor3 = Color3.fromRGB(50,200,50)
OpenButton.BorderSizePixel = 0
OpenButton.Font = Enum.Font.SourceSansBold
OpenButton.TextSize = 25
OpenButton.Visible = false
MakeDraggable(OpenButton)

-- Abrir / fechar menu
local function CloseMenu()
    local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0,0,0,0)})
    tween:Play()
    tween.Completed:Connect(function()
        MainFrame.Visible = false
        OpenButton.Visible = true
    end)
end
local function OpenMenu()
    MainFrame.Visible = true
    local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0,320,0,420)})
    tween:Play()
    OpenButton.Visible = false
end
CloseButton.MouseButton1Click:Connect(CloseMenu)
OpenButton.MouseButton1Click:Connect(OpenMenu)

-- Layout Tabs
local TabsFrame = Instance.new("Frame", MainFrame)
TabsFrame.Size = UDim2.new(0, 100, 1, 0)
TabsFrame.Position = UDim2.new(0,0,0,0)
TabsFrame.BackgroundTransparency = 1

local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.Size = UDim2.new(1, -100, 1, 0)
ContentFrame.Position = UDim2.new(0,100,0,0)
ContentFrame.BackgroundTransparency = 1

-- Criar tab
local function CreateTab(name)
    local btn = Instance.new("TextButton", TabsFrame)
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    return btn
end

-- Tabs
local TabRacks = CreateTab("Racks")
TabRacks.Position = UDim2.new(0,0,0,0)
local TabSkin = CreateTab("Copy Skin")
TabSkin.Position = UDim2.new(0,0,0,45)

-- Frames conteúdo
local FrameRacks = Instance.new("Frame", ContentFrame)
FrameRacks.Size = UDim2.new(1,0,1,0)
FrameRacks.BackgroundTransparency = 1

local FrameSkin = Instance.new("Frame", ContentFrame)
FrameSkin.Size = UDim2.new(1,0,1,0)
FrameSkin.BackgroundTransparency = 1
FrameSkin.Visible = false

-- Alternar tabs
TabRacks.MouseButton1Click:Connect(function()
    FrameRacks.Visible = true
    FrameSkin.Visible = false
end)
TabSkin.MouseButton1Click:Connect(function()
    FrameRacks.Visible = false
    FrameSkin.Visible = true
end)

-- =======================
-- RACKS: ESP, Speed, Infinity Jump
-- =======================
local espEnabled, speedEnabled, infinityJumpEnabled = false,false,false
local speedValue = 16

local function CreateToggle(parent, name, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0.8,0,0,35)
    btn.Text = name.." [OFF]"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        if state then
            btn.Text = name.." [ON]"
            btn.BackgroundColor3 = Color3.fromRGB(0,200,0)
            btn.TextColor3 = Color3.fromRGB(0,0,0)
        else
            btn.Text = name.." [OFF]"
            btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
            btn.TextColor3 = Color3.fromRGB(255,255,255)
        end
        callback(state)
    end)
end

-- ESP toggle
CreateToggle(FrameRacks,"ESP Player",function(state)
    espEnabled = state
end)

-- Infinity Jump toggle
CreateToggle(FrameRacks,"Infinity Jump",function(state)
    infinityJumpEnabled = state
end)

UserInputService.JumpRequest:Connect(function()
    if infinityJumpEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- Speed textbox + toggle
local SpeedBox = Instance.new("TextBox", FrameRacks)
SpeedBox.Size = UDim2.new(0.8,0,0,30)
SpeedBox.Position = UDim2.new(0.1,0,0.4,0)
SpeedBox.PlaceholderText = "Velocidade (0-500)"
SpeedBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
SpeedBox.TextColor3 = Color3.fromRGB(255,255,255)

CreateToggle(FrameRacks,"Speed",function(state)
    speedEnabled = state
end)

-- Aplicar Speed
task.spawn(function()
    while true do
        task.wait(0.1)
        if speedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
            local spd = tonumber(SpeedBox.Text)
            LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = spd and math.clamp(spd,0,500) or 16
        end
    end
end)

-- =======================
-- COPY SKIN
-- =======================
local SkinList = Instance.new("ScrollingFrame",FrameSkin)
SkinList.Size = UDim2.new(0.8,0,0.5,0)
SkinList.Position = UDim2.new(0.1,0,0.1,0)
SkinList.BackgroundColor3 = Color3.fromRGB(40,40,40)
SkinList.BorderSizePixel = 0
SkinList.ScrollBarThickness = 6
local SkinLayout = Instance.new("UIListLayout", SkinList)
SkinLayout.SortOrder = Enum.SortOrder.LayoutOrder
SkinLayout.Padding = UDim.new(0,5)

local SelectedSkinPlayer = nil
local SelectedButton = nil

local function RefreshSkinList()
    SkinList:ClearAllChildren()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local btn = Instance.new("TextButton", SkinList)
            btn.Size = UDim2.new(1,0,0,30)
            btn.Text = player.Name
            btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
            btn.TextColor3 = Color3.fromRGB(255,255,255)
            btn.BorderSizePixel = 0
            btn.Font = Enum.Font.SourceSansBold
            btn.TextSize = 18

            btn.MouseButton1Click:Connect(function()
                if SelectedButton then
                    SelectedButton.BackgroundColor3 = Color3.fromRGB(60,60,60)
                end
                SelectedButton = btn
                SelectedSkinPlayer = player
                btn.BackgroundColor3 = Color3.fromRGB(0,200,0)
            end)
        end
    end
end

RefreshSkinList()

-- Atualizar lista quando jogador entra ou sai
Players.PlayerAdded:Connect(function()
    RefreshSkinList()
end)
Players.PlayerRemoving:Connect(function()
    RefreshSkinList()
end)

-- Botão copiar skin
local CopySkinButton = Instance.new("TextButton", FrameSkin)
CopySkinButton.Size = UDim2.new(0.8,0,0,40)
CopySkinButton.Position = UDim2.new(0.1,0,0.65,0)
CopySkinButton.Text = "Copiar Skin"
CopySkinButton.BackgroundColor3 = Color3.fromRGB(50,150,250)
CopySkinButton.TextColor3 = Color3.fromRGB(255,255,255)
CopySkinButton.BorderSizePixel = 0
CopySkinButton.Font = Enum.Font.SourceSansBold
CopySkinButton.TextSize = 18

CopySkinButton.MouseButton1Click:Connect(function()
    if SelectedSkinPlayer and SelectedSkinPlayer.Character and LocalPlayer.Character then
        local sourceChar = SelectedSkinPlayer.Character
        local targetChar = LocalPlayer.Character

        -- Copiar roupas e acessórios
        for _, item in pairs(sourceChar:GetChildren()) do
            if item:IsA("Accessory") or item:IsA("Clothing") then
                item:Clone().Parent = targetChar
            end
        end

        -- Copiar Body Colors
        local sourceBodyColors = sourceChar:FindFirstChild("Body Colors")
        if sourceBodyColors then
            local cloneColors = sourceBodyColors:Clone()
            cloneColors.Parent = targetChar
        end

        -- Copiar face
        local sourceFace = sourceChar:FindFirstChildOfClass("Decal")
        if sourceFace and sourceFace.Name == "face" then
            local faceClone = sourceFace:Clone()
            faceClone.Parent = targetChar:FindFirstChild("Head")
        end

        CopySkinButton.Text = "Copiado!"
        task.delay(2, function()
            CopySkinButton.Text = "Copiar Skin"
        end)
    end
end)
