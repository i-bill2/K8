-- K888 Hub (Smooth Fly & Full Functions)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

LocalPlayer.CharacterAdded:Connect(function(newChar)
    Character = newChar
    Humanoid = newChar:WaitForChild("Humanoid")
    RootPart = newChar:WaitForChild("HumanoidRootPart")
end)

-- Config States
local State = {
    InfiniteJump = false,
    JumpPower = 50,
    JumpPowerEnabled = false,
    WalkSpeed = 16,
    SpeedEnabled = false,
    FlyEnabled = false,
    FlySpeed = 50,
    Noclip = false,
    Tornado = false,
    LoopRunEnabled = false,
    MenuVisible = true
}

-- Cleanup GUI เก่า
if LocalPlayer:WaitForChild("PlayerGui"):FindFirstChild("K888_Hub_UI") then
    LocalPlayer.PlayerGui.K888_Hub_UI:Destroy()
end

----------------------------------------------------------------
-- GUI CREATION
----------------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "K888_Hub_UI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- ปุ่ม Toggle เมนูวงกลม (มุมซ้ายบน)
local ToggleIconButton = Instance.new("TextButton")
ToggleIconButton.Name = "ToggleIconButton"
ToggleIconButton.Size = UDim2.new(0, 45, 0, 45)
ToggleIconButton.Position = UDim2.new(0, 15, 0, 15)
ToggleIconButton.BackgroundColor3 = Color3.fromRGB(25, 12, 35)
ToggleIconButton.Text = "K8"
ToggleIconButton.TextColor3 = Color3.fromRGB(255, 30, 100)
ToggleIconButton.TextSize = 18
ToggleIconButton.Font = Enum.Font.SourceSansBold
ToggleIconButton.Parent = ScreenGui

local IconCorner = Instance.new("UICorner")
IconCorner.CornerRadius = UDim.new(1, 0)
IconCorner.Parent = ToggleIconButton

local IconStroke = Instance.new("UIStroke")
IconStroke.Color = Color3.fromRGB(235, 30, 100)
IconStroke.Thickness = 2
IconStroke.Parent = ToggleIconButton

-- Main Frame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 600, 0, 560)
MainFrame.Position = UDim2.new(0.5, -300, 0.5, -280)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 8, 22)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(235, 30, 100)
MainStroke.Thickness = 2
MainStroke.Parent = MainFrame

-- Top Bar Title
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -20, 0, 30)
TitleLabel.Position = UDim2.new(0, 15, 0, 10)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "K888 Hub - Smooth Fly Version"
TitleLabel.TextColor3 = Color3.fromRGB(220, 100, 255)
TitleLabel.TextSize = 20
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = MainFrame

-- Content Container
local ContentFrame = Instance.new("Frame")
ContentFrame.Size = UDim2.new(1, -20, 1, -50)
ContentFrame.Position = UDim2.new(0, 10, 0, 45)
ContentFrame.BackgroundTransparency = 1
ContentFrame.Parent = MainFrame

----------------------------------------------------------------
-- HELPER FUNCTIONS
----------------------------------------------------------------
local function CreateCard(titleText, subText, pos, size)
    local Card = Instance.new("Frame")
    Card.Size = size
    Card.Position = pos
    Card.BackgroundColor3 = Color3.fromRGB(20, 10, 30)
    Card.BorderSizePixel = 0
    Card.Parent = ContentFrame

    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = Card

    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Color3.fromRGB(180, 30, 90)
    Stroke.Thickness = 1.5
    Stroke.Parent = Card

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(0.7, 0, 0, 22)
    Title.Position = UDim2.new(0, 10, 0, 5)
    Title.BackgroundTransparency = 1
    Title.Text = titleText
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 15
    Title.Font = Enum.Font.SourceSansBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Card

    local Sub = Instance.new("TextLabel")
    Sub.Size = UDim2.new(0.9, 0, 0, 15)
    Sub.Position = UDim2.new(0, 10, 0, 25)
    Sub.BackgroundTransparency = 1
    Sub.Text = subText
    Sub.TextColor3 = Color3.fromRGB(140, 140, 160)
    Sub.TextSize = 11
    Sub.Font = Enum.Font.SourceSans
    Sub.TextXAlignment = Enum.TextXAlignment.Left
    Sub.Parent = Card

    return Card
end

local function CreateToggle(parent, callback)
    local ToggleBtn = Instance.new("TextButton")
    ToggleBtn.Size = UDim2.new(0, 45, 0, 22)
    ToggleBtn.Position = UDim2.new(1, -55, 0, 8)
    ToggleBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    ToggleBtn.Text = ""
    ToggleBtn.AutoButtonColor = false
    ToggleBtn.Parent = parent

    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(1, 0)
    Corner.Parent = ToggleBtn

    local Circle = Instance.new("Frame")
    Circle.Size = UDim2.new(0, 18, 0, 18)
    Circle.Position = UDim2.new(0, 2, 0.5, -9)
    Circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    Circle.Parent = ToggleBtn

    local CircleCorner = Instance.new("UICorner")
    CircleCorner.CornerRadius = UDim.new(1, 0)
    CircleCorner.Parent = Circle

    local toggled = false
    ToggleBtn.MouseButton1Click:Connect(function()
        toggled = not toggled
        if toggled then
            TweenService:Create(ToggleBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(255, 30, 80)}):Play()
            TweenService:Create(Circle, TweenInfo.new(0.2), {Position = UDim2.new(1, -20, 0.5, -9)}):Play()
        else
            TweenService:Create(ToggleBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 70)}):Play()
            TweenService:Create(Circle, TweenInfo.new(0.2), {Position = UDim2.new(0, 2, 0.5, -9)}):Play()
        end
        callback(toggled)
    end)
end

----------------------------------------------------------------
-- CARDS UI
----------------------------------------------------------------

-- 1. Infinite Jump
local InfJumpCard = CreateCard("กระโดดไม่จำกัด (Infinite Jump)", "กด Spacebar/กระโดดรัวๆ กลางอากาศ", UDim2.new(0, 0, 0, 0), UDim2.new(0, 280, 0, 95))
CreateToggle(InfJumpCard, function(enabled)
    State.InfiniteJump = enabled
end)

-- 2. Jump Power
local JumpCard = CreateCard("กระโดดสูง (Jump Power)", "กรอกความสูงที่ต้องการ", UDim2.new(0, 295, 0, 0), UDim2.new(0, 285, 0, 95))
CreateToggle(JumpCard, function(enabled)
    State.JumpPowerEnabled = enabled
end)

local JumpBox = Instance.new("TextBox")
JumpBox.Size = UDim2.new(0, 265, 0, 30)
JumpBox.Position = UDim2.new(0, 10, 0, 50)
JumpBox.BackgroundColor3 = Color3.fromRGB(10, 5, 15)
JumpBox.Text = "100"
JumpBox.PlaceholderText = "ความสูง (1-1000)"
JumpBox.TextColor3 = Color3.fromRGB(255, 255, 255)
JumpBox.Font = Enum.Font.SourceSans
JumpBox.TextSize = 14
JumpBox.Parent = JumpCard

JumpBox.FocusLost:Connect(function()
    local val = tonumber(JumpBox.Text)
    if val then
        State.JumpPower = math.clamp(val, 1, 1000)
        JumpBox.Text = tostring(State.JumpPower)
    else
        JumpBox.Text = tostring(State.JumpPower)
    end
end)

-- 3. Fly Mode (Smooth Physics Fly)
local FlyCard = CreateCard("ระบบบินลื่นๆ (Smooth Fly)", "บินลอยเนียนๆ คุมด้วย W,A,S,D / E ขึ้น / Q ลง", UDim2.new(0, 0, 0, 105), UDim2.new(0, 280, 0, 95))
CreateToggle(FlyCard, function(enabled)
    State.FlyEnabled = enabled
end)

local FlyBox = Instance.new("TextBox")
FlyBox.Size = UDim2.new(0, 260, 0, 30)
FlyBox.Position = UDim2.new(0, 10, 0, 50)
FlyBox.BackgroundColor3 = Color3.fromRGB(10, 5, 15)
FlyBox.Text = "50"
FlyBox.PlaceholderText = "ความเร็วบิน (1-1000)"
FlyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlyBox.Font = Enum.Font.SourceSans
FlyBox.TextSize = 14
FlyBox.Parent = FlyCard

FlyBox.FocusLost:Connect(function()
    local val = tonumber(FlyBox.Text)
    if val then
        State.FlySpeed = math.clamp(val, 1, 1000)
        FlyBox.Text = tostring(State.FlySpeed)
    else
        FlyBox.Text = tostring(State.FlySpeed)
    end
end)

-- 4. WalkSpeed
local SpeedCard = CreateCard("ความเร็วเดิน (WalkSpeed)", "กรอกความเร็วการเดิน", UDim2.new(0, 295, 0, 105), UDim2.new(0, 285, 0, 95))
CreateToggle(SpeedCard, function(enabled)
    State.SpeedEnabled = enabled
end)

local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0, 265, 0, 30)
SpeedBox.Position = UDim2.new(0, 10, 0, 50)
SpeedBox.BackgroundColor3 = Color3.fromRGB(10, 5, 15)
SpeedBox.Text = "100"
SpeedBox.PlaceholderText = "ความเร็ว (1-10000)"
SpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedBox.Font = Enum.Font.SourceSans
SpeedBox.TextSize = 14
SpeedBox.Parent = SpeedCard

SpeedBox.FocusLost:Connect(function()
    local val = tonumber(SpeedBox.Text)
    if val then
        State.WalkSpeed = math.clamp(val, 1, 10000)
        SpeedBox.Text = tostring(State.WalkSpeed)
    else
        SpeedBox.Text = tostring(State.WalkSpeed)
    end
end)

-- 5. NoClip & Tornado
local NoclipCard = CreateCard("เดินทะลุกำแพง (NoClip)", "เปิดเพื่อเดินทะลุสิ่งกีดขวาง", UDim2.new(0, 0, 0, 210), UDim2.new(0, 280, 0, 85))
CreateToggle(NoclipCard, function(enabled)
    State.Noclip = enabled
end)

local TornadoCard = CreateCard("พายุหมุนดูดวัตถุ (Tornado)", "ดูดวัตถุ Unanchored มาหมุนรอบตัว", UDim2.new(0, 295, 0, 210), UDim2.new(0, 285, 0, 85))
CreateToggle(TornadoCard, function(enabled)
    State.Tornado = enabled
end)

-- 6. Executor & Loop Run
local ExecCard = CreateCard("ช่องใส่สคริปต์ & กดรันรัวๆ (Executor)", "วางสคริปต์ Lua แล้วสั่งรันครั้งเดียว หรือสั่งรันรัวๆ", UDim2.new(0, 0, 0, 305), UDim2.new(0, 580, 0, 195))

local ScriptInputBox = Instance.new("TextBox")
ScriptInputBox.Size = UDim2.new(0, 560, 0, 100)
ScriptInputBox.Position = UDim2.new(0, 10, 0, 45)
ScriptInputBox.BackgroundColor3 = Color3.fromRGB(10, 5, 15)
ScriptInputBox.Text = ""
ScriptInputBox.PlaceholderText = "-- วางสคริปต์ Lua ของคุณที่นี่..."
ScriptInputBox.TextColor3 = Color3.fromRGB(0, 255, 150)
ScriptInputBox.TextXAlignment = Enum.TextXAlignment.Left
ScriptInputBox.TextYAlignment = Enum.TextYAlignment.Top
ScriptInputBox.ClearTextOnFocus = false
ScriptInputBox.MultiLine = true
ScriptInputBox.Font = Enum.Font.Code
ScriptInputBox.TextSize = 13
ScriptInputBox.Parent = ExecCard

local ScriptBoxCorner = Instance.new("UICorner")
ScriptBoxCorner.CornerRadius = UDim.new(0, 6)
ScriptBoxCorner.Parent = ScriptInputBox

local RunOnceBtn = Instance.new("TextButton")
RunOnceBtn.Size = UDim2.new(0, 150, 0, 30)
RunOnceBtn.Position = UDim2.new(0, 10, 0, 152)
RunOnceBtn.BackgroundColor3 = Color3.fromRGB(40, 120, 220)
RunOnceBtn.Text = "▶ รันสคริปต์"
RunOnceBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
RunOnceBtn.Font = Enum.Font.SourceSansBold
RunOnceBtn.TextSize = 14
RunOnceBtn.Parent = ExecCard

local RunOnceCorner = Instance.new("UICorner")
RunOnceCorner.CornerRadius = UDim.new(0, 6)
RunOnceCorner.Parent = RunOnceBtn

local LoopRunBtn = Instance.new("TextButton")
LoopRunBtn.Size = UDim2.new(0, 180, 0, 30)
LoopRunBtn.Position = UDim2.new(0, 170, 0, 152)
LoopRunBtn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
LoopRunBtn.Text = "🔄 กดรันรัวๆ: OFF"
LoopRunBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
LoopRunBtn.Font = Enum.Font.SourceSansBold
LoopRunBtn.TextSize = 14
LoopRunBtn.Parent = ExecCard

local LoopRunCorner = Instance.new("UICorner")
LoopRunCorner.CornerRadius = UDim.new(0, 6)
LoopRunCorner.Parent = LoopRunBtn

----------------------------------------------------------------
-- EXECUTOR LOGIC
----------------------------------------------------------------
local function ExecuteCustomScript()
    local code = ScriptInputBox.Text
    if code and code ~= "" then
        local func, err = loadstring(code)
        if func then
            task.spawn(func)
        else
            warn("[K888 Error]: " .. tostring(err))
        end
    end
end

RunOnceBtn.MouseButton1Click:Connect(ExecuteCustomScript)

LoopRunBtn.MouseButton1Click:Connect(function()
    State.LoopRunEnabled = not State.LoopRunEnabled
    if State.LoopRunEnabled then
        LoopRunBtn.Text = "🔄 กดรันรัวๆ: ON"
        LoopRunBtn.BackgroundColor3 = Color3.fromRGB(50, 180, 50)
    else
        LoopRunBtn.Text = "🔄 กดรันรัวๆ: OFF"
        LoopRunBtn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
    end
end)

----------------------------------------------------------------
-- TOGGLE MENU LOGIC
----------------------------------------------------------------
local function ToggleMenu()
    State.MenuVisible = not State.MenuVisible
    MainFrame.Visible = State.MenuVisible
end

ToggleIconButton.MouseButton1Click:Connect(ToggleMenu)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        ToggleMenu()
    end
end)

----------------------------------------------------------------
-- CORE LOGIC
----------------------------------------------------------------

-- 1. Infinite Jump
UserInputService.JumpRequest:Connect(function()
    if State.InfiniteJump and Humanoid then
        Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- 2. Jump Power
RunService.RenderStepped:Connect(function()
    if Humanoid then
        Humanoid.UseJumpPower = true
        if State.JumpPowerEnabled then
            Humanoid.JumpPower = State.JumpPower
        end
    end
end)

-- 3. Smooth Velocity Fly System (ระบบบินฟิสิกส์ลื่นๆ)
local alignVel, alignRot, att
RunService.RenderStepped:Connect(function()
    if State.FlyEnabled and Character and RootPart and Humanoid then
        Humanoid.PlatformStand = true

        if not att or att.Parent ~= RootPart then
            att = Instance.new("Attachment", RootPart)
        end
        if not alignVel or alignVel.Parent ~= RootPart then
            alignVel = Instance.new("LinearVelocity", RootPart)
            alignVel.Attachment0 = att
            alignVel.MaxForce = 9e9
            alignVel.VelocityConstraintMode = Enum.VelocityConstraintMode.Vector
        end

        local Camera = Workspace.CurrentCamera
        local moveDir = Vector3.new()

        -- PC Controls
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + Camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - Camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - Camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + Camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.E) then moveDir = moveDir + Vector3.new(0, 1, 0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.Q) then moveDir = moveDir - Vector3.new(0, 1, 0) end

        -- Mobile Controls
        if Humanoid.MoveDirection.Magnitude > 0 and moveDir.Magnitude == 0 then
            local camCF = Camera.CFrame
            local dir = Humanoid.MoveDirection
            moveDir = (camCF.LookVector * -dir.Z) + (camCF.RightVector * dir.X)
        end

        if moveDir.Magnitude > 0 then
            alignVel.VectorVelocity = moveDir.Unit * State.FlySpeed
        else
            alignVel.VectorVelocity = Vector3.new(0, 0, 0)
        end
    else
        if Humanoid then Humanoid.PlatformStand = false end
        if alignVel then alignVel:Destroy() end
        if att then att:Destroy() end
    end
end)

-- 4. WalkSpeed
RunService.RenderStepped:Connect(function()
    if Humanoid and State.SpeedEnabled then
        Humanoid.WalkSpeed = State.WalkSpeed
    end
end)

-- 5. NoClip
RunService.Stepped:Connect(function()
    if State.Noclip and Character then
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

-- 6. Item Tornado Logic
local angle = 0
RunService.Heartbeat:Connect(function()
    if State.Tornado and Character and RootPart then
        angle = angle + 0.1
        local radius = 12
        local index = 0

        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("BasePart") and not part.Anchored and not part:IsDescendantOf(Character) then
                index = index + 1
                
                local currentAngle = angle + (index * 0.4)
                local currentRadius = radius + (math.sin(index) * 4)
                local x = math.cos(currentAngle) * currentRadius
                local z = math.sin(currentAngle) * currentRadius
                local heightOffset = (index % 10) * 1.5

                local targetPosition = RootPart.Position + Vector3.new(x, heightOffset - 3, z)
                
                part.CanCollide = false
                part.CFrame = CFrame.new(targetPosition) * CFrame.Angles(0, currentAngle, 0)
                part.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end

    -- Loop Run Logic
    if State.LoopRunEnabled then
        ExecuteCustomScript()
    end
end)
