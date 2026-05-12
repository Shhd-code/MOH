--[[
    السكربت: FFQ
    المطور: FFQ
    التصميم: عصري فخم
]]

local player = game.Players.LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local userInputService = game:GetService("UserInputService")
local runService = game:GetService("RunService")
local lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")

-- ========== المسارات ==========
local chatEvent = nil
local execSignal = nil

local remoteEvents = replicatedStorage:FindFirstChild("RemoteEvents")
if remoteEvents then
    chatEvent = remoteEvents:FindFirstChild("ChatEvent")
end
if not chatEvent then
    for _, v in pairs(replicatedStorage:GetDescendants()) do
        if v.Name == "ChatEvent" and v:IsA("RemoteEvent") then
            chatEvent = v
            break
        end
    end
end

local hdClient = replicatedStorage:FindFirstChild("HDAdminHDClient")
if hdClient and hdClient:FindFirstChild("Signals") then
    execSignal = hdClient.Signals:FindFirstChild("RequestCommandModification")
end

-- ========== دوال الحماية الجديدة ==========
local function deleteNightVision()
    local hdClient = replicatedStorage:FindFirstChild("HDAdminHDClient")
    if hdClient then
        local assets = hdClient:FindFirstChild("Assets")
        if assets then
            local nightVision = assets:FindFirstChild("NightVision")
            if nightVision then
                nightVision:Destroy()
                return true
            end
        end
    end
    return false
end

local function deleteHDInterface()
    local playerGui = player:FindFirstChild("PlayerGui")
    if playerGui then
        local hdInterface = playerGui:FindFirstChild("HDAdminInterface")
        if hdInterface then
            hdInterface:Destroy()
            return true
        end
    end
    return false
end

-- ========== دوال أساسية ==========
local function sendMsg(msg)
    if not chatEvent then return false end
    pcall(function() chatEvent:FireServer(msg) end)
    return true
end

local function execCmd(cmd)
    if not execSignal then return false end
    pcall(function() execSignal:InvokeServer(cmd) end)
    return true
end

-- ========== FFQ Zero Protocol Intro (Red Edition) ==========
local function runSplash()
    local core = game:GetService("CoreGui")
    local UIS = game:GetService("UserInputService")

    if core:FindFirstChild("FFQ_ZeroProtocol") then core.FFQ_ZeroProtocol:Destroy() end

    local sg = Instance.new("ScreenGui")
    sg.Name = "FFQ_ZeroProtocol"
    sg.DisplayOrder = 9999999
    sg.IgnoreGuiInset = true
    sg.Parent = core

    local bg = Instance.new("Frame", sg)
    bg.Size = UDim2.new(1, 0, 1, 0)
    bg.BackgroundColor3 = Color3.new(0, 0, 0)
    bg.BorderSizePixel = 0

    local skipHint = Instance.new("TextLabel", sg)
    skipHint.Size = UDim2.new(1, 0, 0, 28)
    skipHint.Position = UDim2.new(0, 0, 1, -36)
    skipHint.BackgroundTransparency = 1
    skipHint.Font = Enum.Font.GothamSemibold
    skipHint.Text = "اضغط مرتين لتخطي الإنترو"
    skipHint.TextSize = 13
    skipHint.TextColor3 = Color3.fromRGB(150, 0, 0) -- تلميح أحمر خافت
    skipHint.TextXAlignment = Enum.TextXAlignment.Center

    local skipped = false
    local lastClick = 0
    local function doSkip()
        if skipped then return end
        skipped = true
        TweenService:Create(bg, TweenInfo.new(0.3), {BackgroundTransparency = 1}):Play()
        task.delay(0.32, function()
            pcall(function() sg:Destroy() end)
        end)
    end

    local uisConn
    uisConn = UIS.InputBegan:Connect(function(inp, processed)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
            local now = tick()
            if (now - lastClick) < 0.4 then
                uisConn:Disconnect()
                doSkip()
            end
            lastClick = now
        end
    end)

    task.spawn(function()
        -- المرحلة 1: تشويه النظام (أحمر)
        for i = 1, 30 do
            if skipped then return end
            local line = Instance.new("Frame", sg)
            line.Size = UDim2.new(1, 0, 0, 1)
            line.Position = UDim2.new(0, 0, math.random(0, 100)/100, 0)
            line.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- خطوط حمراء
            line.BackgroundTransparency = 0.5
            line.BorderSizePixel = 0
            task.delay(0.2, function() pcall(function() line:Destroy() end) end)
            
            if i % 5 == 0 then
                local warn = Instance.new("TextLabel", sg)
                warn.Text = "DECRYPTING_FFQ_FILES..."
                warn.TextColor3 = Color3.fromRGB(200, 0, 0)
                warn.Font = Enum.Font.Code
                warn.TextSize = 20
                warn.BackgroundTransparency = 1
                warn.Position = UDim2.new(math.random(1, 7)/10, 0, math.random(1, 7)/10, 0)
                task.delay(0.5, function() pcall(function() warn:Destroy() end) end)
            end
            task.wait(0.1)
        end

        -- المرحلة 2: هوية FFQ
        if skipped then return end
        local sh_id = Instance.new("TextLabel", sg)
        sh_id.Text = "ID: FFQ_REDACTED"
        sh_id.TextColor3 = Color3.new(1, 1, 1)
        sh_id.Font = Enum.Font.SpecialElite
        sh_id.TextSize = 80
        sh_id.BackgroundTransparency = 1
        sh_id.Size = UDim2.new(1, 0, 0, 100)
        sh_id.Position = UDim2.new(0, 0, 0.45, 0)

        for i = 1, 40 do
            if skipped then return end
            sh_id.Position = UDim2.new(0, math.random(-5, 5), 0.45, math.random(-5, 5))
            sh_id.Rotation = math.random(-2, 2)
            task.wait(0.05)
        end

        if skipped then return end
        sh_id.Rotation = 0
        sh_id.Text = "F F Q"
        sh_id.TextSize = 150
        sh_id.TextColor3 = Color3.fromRGB(255, 0, 0) -- الاسم باللون الأحمر

        -- المرحلة 3: الإنهاء
        if skipped then return end
        local status = Instance.new("TextLabel", sg)
        status.Text = "FFQ_ACCESS_GRANTED"
        status.TextColor3 = Color3.new(1, 1, 1)
        status.BackgroundColor3 = Color3.fromRGB(150, 0, 0) -- خلفية الحالة حمراء
        status.Font = Enum.Font.Code
        status.TextSize = 25
        status.Size = UDim2.new(0, 300, 0, 40)
        status.Position = UDim2.new(0.5, -150, 0.8, 0)
        
        task.wait(5)

        if not skipped then
            skipped = true
            TweenService:Create(bg, TweenInfo.new(0.4), {BackgroundTransparency = 1}):Play()
            task.wait(0.42)
            pcall(function() sg:Destroy() end)
        end
    end)
end

runSplash()

-- ========== إنشاء الواجهة الرئيسية ==========
local gui = Instance.new("ScreenGui")
gui.Name = "FFQ"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = lighting

-- ========== زر الفتح/الإغلاق (قابل للسحب) ==========
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 50, 0, 50)
toggleBtn.Position = UDim2.new(0.85, 0, 0.1, 0)
toggleBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
toggleBtn.Text = "FFQ"
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.TextSize = 14
toggleBtn.BorderSizePixel = 0
toggleBtn.Parent = gui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(1, 0)
toggleCorner.Parent = toggleBtn

local toggleStroke = Instance.new("UIStroke")
toggleStroke.Thickness = 2
toggleStroke.Color = Color3.fromRGB(255, 50, 50)
toggleStroke.Parent = toggleBtn

local dragToggle = false
local dragStart1, startPos1

toggleBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragToggle = true
        dragStart1 = input.Position
        startPos1 = toggleBtn.Position
    end
end)

toggleBtn.InputEnded:Connect(function() dragToggle = false end)

userInputService.InputChanged:Connect(function(input)
    if dragToggle and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart1
        toggleBtn.Position = UDim2.new(startPos1.X.Scale, startPos1.X.Offset + delta.X, startPos1.Y.Scale, startPos1.Y.Offset + delta.Y)
    end
end)

-- ========== القائمة الرئيسية ==========
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 250, 0, 380)
mainFrame.Position = UDim2.new(0.5, -125, 0.35, -190)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 5, 5)
mainFrame.BackgroundTransparency = 0.05
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Visible = false
mainFrame.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 16)
mainCorner.Parent = mainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Thickness = 1.5
mainStroke.Color = Color3.fromRGB(200, 0, 0)
mainStroke.Parent = mainFrame

-- ========== شريط العنوان ==========
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 55)
titleBar.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 16)
titleCorner.Parent = titleBar

local titleMain = Instance.new("TextLabel")
titleMain.Size = UDim2.new(1, 0, 1, 0)
titleMain.BackgroundTransparency = 1
titleMain.Text = "FFQ"
titleMain.TextColor3 = Color3.fromRGB(255, 255, 255)
titleMain.Font = Enum.Font.GothamBold
titleMain.TextSize = 20
titleMain.Parent = titleBar

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 24, 0, 24)
closeBtn.Position = UDim2.new(1, -32, 0, 15)
closeBtn.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Parent = titleBar
local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(1, 0)
closeCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    blur.Size = 0
end)

-- سحب القائمة
local dragMenu = false
local dragStart2, startPos2
titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragMenu = true
        dragStart2 = input.Position
        startPos2 = mainFrame.Position
    end
end)
titleBar.InputEnded:Connect(function() dragMenu = false end)
userInputService.InputChanged:Connect(function(input)
    if dragMenu and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart2
        mainFrame.Position = UDim2.new(startPos2.X.Scale, startPos2.X.Offset + delta.X, startPos2.Y.Scale, startPos2.Y.Offset + delta.Y)
    end
end)

-- ========== صندوق الإدخال ==========
local inputBox = Instance.new("TextBox")
inputBox.Size = UDim2.new(0.85, 0, 0, 38)
inputBox.Position = UDim2.new(0.075, 0, 0.25, 0)
inputBox.BackgroundColor3 = Color3.fromRGB(40, 10, 10)
inputBox.PlaceholderText = "اكتب رسالة او امر..."
inputBox.Text = ""
inputBox.TextColor3 = Color3.fromRGB(255, 255, 255)
inputBox.Font = Enum.Font.Gotham
inputBox.TextSize = 11
inputBox.Parent = mainFrame
local inputCorner = Instance.new("UICorner")
inputCorner.CornerRadius = UDim.new(0, 8)
inputCorner.Parent = inputBox

-- ========== زر السبام ==========
local spamBtn = Instance.new("TextButton")
spamBtn.Size = UDim2.new(0.85, 0, 0, 40)
spamBtn.Position = UDim2.new(0.075, 0, 0.4, 0)
spamBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
spamBtn.Text = "تفعيل السبام"
spamBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
spamBtn.Font = Enum.Font.GothamBold
spamBtn.Parent = mainFrame
local spamCorner = Instance.new("UICorner")
spamCorner.CornerRadius = UDim.new(0, 10)
spamCorner.Parent = spamBtn

local indicator = Instance.new("Frame")
indicator.Size = UDim2.new(0, 10, 0, 10)
indicator.Position = UDim2.new(0.92, 0, 0.5, -5)
indicator.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
indicator.Parent = spamBtn
local indCorner = Instance.new("UICorner")
indCorner.CornerRadius = UDim.new(1, 0)
indCorner.Parent = indicator

-- ========== خانة اسم الهدف ==========
local playerNameBox = Instance.new("TextBox")
playerNameBox.Size = UDim2.new(0.85, 0, 0, 30)
playerNameBox.Position = UDim2.new(0.075, 0, 0.6, 0)
playerNameBox.BackgroundColor3 = Color3.fromRGB(40, 10, 10)
playerNameBox.PlaceholderText = "اكتب اسم اللاعب..."
playerNameBox.Text = ""
playerNameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
playerNameBox.Font = Enum.Font.Gotham
playerNameBox.Parent = mainFrame
local playerCorner = Instance.new("UICorner")
playerCorner.CornerRadius = UDim.new(0, 8)
playerCorner.Parent = playerNameBox

-- ========== النص الجاهز ==========
local templateText = "!re na !logs na !nv na !re na !logs na !nv na !re na !logs na !nv na !re na !logs na !nv !re na !logs na !nv na !re na !logs na !nv na !re na !logs na !nv na"

-- ========== زر تجهيز النسخ ==========
local prepareBtn = Instance.new("TextButton")
prepareBtn.Size = UDim2.new(0.4, 0, 0, 32)
prepareBtn.Position = UDim2.new(0.075, 0, 0.72, 0)
prepareBtn.BackgroundColor3 = Color3.fromRGB(130, 0, 0)
prepareBtn.Text = "تجهيز النسخ"
prepareBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
prepareBtn.Font = Enum.Font.GothamBold
prepareBtn.TextSize = 10
prepareBtn.Parent = mainFrame
local prepareCorner = Instance.new("UICorner")
prepareCorner.CornerRadius = UDim.new(0, 8)
prepareCorner.Parent = prepareBtn

prepareBtn.MouseButton1Click:Connect(function()
    local targetName = playerNameBox.Text
    if targetName == "" then return end
    inputBox.Text = string.gsub(templateText, "na", targetName)
    prepareBtn.Text = "تم"
    wait(0.6)
    prepareBtn.Text = "تجهيز النسخ"
end)

-- ========== زر الحماية ==========
local protectBtn = Instance.new("TextButton")
protectBtn.Size = UDim2.new(0.4, 0, 0, 32)
protectBtn.Position = UDim2.new(0.525, 0, 0.72, 0)
protectBtn.BackgroundColor3 = Color3.fromRGB(100, 20, 20)
protectBtn.Text = "حماية"
protectBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
protectBtn.Font = Enum.Font.GothamBold
protectBtn.Parent = mainFrame
local protectCorner = Instance.new("UICorner")
protectCorner.CornerRadius = UDim.new(0, 8)
protectCorner.Parent = protectBtn

protectBtn.MouseButton1Click:Connect(function()
    deleteNightVision()
    deleteHDInterface()
    sendMsg("تم تفعيل الحماية FFQ")
    execCmd("تم تفعيل الحماية FFQ")
    protectBtn.Text = "تم"
    wait(0.6)
    protectBtn.Text = "حماية"
end)

-- ========== منطق السبام ==========
local isSpamming = false
local spamConn = nil

spamBtn.MouseButton1Click:Connect(function()
    isSpamming = not isSpamming
    if isSpamming then
        local txt = inputBox.Text
        if txt == "" then isSpamming = false return end
        indicator.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        spamBtn.Text = "ايقاف السبام"
        spamConn = runService.Stepped:Connect(function()
            if isSpamming then
                sendMsg(txt)
                execCmd(txt)
            end
        end)
    else
        if spamConn then spamConn:Disconnect() end
        indicator.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
        spamBtn.Text = "تفعيل السبام"
    end
end)

-- ========== زر المؤثرات ==========
local effectBtn = Instance.new("TextButton")
effectBtn.Size = UDim2.new(0.85, 0, 0, 30)
effectBtn.Position = UDim2.new(0.075, 0, 0.85, 0)
effectBtn.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
effectBtn.Text = "تفعيل المؤثرات"
effectBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
effectBtn.Font = Enum.Font.GothamBold
effectBtn.Parent = mainFrame
local effectCorner = Instance.new("UICorner")
effectCorner.CornerRadius = UDim.new(0, 8)
effectCorner.Parent = effectBtn

local effectsActive = false
effectBtn.MouseButton1Click:Connect(function()
    effectsActive = not effectsActive
    if effectsActive then
        effectBtn.Text = "المؤثرات مفعلة"
        blur.Size = 5
    else
        effectBtn.Text = "تفعيل المؤثرات"
        blur.Size = 0
    end
end)

-- ========== فتح القائمة ==========
toggleBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
    if mainFrame.Visible then
        blur.Size = 4
        mainFrame:TweenSize(UDim2.new(0, 250, 0, 380), "Out", "Quad", 0.2, true)
    else
        if not effectsActive then blur.Size = 0 end
    end
end)

print("FFQ - Zero Protocol Active")
