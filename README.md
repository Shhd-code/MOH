--[[
    MOH Hub - Roblox UI Script
    Tab: تحكم (Control)
]]

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- helper: choose best parent for executor GUIs (Delta/Synapse/etc)
local function getGuiParent()
    local ok, hidden = pcall(function() return gethui() end)
    if ok and hidden then return hidden end
    local ok2, cg = pcall(function() return CoreGui end)
    if ok2 and cg then return cg end
    return PlayerGui
end
local guiParent = getGuiParent()

-- cleanup old
pcall(function()
    for _, n in ipairs({"MOHHub","MOHMini","MOHSplash"}) do
        local old = guiParent:FindFirstChild(n)
        if old then old:Destroy() end
        local old2 = PlayerGui:FindFirstChild(n)
        if old2 then old2:Destroy() end
    end
end)

----------------------------------------------------------------
-- إشعار فريق MOH Hub – يظهر بعد دقيقتين من تشغيل السكربت
----------------------------------------------------------------
local function getAvatar(username)
    local ok1, uid = pcall(function()
        return Players:GetUserIdFromNameAsync(username)
    end)
    if not ok1 then return "" end
    local ok2, url = pcall(function()
        return Players:GetUserThumbnailAsync(uid, Enum.ThumbnailType.AvatarBust, Enum.ThumbnailSize.Size420x420)
    end)
    return ok2 and url or ""
end

local function showTeamCard()
    pcall(function()
        local sg = Instance.new("ScreenGui")
        sg.Name = "MOH_TeamCard"
        sg.DisplayOrder = 9999997
        sg.IgnoreGuiInset = true
        sg.ResetOnSpawn = false
        pcall(function() sg.Parent = CoreGui end)
        if not sg.Parent then sg.Parent = PlayerGui end

        -- خلفية شفافة
        local overlay = Instance.new("Frame", sg)
        overlay.Size = UDim2.new(1, 0, 1, 0)
        overlay.BackgroundColor3 = Color3.new(0, 0, 0)
        overlay.BackgroundTransparency = 0.5
        overlay.BorderSizePixel = 0

        -- البطاقة الرئيسية
        local card = Instance.new("Frame", sg)
        card.Size = UDim2.new(0, 340, 0, 0)
        card.AnchorPoint = Vector2.new(0.5, 0.5)
        card.Position = UDim2.new(0.5, 0, 0.65, 0)
        card.BackgroundColor3 = Color3.fromRGB(20, 14, 0)
        card.BackgroundTransparency = 0.05
        card.BorderSizePixel = 0
        card.AutomaticSize = Enum.AutomaticSize.Y
        Instance.new("UICorner", card).CornerRadius = UDim.new(0, 18)
        local stroke = Instance.new("UIStroke", card)
        stroke.Color = Color3.fromRGB(255, 200, 0)
        stroke.Thickness = 1.8
        stroke.Transparency = 0.2

        local pad = Instance.new("UIPadding", card)
        pad.PaddingTop    = UDim.new(0, 16)
        pad.PaddingBottom = UDim.new(0, 20)
        pad.PaddingLeft   = UDim.new(0, 16)
        pad.PaddingRight  = UDim.new(0, 16)

        local layout = Instance.new("UIListLayout", card)
        layout.Padding = UDim.new(0, 14)
        layout.SortOrder = Enum.SortOrder.LayoutOrder
        layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

        -- عنوان البطاقة
        local title = Instance.new("TextLabel", card)
        title.Size = UDim2.new(1, 0, 0, 26)
        title.BackgroundTransparency = 1
        title.Font = Enum.Font.GothamBold
        title.Text = "✦ فريق MOH Hub ✦"
        title.TextSize = 17
        title.TextColor3 = Color3.fromRGB(255, 210, 100)
        title.TextXAlignment = Enum.TextXAlignment.Center
        title.LayoutOrder = 1

        -- فاصل
        local div = Instance.new("Frame", card)
        div.Size = UDim2.new(1, 0, 0, 1)
        div.BackgroundColor3 = Color3.fromRGB(255, 180, 0)
        div.BackgroundTransparency = 0.5
        div.BorderSizePixel = 0
        div.LayoutOrder = 2

        -- دالة إنشاء بطاقة شخص
        local function makePersonCard(username, roleText, order)
            local avatarUrl = getAvatar(username)

            local row = Instance.new("Frame", card)
            row.Size = UDim2.new(1, 0, 0, 90)
            row.BackgroundColor3 = Color3.fromRGB(10, 20, 35)
            row.BackgroundTransparency = 0.3
            row.BorderSizePixel = 0
            row.LayoutOrder = order
            Instance.new("UICorner", row).CornerRadius = UDim.new(0, 12)

            -- الصورة
            local img = Instance.new("ImageLabel", row)
            img.Size = UDim2.new(0, 72, 0, 72)
            img.Position = UDim2.new(0, 10, 0.5, -36)
            img.BackgroundColor3 = Color3.fromRGB(20, 30, 50)
            img.BorderSizePixel = 0
            img.Image = avatarUrl
            Instance.new("UICorner", img).CornerRadius = UDim.new(0, 36)
            local imgStroke = Instance.new("UIStroke", img)
            imgStroke.Color = Color3.fromRGB(255, 200, 0)
            imgStroke.Thickness = 2
            imgStroke.Transparency = 0.1

            -- اسم الحساب
            local nameLbl = Instance.new("TextLabel", row)
            nameLbl.Size = UDim2.new(1, -96, 0, 32)
            nameLbl.Position = UDim2.new(0, 90, 0.5, -30)
            nameLbl.BackgroundTransparency = 1
            nameLbl.Font = Enum.Font.GothamBold
            nameLbl.Text = "@" .. username
            nameLbl.TextSize = 14
            nameLbl.TextColor3 = Color3.fromRGB(255, 240, 200)
            nameLbl.TextXAlignment = Enum.TextXAlignment.Right

            -- الدور
            local roleLbl = Instance.new("TextLabel", row)
            roleLbl.Size = UDim2.new(1, -96, 0, 28)
            roleLbl.Position = UDim2.new(0, 90, 0.5, 4)
            roleLbl.BackgroundTransparency = 1
            roleLbl.Font = Enum.Font.GothamSemibold
            roleLbl.Text = roleText
            roleLbl.TextSize = 13
            roleLbl.TextColor3 = Color3.fromRGB(255, 210, 100)
            roleLbl.TextXAlignment = Enum.TextXAlignment.Right
        end

        -- المصممه فقط
        makePersonCard("shhode320", "👑 المصممه", 3)

        -- زر إغلاق
        local closeBtn = Instance.new("TextButton", card)
        closeBtn.Size = UDim2.new(1, 0, 0, 34)
        closeBtn.BackgroundColor3 = Color3.fromRGB(210, 165, 0)
        closeBtn.BackgroundTransparency = 0.1
        closeBtn.BorderSizePixel = 0
        closeBtn.AutoButtonColor = false
        closeBtn.Font = Enum.Font.GothamBold
        closeBtn.Text = "حسناً 👍"
        closeBtn.TextSize = 14
        closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        closeBtn.LayoutOrder = 4
        Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 10)
        closeBtn.MouseEnter:Connect(function()
            TweenService:Create(closeBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(255, 180, 0)}):Play()
        end)
        closeBtn.MouseLeave:Connect(function()
            TweenService:Create(closeBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(210, 165, 0)}):Play()
        end)

        -- أنيميشن الظهور
        card.BackgroundTransparency = 1
        overlay.BackgroundTransparency = 1
        TweenService:Create(overlay, TweenInfo.new(0.3), {BackgroundTransparency = 0.5}):Play()
        TweenService:Create(card, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
            {Position = UDim2.new(0.5, 0, 0.5, 0), BackgroundTransparency = 0.05}):Play()

        local function closeCard()
            TweenService:Create(card,    TweenInfo.new(0.25), {BackgroundTransparency = 1}):Play()
            TweenService:Create(overlay, TweenInfo.new(0.25), {BackgroundTransparency = 1}):Play()
            task.delay(0.28, function() pcall(function() sg:Destroy() end) end)
        end

        closeBtn.MouseButton1Click:Connect(closeCard)
    end)
end

-- تشغيل الإشعار بعد دقيقتين
task.delay(120, function()
    showTeamCard()
end)

----------------------------------------------------------------
-- Loading splash: MOH Zero Protocol (hacker intro)
----------------------------------------------------------------

-- ─── نافذة "ما الجديد" تظهر بعد الإنترو ─────────────────────
local function showWhatsNew()
    local core = game:GetService("CoreGui")
    local wsg = Instance.new("ScreenGui")
    wsg.Name = "MOH_WhatsNew"
    wsg.DisplayOrder = 9999998
    wsg.IgnoreGuiInset = true
    wsg.ResetOnSpawn = false
    wsg.Parent = core

    -- خلفية شفافة داكنة
    local overlay = Instance.new("Frame", wsg)
    overlay.Size = UDim2.new(1, 0, 1, 0)
    overlay.BackgroundColor3 = Color3.new(0, 0, 0)
    overlay.BackgroundTransparency = 0.45
    overlay.BorderSizePixel = 0

    -- الإطار الرئيسي
    local card = Instance.new("Frame", wsg)
    card.Size = UDim2.new(0, 400, 0, 0)
    card.AnchorPoint = Vector2.new(0.5, 0.5)
    card.Position = UDim2.new(0.5, 0, 0.5, 0)
    card.BackgroundColor3 = Color3.fromRGB(20, 13, 0)
    card.BackgroundTransparency = 0.08
    card.BorderSizePixel = 0
    card.AutomaticSize = Enum.AutomaticSize.Y
    Instance.new("UICorner", card).CornerRadius = UDim.new(0, 16)
    local cs = Instance.new("UIStroke", card)
    cs.Color = Color3.fromRGB(230, 190, 0); cs.Thickness = 1.8; cs.Transparency = 0.15

    local cpad = Instance.new("UIPadding", card)
    cpad.PaddingTop = UDim.new(0, 16); cpad.PaddingBottom = UDim.new(0, 18)
    cpad.PaddingLeft = UDim.new(0, 18); cpad.PaddingRight = UDim.new(0, 18)

    local layout = Instance.new("UIListLayout", card)
    layout.Padding = UDim.new(0, 10)
    layout.SortOrder = Enum.SortOrder.LayoutOrder

    -- عنوان
    local titleRow = Instance.new("Frame", card)
    titleRow.Size = UDim2.new(1, 0, 0, 30)
    titleRow.BackgroundTransparency = 1
    titleRow.LayoutOrder = 1
    local tLayout = Instance.new("UIListLayout", titleRow)
    tLayout.FillDirection = Enum.FillDirection.Horizontal
    tLayout.VerticalAlignment = Enum.VerticalAlignment.Center
    tLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

    local titleLbl = Instance.new("TextLabel", titleRow)
    titleLbl.BackgroundTransparency = 1
    titleLbl.Font = Enum.Font.GothamBold
    titleLbl.Text = "✦ ما الجديد؟ ✦"
    titleLbl.TextSize = 20
    titleLbl.TextColor3 = Color3.fromRGB(255, 215, 0)
    titleLbl.AutomaticSize = Enum.AutomaticSize.XY

    -- فاصل
    local divider = Instance.new("Frame", card)
    divider.Size = UDim2.new(1, 0, 0, 1)
    divider.BackgroundColor3 = Color3.fromRGB(210, 165, 0)
    divider.BackgroundTransparency = 0.6
    divider.BorderSizePixel = 0
    divider.LayoutOrder = 2

    -- النص الرئيسي
    local bodyLines = {
        "تمت اضافه نسخه من سكربت بلو",
        "لتسهيل النسخ عليكم",
        "موجود في خانة التحكم",
        "",
        "......",
        "",
        "القادم أفضل أن شاء الله 😌🤞",
        "",
        "shhode320~",
    }

    local bodyLbl = Instance.new("TextLabel", card)
    bodyLbl.Size = UDim2.new(1, 0, 0, 0)
    bodyLbl.AutomaticSize = Enum.AutomaticSize.Y
    bodyLbl.BackgroundTransparency = 1
    bodyLbl.Font = Enum.Font.GothamSemibold
    bodyLbl.Text = table.concat(bodyLines, "\n")
    bodyLbl.TextSize = 14
    bodyLbl.TextColor3 = Color3.fromRGB(255, 238, 168)
    bodyLbl.TextXAlignment = Enum.TextXAlignment.Right
    bodyLbl.TextWrapped = true
    bodyLbl.RichText = false
    bodyLbl.LayoutOrder = 3

    -- زر إغلاق
    local closeBtn = Instance.new("TextButton", card)
    closeBtn.Size = UDim2.new(1, 0, 0, 36)
    closeBtn.BackgroundColor3 = Color3.fromRGB(170, 130, 0)
    closeBtn.BackgroundTransparency = 0.1
    closeBtn.BorderSizePixel = 0
    closeBtn.AutoButtonColor = false
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.Text = "فاهمت، يلا نبدأ! 🚀"
    closeBtn.TextSize = 15
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.LayoutOrder = 4
    Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 10)
    closeBtn.MouseEnter:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(210, 170, 0)}):Play()
    end)
    closeBtn.MouseLeave:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(170, 130, 0)}):Play()
    end)

    -- أنيميشن الظهور (AnchorPoint 0.5,0.5 → نبدأ من أسفل قليلاً)
    card.Position = UDim2.new(0.5, 0, 0.65, 0)
    card.BackgroundTransparency = 1
    TweenService:Create(card, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
        {Position = UDim2.new(0.5, 0, 0.5, 0), BackgroundTransparency = 0.08}):Play()

    local closed = false
    local function closeWhatsNew()
        if closed then return end; closed = true
        TweenService:Create(card, TweenInfo.new(0.2), {BackgroundTransparency = 1}):Play()
        TweenService:Create(overlay, TweenInfo.new(0.2), {BackgroundTransparency = 1}):Play()
        task.delay(0.22, function() pcall(function() wsg:Destroy() end) end)
    end

    closeBtn.MouseButton1Click:Connect(closeWhatsNew)
end

-- ─── الإنترو مع تخطي بالضغط مرتين ──────────────────────────
local function runSplash()
    local core    = game:GetService("CoreGui")
    local UIS     = game:GetService("UserInputService")

    if core:FindFirstChild("MOH_ZeroProtocol") then core.MOH_ZeroProtocol:Destroy() end

    local sg = Instance.new("ScreenGui")
    sg.Name = "MOH_ZeroProtocol"
    sg.DisplayOrder = 9999999
    sg.IgnoreGuiInset = true
    sg.Parent = core

    local bg = Instance.new("Frame", sg)
    bg.Size = UDim2.new(1, 0, 1, 0)
    bg.BackgroundColor3 = Color3.new(0, 0, 0)
    bg.BorderSizePixel = 0

    -- نص تلميح التخطي
    local skipHint = Instance.new("TextLabel", sg)
    skipHint.Size = UDim2.new(1, 0, 0, 28)
    skipHint.Position = UDim2.new(0, 0, 1, -36)
    skipHint.BackgroundTransparency = 1
    skipHint.Font = Enum.Font.GothamSemibold
    skipHint.Text = "اضغط مرتين لتخطي الإنترو"
    skipHint.TextSize = 13
    skipHint.TextColor3 = Color3.fromRGB(120, 120, 120)
    skipHint.TextXAlignment = Enum.TextXAlignment.Center

    -- منطق كشف الضغطة المزدوجة عبر UserInputService
    local skipped = false
    local lastClick = 0
    local function doSkip()
        if skipped then return end
        skipped = true
        TweenService:Create(bg, TweenInfo.new(0.3), {BackgroundTransparency = 1}):Play()
        task.delay(0.32, function()
            pcall(function() sg:Destroy() end)
            showWhatsNew()
        end)
    end

    local uisConn
    uisConn = UIS.InputBegan:Connect(function(inp, processed)
        if inp.UserInputType == Enum.UserInputType.MouseButton1
           or inp.UserInputType == Enum.UserInputType.Touch then
            local now = tick()
            if (now - lastClick) < 0.4 then
                uisConn:Disconnect()
                doSkip()
            end
            lastClick = now
        end
    end)

    task.spawn(function()
        -- المرحلة 1: تشويه النظام
        for i = 1, 30 do
            if skipped then return end
            local line = Instance.new("Frame", sg)
            line.Size = UDim2.new(1, 0, 0, 1)
            line.Position = UDim2.new(0, 0, math.random(0, 100)/100, 0)
            line.BackgroundColor3 = Color3.fromRGB(170, 0, 255)
            line.BackgroundTransparency = 0.5
            line.BorderSizePixel = 0
            task.delay(0.2, function() pcall(function() line:Destroy() end) end)
            if i % 5 == 0 then
                local warn = Instance.new("TextLabel", sg)
                warn.Text = "DECRYPTING_MOH_FILES..."
                warn.TextColor3 = Color3.new(0, 1, 0)
                warn.Font = Enum.Font.Code
                warn.TextSize = 20
                warn.BackgroundTransparency = 1
                warn.Position = UDim2.new(math.random(1, 7)/10, 0, math.random(1, 7)/10, 0)
                task.delay(0.5, function() pcall(function() warn:Destroy() end) end)
            end
            task.wait(0.1)
        end

        -- المرحلة 2: هوية رقمية
        if skipped then return end
        local sh_id = Instance.new("TextLabel", sg)
        sh_id.Text = "ID: MOH_REDACTED"
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
        sh_id.Text = "M O H"
        sh_id.TextSize = 150
        sh_id.TextColor3 = Color3.fromRGB(255, 220, 0)

        -- المرحلة 3: الإنهاء
        if skipped then return end
        local status = Instance.new("TextLabel", sg)
        status.Text = "ROOT_ACCESS_GRANTED"
        status.TextColor3 = Color3.new(1, 1, 1)
        status.BackgroundColor3 = Color3.fromRGB(150, 115, 0)
        status.Font = Enum.Font.Code
        status.TextSize = 25
        status.Size = UDim2.new(0, 300, 0, 40)
        status.Position = UDim2.new(0.5, -150, 0.8, 0)
        for i = 1, 50 do
            if skipped then return end
            task.wait(0.1)
        end

        -- إغلاق طبيعي
        if not skipped then
            skipped = true
            TweenService:Create(bg, TweenInfo.new(0.4), {BackgroundTransparency = 1}):Play()
            task.wait(0.42)
            pcall(function() sg:Destroy() end)
            showWhatsNew()
        end
    end)
end
pcall(runSplash)

----------------------------------------------------------------
-- Main GUI
----------------------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "MOHHub"; gui.ResetOnSpawn = false; gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling; gui.DisplayOrder = 9000
pcall(function() gui.Parent = guiParent end)
if not gui.Parent then gui.Parent = PlayerGui end

local main = Instance.new("Frame", gui)
main.Name = "Main"; main.AnchorPoint = Vector2.new(0.5, 0.5)
main.Position = UDim2.new(0.5, 0, 0.5, 0); main.Size = UDim2.new(0, 520, 0, 360)
main.BackgroundColor3 = Color3.fromRGB(18, 12, 0); main.BackgroundTransparency = 0.25
main.BorderSizePixel = 0
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 14)
local mStroke = Instance.new("UIStroke", main)
mStroke.Color = Color3.fromRGB(230, 190, 0); mStroke.Thickness = 1.4; mStroke.Transparency = 0.25
local mGradient = Instance.new("UIGradient", main)
mGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(32, 20, 0)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(16, 10, 0)),
}
mGradient.Rotation = 135

local glow = Instance.new("Frame", main)
glow.BackgroundColor3 = Color3.fromRGB(255, 215, 0); glow.BorderSizePixel = 0
glow.Size = UDim2.new(1, 0, 0, 2); glow.Position = UDim2.new(0, 0, 0, 44)
local glowGrad = Instance.new("UIGradient", glow)
glowGrad.Transparency = NumberSequence.new{
    NumberSequenceKeypoint.new(0, 1),
    NumberSequenceKeypoint.new(0.5, 0.2),
    NumberSequenceKeypoint.new(1, 1),
}
task.spawn(function()
    while glow.Parent do
        glowGrad.Offset = Vector2.new(-1, 0)
        TweenService:Create(glowGrad, TweenInfo.new(2.2, Enum.EasingStyle.Linear), {Offset = Vector2.new(1, 0)}):Play()
        task.wait(2.2)
    end
end)

local title = Instance.new("TextLabel", main)
title.BackgroundTransparency = 1
title.Size = UDim2.new(1, -90, 0, 44); title.Position = UDim2.new(0, 16, 0, 0)
title.Font = Enum.Font.GothamBlack; title.Text = "⬛ MOH ⬛"
title.TextSize = 22; title.TextColor3 = Color3.fromRGB(255, 215, 0)
title.TextXAlignment = Enum.TextXAlignment.Left

----------------------------------------------------------------
-- Top buttons (close X + minimize circle)
----------------------------------------------------------------
local closeBtn = Instance.new("TextButton", main)
closeBtn.AnchorPoint = Vector2.new(1, 0)
closeBtn.Position = UDim2.new(1, -8, 0, 8)
closeBtn.Size = UDim2.new(0, 28, 0, 28)
closeBtn.BackgroundColor3 = Color3.fromRGB(70, 50, 0)
closeBtn.BackgroundTransparency = 0.3; closeBtn.BorderSizePixel = 0
closeBtn.Text = "X"; closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextColor3 = Color3.fromRGB(255, 240, 160); closeBtn.TextSize = 16
closeBtn.AutoButtonColor = false
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 8)
local closeStroke = Instance.new("UIStroke", closeBtn)
closeStroke.Color = Color3.fromRGB(210, 165, 0); closeStroke.Transparency = 0.4
closeBtn.MouseEnter:Connect(function() TweenService:Create(closeBtn, TweenInfo.new(0.15), {BackgroundTransparency = 0.1}):Play() end)
closeBtn.MouseLeave:Connect(function() TweenService:Create(closeBtn, TweenInfo.new(0.15), {BackgroundTransparency = 0.3}):Play() end)

-- circle minimize
local minBtn = Instance.new("TextButton", main)
minBtn.AnchorPoint = Vector2.new(1, 0)
minBtn.Position = UDim2.new(1, -44, 0, 8)
minBtn.Size = UDim2.new(0, 28, 0, 28)
minBtn.BackgroundColor3 = Color3.fromRGB(100, 75, 0)
minBtn.BackgroundTransparency = 0.2; minBtn.BorderSizePixel = 0
minBtn.Text = ""; minBtn.AutoButtonColor = false
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(1, 0) -- full circle
local minStroke = Instance.new("UIStroke", minBtn)
minStroke.Color = Color3.fromRGB(255, 215, 0); minStroke.Transparency = 0.2; minStroke.Thickness = 1.4
minBtn.MouseEnter:Connect(function() TweenService:Create(minBtn, TweenInfo.new(0.15), {BackgroundTransparency = 0}):Play() end)
minBtn.MouseLeave:Connect(function() TweenService:Create(minBtn, TweenInfo.new(0.15), {BackgroundTransparency = 0.2}):Play() end)

----------------------------------------------------------------
-- Floating mini circle (when hidden)
----------------------------------------------------------------
local miniGui = Instance.new("ScreenGui")
miniGui.Name = "MOHMini"; miniGui.ResetOnSpawn = false; miniGui.IgnoreGuiInset = true
miniGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling; miniGui.DisplayOrder = 9001
pcall(function() miniGui.Parent = guiParent end)
if not miniGui.Parent then miniGui.Parent = PlayerGui end

local miniBubble = Instance.new("TextButton", miniGui)
miniBubble.AnchorPoint = Vector2.new(0, 0.5)
miniBubble.Position = UDim2.new(0, 16, 0.5, 0)
miniBubble.Size = UDim2.new(0, 44, 0, 44)
miniBubble.BackgroundColor3 = Color3.fromRGB(120, 90, 0)
miniBubble.BackgroundTransparency = 0.15; miniBubble.BorderSizePixel = 0
miniBubble.AutoButtonColor = false
miniBubble.Text = "MOH"
miniBubble.Font = Enum.Font.GothamBlack
miniBubble.TextSize = 14
miniBubble.TextColor3 = Color3.fromRGB(230, 255, 240)
miniBubble.Visible = false
Instance.new("UICorner", miniBubble).CornerRadius = UDim.new(1, 0)
local miniStroke = Instance.new("UIStroke", miniBubble)
miniStroke.Color = Color3.fromRGB(255, 215, 0); miniStroke.Thickness = 1.6; miniStroke.Transparency = 0.2

-- pulse
task.spawn(function()
    while miniGui.Parent do
        if miniBubble.Visible then
            TweenService:Create(miniStroke, TweenInfo.new(0.7), {Transparency = 0.7}):Play(); task.wait(0.7)
            TweenService:Create(miniStroke, TweenInfo.new(0.7), {Transparency = 0.15}):Play(); task.wait(0.7)
        else
            task.wait(0.2)
        end
    end
end)

-- mini bubble stays ALWAYS visible. clicking it toggles the main panel.
miniBubble.Visible = true

local function setHidden(hidden)
    if hidden then
        TweenService:Create(main, TweenInfo.new(0.2), {Size = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1}):Play()
        task.wait(0.22)
        main.Visible = false
        main.Size = UDim2.new(0, 520, 0, 360)
        main.BackgroundTransparency = 0.25
    else
        main.Visible = true
        main.Size = UDim2.new(0, 0, 0, 0)
        main.BackgroundTransparency = 1
        TweenService:Create(main, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 520, 0, 360), BackgroundTransparency = 0.25}):Play()
    end
end

minBtn.MouseButton1Click:Connect(function() setHidden(true) end)
miniBubble.MouseButton1Click:Connect(function()
    setHidden(main.Visible)
end)

closeBtn.MouseButton1Click:Connect(function()
    TweenService:Create(main, TweenInfo.new(0.2), {Size = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1}):Play()
    task.wait(0.22); gui:Destroy(); miniGui:Destroy()
end)

----------------------------------------------------------------
-- Drag main + mini
----------------------------------------------------------------
local function makeDraggable(handle, target)
    local dragging, dragStart, startPos
    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = target.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            target.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
makeDraggable(title, main)
makeDraggable(miniBubble, miniBubble)

----------------------------------------------------------------
-- Buttons area (no tabs)
----------------------------------------------------------------
local function makeBigBtn(parent, text, posY, color1, color2)
    color1 = color1 or Color3.fromRGB(180, 140, 0)
    color2 = color2 or Color3.fromRGB(100, 75, 0)
    local b = Instance.new("TextButton", parent)
    b.Position = UDim2.new(0, 10, 0, posY); b.Size = UDim2.new(1, -20, 0, 46)
    b.BackgroundColor3 = Color3.fromRGB(120, 90, 0); b.BackgroundTransparency = 0.15
    b.BorderSizePixel = 0; b.AutoButtonColor = false
    b.Font = Enum.Font.GothamBlack; b.Text = text; b.TextSize = 16
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 10)
    local s = Instance.new("UIStroke", b); s.Color = Color3.fromRGB(255, 215, 0); s.Transparency = 0.3
    local g = Instance.new("UIGradient", b)
    g.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, color1),
        ColorSequenceKeypoint.new(1, color2),
    }
    g.Rotation = 90
    b.MouseEnter:Connect(function() TweenService:Create(b, TweenInfo.new(0.15), {BackgroundTransparency = 0}):Play() end)
    b.MouseLeave:Connect(function() TweenService:Create(b, TweenInfo.new(0.15), {BackgroundTransparency = 0.15}):Play() end)
    return b
end

local ctrlScroll = Instance.new("ScrollingFrame", main)
ctrlScroll.Position = UDim2.new(0, 0, 0, 52)
ctrlScroll.Size = UDim2.new(1, 0, 1, -80)
ctrlScroll.BackgroundTransparency = 1
ctrlScroll.BorderSizePixel = 0
ctrlScroll.ScrollBarThickness = 4
ctrlScroll.ScrollBarImageColor3 = Color3.fromRGB(210, 165, 0)
ctrlScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
ctrlScroll.CanvasSize = UDim2.new(0, 0, 0, 0)

local cuteBtn      = makeBigBtn(ctrlScroll, "نسخ", 4,
    Color3.fromRGB(255, 215, 0), Color3.fromRGB(200, 140, 0))
local spamBtn      = makeBigBtn(ctrlScroll, "سبام", 58,
    Color3.fromRGB(255, 60, 60), Color3.fromRGB(180, 20, 20))
local skinsBtn     = makeBigBtn(ctrlScroll, "سكنات", 112,
    Color3.fromRGB(220, 50, 255), Color3.fromRGB(140, 10, 180))
local dancesBtn    = makeBigBtn(ctrlScroll, "رقصات", 166,
    Color3.fromRGB(255, 120, 20), Color3.fromRGB(200, 70, 0))
local loadBtn      = makeBigBtn(ctrlScroll, "راديو", 220,
    Color3.fromRGB(0, 210, 200), Color3.fromRGB(0, 130, 120))
local hideBtn      = makeBigBtn(ctrlScroll, "إخفاء رسائل السبام", 274,
    Color3.fromRGB(30, 130, 255), Color3.fromRGB(10, 60, 180))
local spinStartBtn = makeBigBtn(ctrlScroll, "تشغيل الدوران", 328,
    Color3.fromRGB(80, 220, 50), Color3.fromRGB(30, 140, 10))
local spinStopBtn  = makeBigBtn(ctrlScroll, "إيقاف الدوران", 382,
    Color3.fromRGB(220, 30, 60), Color3.fromRGB(140, 10, 30))
local logsBtn      = makeBigBtn(ctrlScroll, "حماية من logs / clogs", 436,
    Color3.fromRGB(150, 50, 255), Color3.fromRGB(80, 10, 170))
local titleBtn     = makeBigBtn(ctrlScroll, "تايتل", 490,
    Color3.fromRGB(255, 50, 150), Color3.fromRGB(180, 10, 90))
local allBtn       = makeBigBtn(ctrlScroll, "نسخ all", 544,
    Color3.fromRGB(0, 180, 255), Color3.fromRGB(0, 90, 180))
local blueBtn      = makeBigBtn(ctrlScroll, "نسخه من سكربت بلو", 598,
    Color3.fromRGB(20, 80, 255), Color3.fromRGB(10, 30, 160))

local ctrlStatus = Instance.new("TextLabel", main)
ctrlStatus.BackgroundTransparency = 1
ctrlStatus.Position = UDim2.new(0, 10, 1, -28); ctrlStatus.Size = UDim2.new(1, -20, 0, 22)
ctrlStatus.Font = Enum.Font.GothamSemibold; ctrlStatus.TextSize = 13
ctrlStatus.TextColor3 = Color3.fromRGB(220, 195, 120); ctrlStatus.Text = ""
ctrlStatus.TextXAlignment = Enum.TextXAlignment.Left

local function showBigNotice(text)
    local nGui = Instance.new("ScreenGui")
    nGui.Name = "MOHNotice"; nGui.ResetOnSpawn = false; nGui.IgnoreGuiInset = true
    nGui.DisplayOrder = 9998
    pcall(function() nGui.Parent = guiParent end)
    if not nGui.Parent then nGui.Parent = PlayerGui end

    local nFrame = Instance.new("Frame", nGui)
    nFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    nFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    nFrame.Size = UDim2.new(0, 520, 0, 220)
    nFrame.BackgroundColor3 = Color3.fromRGB(22, 14, 0)
    nFrame.BackgroundTransparency = 0.1
    nFrame.BorderSizePixel = 0
    Instance.new("UICorner", nFrame).CornerRadius = UDim.new(0, 16)
    local nStroke = Instance.new("UIStroke", nFrame)
    nStroke.Color = Color3.fromRGB(255, 215, 0); nStroke.Thickness = 2; nStroke.Transparency = 0.1

    local title = Instance.new("TextLabel", nFrame)
    title.BackgroundTransparency = 1
    title.Position = UDim2.new(0, 12, 0, 14); title.Size = UDim2.new(1, -24, 0, 32)
    title.Font = Enum.Font.GothamBlack; title.Text = "ملاحظة مهمة"
    title.TextSize = 22; title.TextColor3 = Color3.fromRGB(255, 215, 0)

    local body = Instance.new("TextLabel", nFrame)
    body.BackgroundTransparency = 1
    body.Position = UDim2.new(0, 16, 0, 56); body.Size = UDim2.new(1, -32, 1, -116)
    body.Font = Enum.Font.GothamSemibold; body.Text = text
    body.TextSize = 20; body.TextColor3 = Color3.fromRGB(230, 255, 235)
    body.TextWrapped = true; body.TextYAlignment = Enum.TextYAlignment.Top

    local okBtn = Instance.new("TextButton", nFrame)
    okBtn.AnchorPoint = Vector2.new(0.5, 1)
    okBtn.Position = UDim2.new(0.5, 0, 1, -14); okBtn.Size = UDim2.new(0, 160, 0, 38)
    okBtn.BackgroundColor3 = Color3.fromRGB(210, 165, 0); okBtn.BorderSizePixel = 0
    okBtn.Font = Enum.Font.GothamBold; okBtn.Text = "تمام"
    okBtn.TextSize = 18; okBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
    Instance.new("UICorner", okBtn).CornerRadius = UDim.new(0, 10)

    okBtn.MouseButton1Click:Connect(function() pcall(function() nGui:Destroy() end) end)
    task.delay(12, function() pcall(function() nGui:Destroy() end) end)
end

local dancesLoaded = false
dancesBtn.MouseButton1Click:Connect(function()
    if dancesLoaded then ctrlStatus.Text = "الرقصات مفعلة بالفعل" return end
    ctrlStatus.Text = "جاري تشغيل الرقصات..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-ARES-EMOTE-HUB-148804"))()
        end)
        if ok then dancesLoaded = true; ctrlStatus.Text = "تم تشغيل الرقصات"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

local controlLoaded = false
loadBtn.MouseButton1Click:Connect(function()
    showBigNotice("البس الراديو يلا يشتغل السكربت 🙌😌")
    if controlLoaded then ctrlStatus.Text = "الراديو مفعل بالفعل" return end
    ctrlStatus.Text = "جاري تشغيل الراديو..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/Raduo_moh/refs/heads/main/README.md"))()
        end)
        if ok then controlLoaded = true; ctrlStatus.Text = "تم تشغيل الراديو"
        else ctrlStatus.Text = "فشل التشغيل: " .. tostring(err):sub(1, 60) end
    end)
end)

----------------------------------------------------------------
-- Anti notification (activated by hide button)
----------------------------------------------------------------
local antiActive = false
local antiConnection
local function hideSystemNotifications(obj)
    if obj:IsA("TextLabel") or obj:IsA("TextBox") then
        local ok, txt = pcall(function() return obj.Text end)
        if ok and txt and (txt:find("Sending commands") or txt:find("CommandLimit")) then
            local frame = obj.Parent
            if frame then
                pcall(function() frame.Visible = false; frame:Destroy() end)
            end
        end
    end
end

hideBtn.MouseButton1Click:Connect(function()
    if antiActive then
        ctrlStatus.Text = "حماية الواجهة مفعلة بالفعل"
        return
    end
    antiActive = true
    antiConnection = PlayerGui.DescendantAdded:Connect(function(d)
        task.wait(0.01)
        hideSystemNotifications(d)
    end)
    task.spawn(function()
        for _, v in ipairs(PlayerGui:GetDescendants()) do
            hideSystemNotifications(v)
        end
    end)
    ctrlStatus.Text = "تم تفعيل اخفاء رسائل السبام"
    print("تم تفعيل حماية الواجهة.. لن تظهر رسائل System بعد الآن.")
end)

----------------------------------------------------------------
-- Spin (دوران)
----------------------------------------------------------------
local spinning = false
local spinSpeed = 50
local RunService = game:GetService("RunService")

RunService.Heartbeat:Connect(function()
    if spinning then
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0)
        end
    end
end)

spinStartBtn.MouseButton1Click:Connect(function()
    spinning = true
    ctrlStatus.Text = "تم تشغيل الدوران"
end)
spinStopBtn.MouseButton1Click:Connect(function()
    spinning = false
    ctrlStatus.Text = "تم إيقاف الدوران"
end)

----------------------------------------------------------------
-- Logs / clogs protection
----------------------------------------------------------------
local logsActive = false
local function scanAndDestroy(obj)
    if obj:IsA("ScreenGui") or obj:IsA("Frame") then
        local nm = obj.Name:lower()
        if nm:find("log") or nm:find("admin") or nm:find("command") then
            pcall(function() obj:Destroy() end)
        end
    end
end

logsBtn.MouseButton1Click:Connect(function()
    if logsActive then
        ctrlStatus.Text = "حماية logs مفعلة بالفعل"
        return
    end
    logsActive = true
    for _, g in ipairs(PlayerGui:GetDescendants()) do
        scanAndDestroy(g)
    end
    PlayerGui.DescendantAdded:Connect(function(d)
        scanAndDestroy(d)
    end)
    task.spawn(function()
        while logsActive and task.wait(0.1) do
            for _, g in ipairs(PlayerGui:GetChildren()) do
                if g:IsA("ScreenGui") and (g.Name:find("Log") or g.Name:find("Admin")) then
                    pcall(function() g.Enabled = false; g:Destroy() end)
                end
            end
        end
    end)
    ctrlStatus.Text = "تم تفعيل حماية logs / clogs"
    print("تم تفعيل الحظر النهائي لقائمة اللوقز")
end)






----------------------------------------------------------------
-- Title control (SH RGB embedded)
----------------------------------------------------------------
local titleLoaded = false
local MOH_RGB_SOURCE = [==[
--[[
    MOH RGB - Roblox UI Script (Golden Theme)
    Sends title text + cycling RGB Color3 to ApplyTitle RemoteEvent
]]

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local function getGuiParent()
    local ok, hidden = pcall(function() return gethui() end)
    if ok and hidden then return hidden end
    local ok2, cg = pcall(function() return CoreGui end)
    if ok2 and cg then return cg end
    return PlayerGui
end
local guiParent = getGuiParent()

pcall(function()
    for _, n in ipairs({"MOHRGBHub","MOHRGBMini","MOHRGBSplash"}) do
        local old = guiParent:FindFirstChild(n)
        if old then old:Destroy() end
        local old2 = PlayerGui:FindFirstChild(n)
        if old2 then old2:Destroy() end
    end
end)

----------------------------------------------------------------
-- Loading splash
----------------------------------------------------------------
local function runSplash()
    local splash = Instance.new("ScreenGui")
    splash.Name = "MOHRGBSplash"; splash.ResetOnSpawn = false; splash.IgnoreGuiInset = true
    splash.DisplayOrder = 9999
    pcall(function() splash.Parent = guiParent end)
    if not splash.Parent then splash.Parent = PlayerGui end

    local f = Instance.new("Frame", splash)
    f.AnchorPoint = Vector2.new(0.5, 0.5)
    f.Position = UDim2.new(0.5, 0, 0.5, 0)
    f.Size = UDim2.new(0, 360, 0, 140)
    f.BackgroundColor3 = Color3.fromRGB(22, 14, 0)
    f.BackgroundTransparency = 0.15; f.BorderSizePixel = 0
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 14)
    local s = Instance.new("UIStroke", f)
    s.Color = Color3.fromRGB(255, 210, 0); s.Thickness = 1.4; s.Transparency = 0.2

    local n = Instance.new("TextLabel", f)
    n.BackgroundTransparency = 1
    n.Position = UDim2.new(0, 0, 0, 22); n.Size = UDim2.new(1, 0, 0, 44)
    n.Font = Enum.Font.GothamBlack; n.Text = "MOH RGB"
    n.TextSize = 32; n.TextColor3 = Color3.fromRGB(255, 215, 0)

    local st = Instance.new("TextLabel", f)
    st.BackgroundTransparency = 1
    st.Position = UDim2.new(0, 0, 0, 78); st.Size = UDim2.new(1, 0, 0, 28)
    st.Font = Enum.Font.GothamSemibold; st.Text = "جاري التشغيل..."
    st.TextSize = 18; st.TextColor3 = Color3.fromRGB(255, 240, 160)

    for i = 1, 3 do
        if not splash.Parent then break end
        pcall(function() TweenService:Create(s, TweenInfo.new(0.35), {Transparency = 0.7}):Play() end)
        task.wait(0.35)
        pcall(function() TweenService:Create(s, TweenInfo.new(0.35), {Transparency = 0.1}):Play() end)
        task.wait(0.35)
    end
    pcall(function() TweenService:Create(f, TweenInfo.new(0.4), {BackgroundTransparency = 1}):Play() end)
    pcall(function() TweenService:Create(n, TweenInfo.new(0.4), {TextTransparency = 1}):Play() end)
    pcall(function() TweenService:Create(st, TweenInfo.new(0.4), {TextTransparency = 1}):Play() end)
    pcall(function() TweenService:Create(s, TweenInfo.new(0.4), {Transparency = 1}):Play() end)
    task.wait(0.45)
    pcall(function() splash:Destroy() end)
end
pcall(runSplash)

----------------------------------------------------------------
-- Main GUI
----------------------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "MOHRGBHub"; gui.ResetOnSpawn = false; gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling; gui.DisplayOrder = 9000
pcall(function() gui.Parent = guiParent end)
if not gui.Parent then gui.Parent = PlayerGui end

local main = Instance.new("Frame", gui)
main.Name = "Main"; main.AnchorPoint = Vector2.new(0.5, 0.5)
main.Position = UDim2.new(0.5, 0, 0.5, 0); main.Size = UDim2.new(0, 420, 0, 320)
main.BackgroundColor3 = Color3.fromRGB(18, 12, 0); main.BackgroundTransparency = 0.25
main.BorderSizePixel = 0
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 14)
local mStroke = Instance.new("UIStroke", main)
mStroke.Color = Color3.fromRGB(230, 190, 0); mStroke.Thickness = 1.4; mStroke.Transparency = 0.25

local mGradient = Instance.new("UIGradient", main)
mGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(32, 20, 0)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(16, 10, 0)),
}
mGradient.Rotation = 135

local glow = Instance.new("Frame", main)
glow.BackgroundColor3 = Color3.fromRGB(255, 215, 0); glow.BorderSizePixel = 0
glow.Size = UDim2.new(1, 0, 0, 2); glow.Position = UDim2.new(0, 0, 0, 44)

-- Header
local header = Instance.new("Frame", main)
header.BackgroundTransparency = 1
header.Size = UDim2.new(1, 0, 0, 44)

local title = Instance.new("TextLabel", header)
title.BackgroundTransparency = 1
title.Position = UDim2.new(0, 14, 0, 0); title.Size = UDim2.new(1, -60, 1, 0)
title.Font = Enum.Font.GothamBlack; title.Text = "⬛ MOH RGB ⬛"
title.TextSize = 20; title.TextColor3 = Color3.fromRGB(255, 215, 0)
title.TextXAlignment = Enum.TextXAlignment.Left

local closeBtn = Instance.new("TextButton", header)
closeBtn.AnchorPoint = Vector2.new(1, 0.5)
closeBtn.Position = UDim2.new(1, -10, 0.5, 0); closeBtn.Size = UDim2.new(0, 28, 0, 28)
closeBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30); closeBtn.BorderSizePixel = 0
closeBtn.Font = Enum.Font.GothamBold; closeBtn.Text = "X"
closeBtn.TextSize = 16; closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 6)

-- Drag main
do
    local dragging, dragStart, startPos = false, nil, nil
    header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = main.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    header.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
    end)
end

----------------------------------------------------------------
-- Body with slots
----------------------------------------------------------------
local body = Instance.new("Frame", main)
body.Position = UDim2.new(0, 12, 0, 56); body.Size = UDim2.new(1, -24, 1, -68)
body.BackgroundTransparency = 1

local list = Instance.new("UIListLayout", body)
list.Padding = UDim.new(0, 10)
list.SortOrder = Enum.SortOrder.LayoutOrder

local Remote = ReplicatedStorage:FindFirstChild("ApplyTitle")
local activeLoops = {}

local function makeSlot(index, defaultText)
    local row = Instance.new("Frame", body)
    row.LayoutOrder = index
    row.Size = UDim2.new(1, 0, 0, 42); row.BackgroundTransparency = 1

    local box = Instance.new("TextBox", row)
    box.Size = UDim2.new(1, -100, 1, 0); box.Position = UDim2.new(0, 0, 0, 0)
    box.BackgroundColor3 = Color3.fromRGB(26, 17, 0); box.BackgroundTransparency = 0.15
    box.BorderSizePixel = 0
    box.Text = defaultText; box.PlaceholderText = "اكتب النص..."
    box.TextColor3 = Color3.fromRGB(220, 255, 230); box.PlaceholderColor3 = Color3.fromRGB(180, 155, 80)
    box.Font = Enum.Font.GothamSemibold; box.TextSize = 14
    box.ClearTextOnFocus = false
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 8)
    local bs = Instance.new("UIStroke", box)
    bs.Color = Color3.fromRGB(210, 165, 0); bs.Thickness = 1; bs.Transparency = 0.5

    local btn = Instance.new("TextButton", row)
    btn.AnchorPoint = Vector2.new(1, 0)
    btn.Position = UDim2.new(1, 0, 0, 0); btn.Size = UDim2.new(0, 92, 1, 0)
    btn.BackgroundColor3 = Color3.fromRGB(180, 140, 0); btn.BorderSizePixel = 0
    btn.Font = Enum.Font.GothamBold; btn.Text = "تشغيل"
    btn.TextSize = 14; btn.TextColor3 = Color3.fromRGB(0, 0, 0)
    btn.AutoButtonColor = false
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    local bts = Instance.new("UIStroke", btn)
    bts.Color = Color3.fromRGB(255, 215, 0); bts.Thickness = 1; bts.Transparency = 0.3

    btn.MouseButton1Click:Connect(function()
        if activeLoops[index] then
            activeLoops[index] = false
            btn.Text = "تشغيل"
            btn.BackgroundColor3 = Color3.fromRGB(180, 140, 0)
            bts.Color = Color3.fromRGB(255, 215, 0)
        else
            if not Remote then
                Remote = ReplicatedStorage:FindFirstChild("ApplyTitle")
            end
            activeLoops[index] = true
            btn.Text = "إيقاف"
            btn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
            bts.Color = Color3.fromRGB(255, 80, 80)

            task.spawn(function()
                while activeLoops[index] do
                    local dynamicColor = Color3.fromHSV(tick() % 5 / 5, 1, 1)
                    pcall(function()
                        if Remote then Remote:FireServer(box.Text, dynamicColor) end
                    end)
                    task.wait(0.2)
                end
            end)
        end
    end)
end

makeSlot(1, "SH ON TOP")
makeSlot(2, "HAHAHAHA")
makeSlot(3, "SHAHAD WAS HERE")

----------------------------------------------------------------
-- Mini draggable bubble
----------------------------------------------------------------
local miniGui = Instance.new("ScreenGui")
miniGui.Name = "MOHRGBMini"; miniGui.ResetOnSpawn = false; miniGui.IgnoreGuiInset = true
miniGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling; miniGui.DisplayOrder = 9001
pcall(function() miniGui.Parent = guiParent end)
if not miniGui.Parent then miniGui.Parent = PlayerGui end

local miniBubble = Instance.new("TextButton", miniGui)
miniBubble.AnchorPoint = Vector2.new(0, 0.5)
miniBubble.Position = UDim2.new(0, 14, 0.35, 0)
miniBubble.Size = UDim2.new(0, 48, 0, 48)
miniBubble.BackgroundColor3 = Color3.fromRGB(150, 60, 200)
miniBubble.BackgroundTransparency = 0.1; miniBubble.BorderSizePixel = 0
miniBubble.AutoButtonColor = false
miniBubble.Text = "🌈"
miniBubble.Font = Enum.Font.GothamBlack
miniBubble.TextSize = 22
miniBubble.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", miniBubble).CornerRadius = UDim.new(0, 10)
local mbStroke = Instance.new("UIStroke", miniBubble)
mbStroke.Color = Color3.fromRGB(220, 120, 255); mbStroke.Thickness = 2; mbStroke.Transparency = 0.1

local mbGrad = Instance.new("UIGradient", miniBubble)
mbGrad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(180, 80, 230)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(90, 30, 150)),
}
mbGrad.Rotation = 135

-- color cycling animation (RGB style to differentiate from green SH)
task.spawn(function()
    local t = 0
    while miniBubble.Parent do
        t = t + 0.05
        pcall(function()
            mbStroke.Color = Color3.fromHSV(t % 1, 1, 1)
        end)
        task.wait(0.05)
    end
end)

-- draggable + click-to-toggle
do
    local dragging, dragStart, startPos, moved = false, nil, nil, false
    miniBubble.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; moved = false
            dragStart = input.Position; startPos = miniBubble.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            if delta.Magnitude > 4 then moved = true end
            miniBubble.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
    miniBubble.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
            if not moved then
                main.Visible = not main.Visible
            end
        end
    end)
end

closeBtn.MouseButton1Click:Connect(function()
    pcall(function() gui:Destroy() end)
    pcall(function() miniGui:Destroy() end)
end)

print("[MOH RGB] Loaded")

]==]

titleBtn.MouseButton1Click:Connect(function()
    if titleLoaded then ctrlStatus.Text = "تايتل مفعل بالفعل" return end
    ctrlStatus.Text = "جاري تشغيل تايتل..."
    task.spawn(function()
        local fn, err = loadstring(MOH_RGB_SOURCE)
        if not fn then ctrlStatus.Text = "فشل: " .. tostring(err):sub(1,60); return end
        local ok, runErr = pcall(fn)
        if ok then titleLoaded = true; ctrlStatus.Text = "تم تشغيل تايتل"
        else ctrlStatus.Text = "خطأ: " .. tostring(runErr):sub(1,60) end
    end)
end)

local cuteLoaded = false
cuteBtn.MouseButton1Click:Connect(function()
    if cuteLoaded then ctrlStatus.Text = "نسخ مفعل بالفعل" return end
    ctrlStatus.Text = "جاري تشغيل نسخ..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/Cute/refs/heads/main/README.md"))()
        end)
        if ok then cuteLoaded = true; ctrlStatus.Text = "تم تشغيل نسخ"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

spamBtn.MouseButton1Click:Connect(function()
    ctrlStatus.Text = "جاري تشغيل السبام..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/Spam_moh/refs/heads/main/README.md"))()
        end)
        if ok then ctrlStatus.Text = "تم تشغيل السبام"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

skinsBtn.MouseButton1Click:Connect(function()
    ctrlStatus.Text = "جاري تشغيل السكنات..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/Skin_moh/refs/heads/main/README.md"))()
        end)
        if ok then ctrlStatus.Text = "تم تشغيل السكنات"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

allBtn.MouseButton1Click:Connect(function()
    ctrlStatus.Text = "جاري تشغيل نسخ all..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/All_moh/refs/heads/main/README.md"))()
        end)
        if ok then ctrlStatus.Text = "تم تشغيل نسخ all"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

local blueLoaded = false
blueBtn.MouseButton1Click:Connect(function()
    if blueLoaded then ctrlStatus.Text = "نسخه معدلة من سكربت بلو مفعلة بالفعل" return end
    ctrlStatus.Text = "جاري تشغيل نسخه معدلة من سكربت بلو..."
    task.spawn(function()
        local ok, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Shhd-code/Blu_moh/refs/heads/main/README.md"))()
        end)
        if ok then blueLoaded = true; ctrlStatus.Text = "تم تشغيل نسخه معدلة من سكربت بلو"
        else ctrlStatus.Text = "فشل: " .. tostring(err):sub(1, 60) end
    end)
end)

print("[MOH] Loaded")
