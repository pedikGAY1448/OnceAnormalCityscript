-- ==================== ЗАГРУЗЧИК ====================
local SCRIPT_1 = [==[
--[[
    РАСШИРЕННЫЙ СКАНЕР
--]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

if game.CoreGui:FindFirstChild("ExtendedScanner") then
    game.CoreGui.ExtendedScanner:Destroy()
end

local mainGui = Instance.new("ScreenGui")
mainGui.Name = "ExtendedScanner"
mainGui.ResetOnSpawn = false
mainGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
mainGui.Parent = game.CoreGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 260, 0, 240)
mainFrame.Position = UDim2.new(0, 50, 0, 50)
mainFrame.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
mainFrame.BorderSizePixel = 2
mainFrame.BorderColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = mainGui

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(30, 30, 30)
stroke.Thickness = 2
stroke.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.BorderSizePixel = 0
title.Text = "РАСШИРЕННЫЙ СКАНЕР"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.Parent = mainFrame

local function makeButton(text, yPos, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 38)
    btn.Position = UDim2.new(0.05, 0, 0, yPos)
    btn.BackgroundColor3 = color or Color3.fromRGB(90, 90, 90)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.Parent = mainFrame
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = btn
    return btn
end

local unloadBtn    = makeButton("ВЫГРУЗИТЬ", 40, Color3.fromRGB(180, 50, 50))
local scanToolBtn  = makeButton("СКАНИРОВАТЬ ИНСТРУМЕНТ", 88, Color3.fromRGB(90, 90, 90))
local scanMouseBtn = makeButton("СКАНИРОВАТЬ ПОД МЫШЬЮ", 136, Color3.fromRGB(90, 90, 90))
local searchBtn    = makeButton("ПОИСК ИНСТРУМЕНТА", 184, Color3.fromRGB(80, 110, 160))

local searchFrame = Instance.new("Frame")
searchFrame.Size = UDim2.new(0, 300, 0, 350)
searchFrame.Position = UDim2.new(0, 330, 0, 50)
searchFrame.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
searchFrame.BorderSizePixel = 2
searchFrame.BorderColor3 = Color3.fromRGB(30, 30, 30)
searchFrame.Active = true
searchFrame.Draggable = true
searchFrame.Visible = false
searchFrame.Parent = mainGui

local searchStroke = Instance.new("UIStroke")
searchStroke.Color = Color3.fromRGB(30, 30, 30)
searchStroke.Thickness = 2
searchStroke.Parent = searchFrame

local searchTitle = Instance.new("TextLabel")
searchTitle.Size = UDim2.new(1, -35, 0, 28)
searchTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
searchTitle.BorderSizePixel = 0
searchTitle.Text = "ПОИСК ИНСТРУМЕНТА"
searchTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
searchTitle.Font = Enum.Font.GothamBold
searchTitle.TextSize = 13
searchTitle.Parent = searchFrame

local searchCloseBtn = Instance.new("TextButton")
searchCloseBtn.Size = UDim2.new(0, 30, 0, 28)
searchCloseBtn.Position = UDim2.new(1, -30, 0, 0)
searchCloseBtn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
searchCloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
searchCloseBtn.Text = "X"
searchCloseBtn.Font = Enum.Font.GothamBold
searchCloseBtn.TextSize = 12
searchCloseBtn.BorderSizePixel = 0
searchCloseBtn.Parent = searchFrame

local searchBox = Instance.new("TextBox")
searchBox.Size = UDim2.new(1, -20, 0, 32)
searchBox.Position = UDim2.new(0, 10, 0, 35)
searchBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
searchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
searchBox.PlaceholderText = "Введи название..."
searchBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
searchBox.Font = Enum.Font.Code
searchBox.TextSize = 13
searchBox.Text = ""
searchBox.ClearTextOnFocus = false
searchBox.BorderSizePixel = 0
searchBox.Parent = searchFrame

local searchBoxCorner = Instance.new("UICorner")
searchBoxCorner.CornerRadius = UDim.new(0, 5)
searchBoxCorner.Parent = searchBox

local suggestionsFrame = Instance.new("ScrollingFrame")
suggestionsFrame.Size = UDim2.new(1, -20, 1, -80)
suggestionsFrame.Position = UDim2.new(0, 10, 0, 72)
suggestionsFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
suggestionsFrame.BorderSizePixel = 0
suggestionsFrame.ScrollBarThickness = 6
suggestionsFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
suggestionsFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
suggestionsFrame.Parent = searchFrame

local suggestionsCorner = Instance.new("UICorner")
suggestionsCorner.CornerRadius = UDim.new(0, 5)
suggestionsCorner.Parent = suggestionsFrame

local suggestionsLayout = Instance.new("UIListLayout")
suggestionsLayout.SortOrder = Enum.SortOrder.LayoutOrder
suggestionsLayout.Padding = UDim.new(0, 2)
suggestionsLayout.Parent = suggestionsFrame

local resultFrame = Instance.new("Frame")
resultFrame.Size = UDim2.new(0, 620, 0, 480)
resultFrame.Position = UDim2.new(0, 640, 0, 50)
resultFrame.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
resultFrame.BorderSizePixel = 2
resultFrame.BorderColor3 = Color3.fromRGB(30, 30, 30)
resultFrame.Active = true
resultFrame.Draggable = true
resultFrame.Visible = false
resultFrame.Parent = mainGui

local resultStroke = Instance.new("UIStroke")
resultStroke.Color = Color3.fromRGB(30, 30, 30)
resultStroke.Thickness = 2
resultStroke.Parent = resultFrame

local resultTitle = Instance.new("TextLabel")
resultTitle.Size = UDim2.new(1, -200, 0, 28)
resultTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
resultTitle.BorderSizePixel = 0
resultTitle.Text = "РЕЗУЛЬТАТ"
resultTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
resultTitle.Font = Enum.Font.GothamBold
resultTitle.TextSize = 12
resultTitle.Parent = resultFrame

local spawnBtn = Instance.new("TextButton")
spawnBtn.Size = UDim2.new(0, 70, 0, 28)
spawnBtn.Position = UDim2.new(1, -170, 0, 0)
spawnBtn.BackgroundColor3 = Color3.fromRGB(80, 160, 80)
spawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
spawnBtn.Text = "SPAWN"
spawnBtn.Font = Enum.Font.GothamBold
spawnBtn.TextSize = 11
spawnBtn.BorderSizePixel = 0
spawnBtn.Parent = resultFrame

local copyBtn = Instance.new("TextButton")
copyBtn.Size = UDim2.new(0, 70, 0, 28)
copyBtn.Position = UDim2.new(1, -100, 0, 0)
copyBtn.BackgroundColor3 = Color3.fromRGB(60, 130, 200)
copyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
copyBtn.Text = "КОПИЯ"
copyBtn.Font = Enum.Font.GothamBold
copyBtn.TextSize = 11
copyBtn.BorderSizePixel = 0
copyBtn.Parent = resultFrame

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 28)
closeBtn.Position = UDim2.new(1, -30, 0, 0)
closeBtn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Text = "X"
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 12
closeBtn.BorderSizePixel = 0
closeBtn.Parent = resultFrame

local filterFrame = Instance.new("Frame")
filterFrame.Size = UDim2.new(1, -10, 0, 30)
filterFrame.Position = UDim2.new(0, 5, 0, 33)
filterFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
filterFrame.BorderSizePixel = 0
filterFrame.Parent = resultFrame

local filterCorner = Instance.new("UICorner")
filterCorner.CornerRadius = UDim.new(0, 5)
filterCorner.Parent = filterFrame

local filterIcon = Instance.new("TextLabel")
filterIcon.Size = UDim2.new(0, 28, 1, 0)
filterIcon.BackgroundTransparency = 1
filterIcon.Text = "🔍"
filterIcon.TextColor3 = Color3.fromRGB(200, 200, 200)
filterIcon.Font = Enum.Font.GothamBold
filterIcon.TextSize = 14
filterIcon.Parent = filterFrame

local filterBox = Instance.new("TextBox")
filterBox.Size = UDim2.new(1, -70, 1, 0)
filterBox.Position = UDim2.new(0, 28, 0, 0)
filterBox.BackgroundTransparency = 1
filterBox.TextColor3 = Color3.fromRGB(255, 255, 255)
filterBox.PlaceholderText = "Фильтр строк..."
filterBox.PlaceholderColor3 = Color3.fromRGB(130, 130, 130)
filterBox.Font = Enum.Font.Code
filterBox.TextSize = 13
filterBox.Text = ""
filterBox.ClearTextOnFocus = false
filterBox.TextXAlignment = Enum.TextXAlignment.Left
filterBox.Parent = filterFrame

local filterCount = Instance.new("TextLabel")
filterCount.Size = UDim2.new(0, 60, 1, 0)
filterCount.Position = UDim2.new(1, -62, 0, 0)
filterCount.BackgroundTransparency = 1
filterCount.Text = ""
filterCount.TextColor3 = Color3.fromRGB(150, 220, 150)
filterCount.Font = Enum.Font.Code
filterCount.TextSize = 12
filterCount.Parent = filterFrame

local clearFilterBtn = Instance.new("TextButton")
clearFilterBtn.Size = UDim2.new(0, 20, 0, 20)
clearFilterBtn.Position = UDim2.new(1, -24, 0, 5)
clearFilterBtn.BackgroundColor3 = Color3.fromRGB(120, 50, 50)
clearFilterBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
clearFilterBtn.Text = "×"
clearFilterBtn.Font = Enum.Font.GothamBold
clearFilterBtn.TextSize = 14
clearFilterBtn.BorderSizePixel = 0
clearFilterBtn.Visible = false
clearFilterBtn.Parent = filterFrame

local clearFilterCorner = Instance.new("UICorner")
clearFilterCorner.CornerRadius = UDim.new(0, 10)
clearFilterCorner.Parent = clearFilterBtn

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -10, 1, -75)
scroll.Position = UDim2.new(0, 5, 0, 68)
scroll.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 8
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
scroll.Parent = resultFrame

local listLayout = Instance.new("UIListLayout")
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout.Padding = UDim.new(0, 2)
listLayout.Parent = scroll

local lockedValues = {}
local renderLines = {}
local currentScannedObject = nil

local function clearLockedForObject(obj)
    for i = #lockedValues, 1, -1 do
        if lockedValues[i].obj == obj then
            table.remove(lockedValues, i)
        end
    end
end

task.spawn(function()
    while true do
        task.wait(0.1)
        for _, entry in ipairs(lockedValues) do
            pcall(function()
                if entry.obj and entry.obj.Parent then
                    if entry.isAttribute then
                        entry.obj:SetAttribute(entry.key, entry.value)
                    else
                        entry.obj[entry.key] = entry.value
                    end
                end
            end)
        end
    end
end)

local allRows = {}
local currentFilter = ""

local function matchesFilter(text)
    if currentFilter == "" then return true end
    return text:lower():find(currentFilter:lower(), 1, true) ~= nil
end

local function applyFilter()
    local visibleCount = 0
    local totalCount = #allRows

    for _, entry in ipairs(allRows) do
        entry.gui.Visible = matchesFilter(entry.searchText)
    end

    for i, entry in ipairs(allRows) do
        if entry.isHeader then
            local hasVisibleChild = false
            for j = i + 1, #allRows do
                if allRows[j].isHeader then break end
                if allRows[j].gui.Visible then
                    hasVisibleChild = true
                    break
                end
            end
            entry.gui.Visible = hasVisibleChild or currentFilter == ""
        end
        if entry.gui.Visible then visibleCount = visibleCount + 1 end
    end

    if currentFilter ~= "" then
        filterCount.Text = visibleCount .. "/" .. totalCount
    else
        filterCount.Text = ""
    end
    clearFilterBtn.Visible = currentFilter ~= ""
end

local function registerRow(gui, searchText, isHeader)
    table.insert(allRows, { gui = gui, searchText = searchText, isHeader = isHeader or false })
end

local function addHeader(text)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -10, 0, 22)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = Color3.fromRGB(255, 210, 100)
    l.Font = Enum.Font.Code
    l.TextSize = 13
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = scroll
    table.insert(renderLines, text)
    registerRow(l, text, true)
end

local function addTextRow(text, color)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -10, 0, 18)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = color or Color3.fromRGB(220, 220, 220)
    l.Font = Enum.Font.Code
    l.TextSize = 13
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.TextWrapped = true
    l.AutomaticSize = Enum.AutomaticSize.Y
    l.Parent = scroll
    table.insert(renderLines, text)
    registerRow(l, text, false)
end

local function addEditableRow(obj, key, isAttribute, value)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1, -10, 0, 22)
    row.BackgroundTransparency = 1
    row.Parent = scroll

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.65, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = "  [" .. (isAttribute and "A" or "P") .. "] " .. key .. " ="
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.Font = Enum.Font.Code
    label.TextSize = 13
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = row

    local box = Instance.new("TextBox")
    box.Size = UDim2.new(0.33, -5, 1, 0)
    box.Position = UDim2.new(0.66, 0, 0, 0)
    box.BackgroundColor3 = Color3.fromRGB(90, 80, 40)
    box.TextColor3 = Color3.fromRGB(255, 255, 200)
    box.Font = Enum.Font.Code
    box.TextSize = 13
    box.Text = tostring(value)
    box.ClearTextOnFocus = false
    box.BorderSizePixel = 0
    box.Parent = row

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 4)
    c.Parent = box

    box.FocusLost:Connect(function()
        local ok, err = pcall(function()
            local txt = box.Text
            local num = tonumber(txt)
            local newVal

            if num ~= nil then
                if num == math.floor(num) and not txt:find("%.") then
                    newVal = math.floor(num)
                else
                    newVal = num
                end
            elseif txt == "true" then newVal = true
            elseif txt == "false" then newVal = false
            else newVal = txt end

            for i = #lockedValues, 1, -1 do
                local e = lockedValues[i]
                if e.obj == obj and e.key == key and e.isAttribute == isAttribute then
                    table.remove(lockedValues, i)
                end
            end
            table.insert(lockedValues, {
                obj = obj, key = key, isAttribute = isAttribute, value = newVal,
            })

            if isAttribute then obj:SetAttribute(key, newVal)
            else obj[key] = newVal end

            box.BackgroundColor3 = Color3.fromRGB(60, 130, 60)
            task.delay(0.4, function()
                if box and box.Parent then
                    box.BackgroundColor3 = Color3.fromRGB(90, 80, 40)
                end
            end)
        end)
        if not ok then warn("[Сканер] Ошибка: " .. tostring(err)) end
    end)

    local lineText = "  [" .. (isAttribute and "A" or "P") .. "] " .. key .. " = " .. tostring(value)
    table.insert(renderLines, lineText)
    registerRow(row, key .. " " .. tostring(value) .. " " .. lineText, false)
end

local function formatValue(val)
    local t = typeof(val)
    if t == "Instance" then return val:GetFullName()
    elseif t == "table" then
        local parts = {}
        for k, v in pairs(val) do table.insert(parts, tostring(k) .. "=" .. tostring(v)) end
        return "{" .. table.concat(parts, ", ") .. "}"
    elseif t == "EnumItem" then return val.Name
    else return tostring(val) end
end

local function isEditableType(val)
    local t = typeof(val)
    return t == "number" or t == "string" or t == "boolean"
end

local function scanObject(obj, depth, maxDepth, seen)
    if not obj or seen[obj] then return end
    seen[obj] = true

    local indent = string.rep("  ", depth)
    addHeader(indent .. "> " .. obj.Name .. " [" .. obj.ClassName .. "]")
    addTextRow(indent .. "  FullName: " .. obj:GetFullName(), Color3.fromRGB(150, 200, 255))

    local attrs = obj:GetAttributes()
    for name, val in pairs(attrs) do
        if isEditableType(val) then addEditableRow(obj, name, true, val)
        else addTextRow(indent .. "  [A] " .. name .. " = " .. formatValue(val)) end
    end

    local props
    local ok = pcall(function() props = obj:GetProperties() end)
    if ok and props then
        for _, prop in ipairs(props) do
            local val
            local ok2 = pcall(function() val = obj[prop] end)
            if ok2 and isEditableType(val) then
                if prop ~= "Name" and prop ~= "Parent" then
                    addEditableRow(obj, prop, false, val)
                end
            end
        end
    end

    local tags = obj:GetTags()
    if #tags > 0 then
        addTextRow(indent .. "  [Теги] " .. table.concat(tags, ", "), Color3.fromRGB(180, 255, 180))
    end

    if depth < maxDepth then
        for _, child in ipairs(obj:GetChildren()) do
            scanObject(child, depth + 1, maxDepth, seen)
        end
    else
        local kids = obj:GetChildren()
        if #kids > 0 then
            addTextRow(indent .. "  ... (" .. #kids .. " детей пропущено)", Color3.fromRGB(150, 150, 150))
        end
    end
end

local function showResult(obj, headerText, maxDepth)
    currentScannedObject = obj

    allRows = {}
    currentFilter = ""
    if filterBox then filterBox.Text = "" end
    if filterCount then filterCount.Text = "" end
    if clearFilterBtn then clearFilterBtn.Visible = false end

    for _, child in ipairs(scroll:GetChildren()) do
        if child:IsA("GuiObject") then child:Destroy() end
    end
    renderLines = {}
    clearLockedForObject(obj)

    addHeader("=== " .. headerText .. " ===")
    addTextRow("Время: " .. os.date("%H:%M:%S"), Color3.fromRGB(150, 150, 150))

    local seen = {}
    scanObject(obj, 0, maxDepth, seen)

    resultFrame.Visible = true
    scroll.CanvasPosition = Vector2.new(0, 0)
end

local function collectAllTools()
    local tools = {}
    local seen = {}

    local function scan(container)
        if not container or seen[container] then return end
        seen[container] = true
        for _, obj in ipairs(container:GetDescendants()) do
            if obj:IsA("Tool") then
                table.insert(tools, obj)
            end
        end
    end

    scan(game:GetService("Workspace"))
    scan(game:GetService("ReplicatedStorage"))
    scan(game:GetService("ReplicatedFirst"))
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character then
            scan(plr.Character)
            for _, c in ipairs(plr.Character:GetChildren()) do
                if c:IsA("Tool") then table.insert(tools, c) end
            end
        end
        local bp = plr:FindFirstChildOfClass("Backpack")
        if bp then scan(bp) end
    end

    return tools
end

local function clearSuggestions()
    for _, child in ipairs(suggestionsFrame:GetChildren()) do
        if child:IsA("GuiObject") then child:Destroy() end
    end
end

local function addSuggestion(tool)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 28)
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.fromRGB(230, 230, 230)
    btn.Text = "  " .. tool.Name .. "   [" .. tool:GetFullName() .. "]"
    btn.Font = Enum.Font.Code
    btn.TextSize = 12
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.BorderSizePixel = 0
    btn.Parent = suggestionsFrame

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 4)
    c.Parent = btn

    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(85, 85, 85)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    end)
    btn.MouseButton1Click:Connect(function()
        showResult(tool, "ИНСТРУМЕНТ: " .. tool.Name, 5)
    end)
end

local function updateSuggestions()
    clearSuggestions()
    local query = searchBox.Text:lower()
    if query == "" then return end

    local allTools = collectAllTools()
    local matches = {}
    for _, tool in ipairs(allTools) do
        if tool.Name:lower():find(query, 1, true) then
            table.insert(matches, tool)
        end
    end

    table.sort(matches, function(a, b) return a.Name:lower() < b.Name:lower() end)

    for i, tool in ipairs(matches) do
        if i > 100 then break end
        addSuggestion(tool)
    end
end

searchBox:GetPropertyChangedSignal("Text"):Connect(updateSuggestions)

filterBox:GetPropertyChangedSignal("Text"):Connect(function()
    currentFilter = filterBox.Text
    applyFilter()
end)

clearFilterBtn.MouseButton1Click:Connect(function()
    filterBox.Text = ""
    currentFilter = ""
    applyFilter()
end)

local function scanTool()
    local char = player.Character
    if not char then return end
    local tool = char:FindFirstChildOfClass("Tool")
    if not tool then
        for _, child in ipairs(scroll:GetChildren()) do
            if child:IsA("GuiObject") then child:Destroy() end
        end
        renderLines = {}
        addHeader("Инструмент не найден.")
        resultFrame.Visible = true
        return
    end
    showResult(tool, "СКАНИРОВАНИЕ ИНСТРУМЕНТА", 5)
end

local mouseActive = false
local function toggleMouseScan()
    mouseActive = not mouseActive
    if mouseActive then
        scanMouseBtn.Text = "КЛИКНИ ПО ОБЪЕКТУ..."
        scanMouseBtn.BackgroundColor3 = Color3.fromRGB(200, 150, 60)
    else
        scanMouseBtn.Text = "СКАНИРОВАТЬ ПОД МЫШЬЮ"
        scanMouseBtn.BackgroundColor3 = Color3.fromRGB(90, 90, 90)
    end
end

mouse.Button1Down:Connect(function()
    if not mouseActive then return end
    local target = mouse.Target
    if target then
        mouseActive = false
        scanMouseBtn.Text = "СКАНИРОВАТЬ ПОД МЫШЬЮ"
        scanMouseBtn.BackgroundColor3 = Color3.fromRGB(90, 90, 90)
        showResult(target, "СКАНИРОВАНИЕ ПОД МЫШЬЮ", 3)
    end
end)

spawnBtn.MouseButton1Click:Connect(function()
    if not currentScannedObject then
        spawnBtn.Text = "НЕТ"
        task.wait(1)
        spawnBtn.Text = "SPAWN"
        return
    end

    local char = player.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local ok, err = pcall(function()
        local src = currentScannedObject

        local part
        if src:IsA("BasePart") then
            part = src
        elseif src:IsA("Tool") then
            part = src:FindFirstChild("Handle")
            if not part then
                for _, d in ipairs(src:GetDescendants()) do
                    if d:IsA("BasePart") then part = d break end
                end
            end
        else
            for _, d in ipairs(src:GetDescendants()) do
                if d:IsA("BasePart") then part = d break end
            end
        end

        if not part then
            warn("[SPAWN] Не найден BasePart.")
            return
        end

        local spawnPos = hrp.Position + hrp.CFrame.LookVector * 5 + Vector3.new(0, 3, 0)

        local newPart = Instance.new("Part")
        newPart.Size = part.Size
        newPart.Color = part.Color
        newPart.Material = part.Material
        newPart.Transparency = part.Transparency
        newPart.Anchored = true
        newPart.CanCollide = true
        newPart.Name = src.Name
        newPart.CFrame = CFrame.new(spawnPos) * CFrame.Angles(math.rad(-90), 0, 0)

        local mesh = part:FindFirstChildOfClass("SpecialMesh")
        if mesh then mesh:Clone().Parent = newPart end

        local blockMesh = part:FindFirstChildOfClass("BlockMesh")
        if blockMesh then blockMesh:Clone().Parent = newPart end

        local cylinderMesh = part:FindFirstChildOfClass("CylinderMesh")
        if cylinderMesh then cylinderMesh:Clone().Parent = newPart end

        local texture = part:FindFirstChildOfClass("Texture")
        if texture then texture:Clone().Parent = newPart end

        for _, d in ipairs(part:GetChildren()) do
            if d:IsA("Decal") then
                d:Clone().Parent = newPart
            end
        end

        newPart.Parent = workspace

        spawnBtn.Text = "OK!"
        task.wait(1)
        spawnBtn.Text = "SPAWN"
    end)

    if not ok then
        warn("[SPAWN] Ошибка: " .. tostring(err))
        spawnBtn.Text = "ОШИБ"
        task.wait(1)
        spawnBtn.Text = "SPAWN"
    end
end)

unloadBtn.MouseButton1Click:Connect(function() mainGui:Destroy() end)
scanToolBtn.MouseButton1Click:Connect(scanTool)
scanMouseBtn.MouseButton1Click:Connect(toggleMouseScan)

searchBtn.MouseButton1Click:Connect(function()
    searchFrame.Visible = not searchFrame.Visible
    if searchFrame.Visible then
        searchBox:CaptureFocus()
        updateSuggestions()
    end
end)

searchCloseBtn.MouseButton1Click:Connect(function()
    searchFrame.Visible = false
end)

copyBtn.MouseButton1Click:Connect(function()
    local text = table.concat(renderLines, "\n")
    if setclipboard then
        setclipboard(text)
        copyBtn.Text = "OK!"
        task.wait(1)
        copyBtn.Text = "КОПИЯ"
    else
        copyBtn.Text = "НЕТ"
        task.wait(1)
        copyBtn.Text = "КОПИЯ"
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    resultFrame.Visible = false
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end

    if input.KeyCode == Enum.KeyCode.RightShift then
        mainFrame.Visible = not mainFrame.Visible
        if not mainFrame.Visible then
            resultFrame.Visible = false
            searchFrame.Visible = false
        end
    end

    if input.KeyCode == Enum.KeyCode.F and UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
        if resultFrame.Visible then
            filterBox:CaptureFocus()
        end
    end
end)

print("[Extended Scanner] Загружено.")
]==]

-- ==================== КОД ВТОРОГО СКРИПТА ====================
local SCRIPT_2 = [==[
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

local customTpCoord = Vector3.new(2017, 55, 703)
local tpAfterGatherEnabled = false

local savedPoints = {}
local lastTeleportTime = 0
local selectedPoint = nil

if CoreGui:FindFirstChild("UnifiedCityMenu") then
    CoreGui.UnifiedCityMenu:Destroy()
end
pcall(function()
    if gethui and gethui():FindFirstChild("UnifiedCityMenu") then
        gethui().UnifiedCityMenu:Destroy()
    end
end)

local isUnloaded = false
local searchRadius = 200

local overlapParams = OverlapParams.new()
overlapParams.RespectCanCollide = false
overlapParams.FilterType = Enum.RaycastFilterType.Exclude

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "UnifiedCityMenu"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.DisplayOrder = 999

pcall(function()
    if gethui then
        screenGui.Parent = gethui()
    else
        screenGui.Parent = CoreGui
    end
end)
if not screenGui.Parent then
    screenGui.Parent = CoreGui
end

local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 220, 0, 560)
mainFrame.Position = UDim2.new(0.5, -110, 0.5, -280)
mainFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
mainFrame.BorderSizePixel = 1
mainFrame.BorderColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Visible = true
mainFrame.Parent = screenGui

local layout = Instance.new("UIListLayout")
layout.Parent = mainFrame
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 5)
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.VerticalAlignment = Enum.VerticalAlignment.Center

local radiusFrame = Instance.new("Frame")
radiusFrame.Size = UDim2.new(0, 190, 0, 28)
radiusFrame.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
radiusFrame.BorderSizePixel = 0
radiusFrame.LayoutOrder = 1
radiusFrame.Parent = mainFrame

local btnRadiusMinus = Instance.new("TextButton")
btnRadiusMinus.Size = UDim2.new(0, 35, 1, 0)
btnRadiusMinus.BackgroundColor3 = Color3.fromRGB(75, 75, 75)
btnRadiusMinus.Text = "-50"
btnRadiusMinus.TextColor3 = Color3.fromRGB(255, 255, 255)
btnRadiusMinus.Font = Enum.Font.SourceSansBold
btnRadiusMinus.TextSize = 12
btnRadiusMinus.Parent = radiusFrame

local radiusLabel = Instance.new("TextLabel")
radiusLabel.Size = UDim2.new(0, 120, 1, 0)
radiusLabel.Position = UDim2.new(0, 35, 0, 0)
radiusLabel.BackgroundTransparency = 1
radiusLabel.Text = "Радиус: " .. searchRadius
radiusLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
radiusLabel.Font = Enum.Font.SourceSansBold
radiusLabel.TextSize = 13
radiusLabel.Parent = radiusFrame

local btnRadiusPlus = Instance.new("TextButton")
btnRadiusPlus.Size = UDim2.new(0, 35, 1, 0)
btnRadiusPlus.Position = UDim2.new(1, -35, 0, 0)
btnRadiusPlus.BackgroundColor3 = Color3.fromRGB(75, 75, 75)
btnRadiusPlus.Text = "+50"
btnRadiusPlus.TextColor3 = Color3.fromRGB(255, 255, 255)
btnRadiusPlus.Font = Enum.Font.SourceSansBold
btnRadiusPlus.TextSize = 12
btnRadiusPlus.Parent = radiusFrame

btnRadiusMinus.MouseButton1Click:Connect(function()
    searchRadius = math.max(50, searchRadius - 50)
    radiusLabel.Text = "Радиус: " .. searchRadius
end)

btnRadiusPlus.MouseButton1Click:Connect(function()
    searchRadius = math.min(1000, searchRadius + 50)
    radiusLabel.Text = "Радиус: " .. searchRadius
end)

local espEnabled = false
local itemEspEnabled = false
local camNoclipEnabled = false
local fullbrightEnabled = false
local maxZoomEnabled = false

local origOcclusion = player.DevCameraOcclusionMode
local origBrightness = Lighting.Brightness
local origShadows = Lighting.GlobalShadows
local origOutdoorAmbient = Lighting.OutdoorAmbient
local origAmbient = Lighting.Ambient
local origMaxZoom = player.CameraMaxZoomDistance
local origMinZoom = player.CameraMinZoomDistance

local activeNpcEsps = {}
local activeItemEsps = {}
local camNoclipConn = nil
local fullbrightConn = nil
local maxZoomConn = nil
local uisConn = nil

local translationMap = {
    ["medkit"] = "Аптечка", ["med kit"] = "Аптечка", ["bandage"] = "Бинт", ["painkillers"] = "Обезболивающее",
    ["aseptic"] = "Асептический бинт", ["firstaid"] = "Аптечка", ["syringe"] = "Шприц", ["pills"] = "Таблетки",
    ["food"] = "Еда", ["apple"] = "Яблоко", ["burger"] = "Бургер", ["drink"] = "Напиток",
    ["flashlight"] = "Фонарик", ["key"] = "Ключ", ["lockpick"] = "Отмычка",
    ["armor"] = "Броня", ["helmet"] = "Шлем", ["vest"] = "Жилет", ["bodyarmor"] = "Бронежилет",
    ["shield"] = "Щит", ["chestplate"] = "Нагрудник", ["kevlar"] = "Кевлар", ["hat"] = "Каска/Шапка",
    ["mask"] = "Маска", ["boots"] = "Берцы", ["gloves"] = "Перчатки", ["plate"] = "Пластина",
    ["knife"] = "Нож", ["bat"] = "Бита", ["crowbar"] = "Монтировка", ["sword"] = "Меч",
    ["axe"] = "Топор", ["hammer"] = "Молоток", ["spear"] = "Копье", ["machete"] = "Мачете",
    ["katana"] = "Катана", ["dagger"] = "Кинжал", ["pipe"] = "Труба", ["shank"] = "Заточка",
    ["cleaver"] = "Тесак", ["stick"] = "Палка", ["club"] = "Дубинка",
    ["pistol"] = "Пистолет", ["shotgun"] = "Дробовик", ["rifle"] = "Винтовка", ["revolver"] = "Револьвер",
    ["ak47"] = "АК-47", ["m4a1"] = "M4A1", ["glock"] = "Глок", ["deagle"] = "Дигл",
    ["uzi"] = "Узи", ["mp5"] = "MP5", ["sniper"] = "Снайперка", ["weapon"] = "Оружие", ["gun"] = "Пушка",
    ["ammo"] = "Патроны", ["bullets"] = "Патроны", ["magazine"] = "Магазин", ["mag"] = "Магазин",
    ["shell"] = "Гильзы/Дробь", ["round"] = "Патрон", ["9mm"] = "Патроны 9мм", ["556"] = "Патроны 5.56",
    ["762"] = "Патроны 7.62",
    ["phone"] = "Телефон", ["smartphone"] = "Смартфон", ["tablet"] = "Планшет", ["ipad"] = "Планшет",
    ["lavalamp"] = "Лавовая лампа", ["lava lamp"] = "Лавовая лампа", ["lava_lamp"] = "Лавовая лампа",
    ["vase"] = "Ваза", ["perfume"] = "Духи", ["small perfume"] = "Маленький парфюм",
    ["large perfume"] = "Большой парфюм", ["cologne"] = "Парфюм", ["fragrance"] = "Парфюм",
    ["radio"] = "Радио", ["camera"] = "Камера", ["laptop"] = "Ноутбук", ["tv"] = "Телевизор",
    ["bottle"] = "Бутылка"
}

local meleeKeywords = {"knife", "bat", "crowbar", "sword", "blade", "axe", "hammer", "spear", "machete", "katana", "dagger", "pipe", "shank", "cleaver", "stick", "club", "нож", "бита", "топор", "меч", "монтировка"}
local gunKeywords = {"pistol", "shotgun", "rifle", "revolver", "ak47", "m4a1", "gun", "sniper", "smg", "glock", "deagle", "uzi", "mp5", "ar15", "carbine", "weapon", "blaster", "musket", "пистолет", "винтовка", "дробовик", "пушка"}
local ammoKeywords = {"ammo", "bullets", "shell", "magazine", "mag", "round", "9mm", "762", "556", "12gauge", "buckshot", "cartridge", "патроны", "обойма", "гильза"}
local gadgetKeywords = {"phone", "tablet", "lavalamp", "lava lamp", "lava_lamp", "vase", "perfume", "fragrance", "cologne", "electronics", "device", "camera", "radio", "laptop", "tv", "bottle", "телефон", "планшет", "лавовая", "ваза", "духи", "парфюм"}
local miscKeywords = {"armor", "helmet", "vest", "shield", "bodyarmor", "chestplate", "cap", "hat", "mask", "plate", "kevlar", "boots", "medkit", "bandage", "food", "key", "painkillers", "aseptic", "potion", "heal", "apple", "burger", "drink", "броня", "жилет", "шлем", "аптечка", "бинт", "еда", "ключ", "отмычка", "lockpick", "gloves"}

local blacklistNames = {
    "water", "glass", "window", "ocean", "river", "sea", "puddle", "ice", "wall", "floor", "baseplate", "terrain", "building", "door", "tree", "roof", "road", "street", "light", "ceiling", "part", "wedge", "meshpart"
}

local function translateItemName(originalName)
    local nameLower = string.lower(originalName)
    if translationMap[nameLower] then return translationMap[nameLower] end
    for eng, rus in pairs(translationMap) do
        if string.find(nameLower, eng) then return rus end
    end
    return originalName
end

local function getItemCategory(toolName)
    local lower = string.lower(toolName)
    for _, kw in ipairs(meleeKeywords) do if lower:find(kw) then return "Melee" end end
    for _, kw in ipairs(gunKeywords) do if lower:find(kw) then return "Gun" end end
    for _, kw in ipairs(ammoKeywords) do if lower:find(kw) then return "Ammo" end end
    for _, kw in ipairs(gadgetKeywords) do if lower:find(kw) then return "Gadget" end end
    for _, kw in ipairs(miscKeywords) do if lower:find(kw) then return "Misc" end end
    return "Unsorted"
end

local validToolCache = setmetatable({}, {__mode = "k"})

local function getValidTool(part)
    if not part or not part.Parent then return nil end

    local cached = validToolCache[part]
    if cached ~= nil then return cached or nil end

    if part.Anchored and not part:FindFirstChildWhichIsA("ClickDetector") and not part:FindFirstChildWhichIsA("ProximityPrompt") then
        local parentModel = part.Parent
        if parentModel == Workspace or not parentModel:IsA("Tool") then
            validToolCache[part] = false
            return nil
        end
    end

    local ancestorModel = part:FindFirstAncestorOfClass("Model")
    if ancestorModel then
        if ancestorModel:FindFirstChildOfClass("Humanoid") or Players:GetPlayerFromCharacter(ancestorModel) then
            validToolCache[part] = false
            return nil
        end
    end

    local objNameLower = string.lower(part.Name)
    for i = 1, #blacklistNames do
        if objNameLower == blacklistNames[i] then
            validToolCache[part] = false
            return nil
        end
    end

    local tool = part:FindFirstAncestorOfClass("Tool") or (part.Parent and part.Parent:IsA("Tool") and part.Parent)
    if tool then
        validToolCache[part] = tool
        return tool
    end

    if ancestorModel and ancestorModel ~= Workspace then
        local nameLower = string.lower(ancestorModel.Name)
        for i = 1, #blacklistNames do
            if nameLower:find(blacklistNames[i], 1, true) and not nameLower:find("lava", 1, true) then
                validToolCache[part] = false
                return nil
            end
        end

        local hasPrompt = ancestorModel:FindFirstChildOfClass("ClickDetector")
            or ancestorModel:FindFirstChildOfClass("ProximityPrompt")
            or ancestorModel:FindFirstChild("TouchInterest")

        local recognizedCategory = getItemCategory(ancestorModel.Name)

        if (ancestorModel:FindFirstChild("Handle") or hasPrompt) and recognizedCategory ~= "Unsorted" then
            validToolCache[part] = ancestorModel
            return ancestorModel
        end
    end

    validToolCache[part] = false
    return nil
end

local btnEsp = Instance.new("TextButton")
btnEsp.Size = UDim2.new(0, 190, 0, 30)
btnEsp.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnEsp.TextColor3 = Color3.fromRGB(200, 200, 200)
btnEsp.Font = Enum.Font.SourceSansBold
btnEsp.TextSize = 13
btnEsp.Text = "ESP: [ OFF ]"
btnEsp.LayoutOrder = 2
btnEsp.Parent = mainFrame

btnEsp.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    espEnabled = not espEnabled
    if espEnabled then
        btnEsp.Text = "ESP: [ ON ]"
        btnEsp.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnEsp.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        btnEsp.Text = "ESP: [ OFF ]"
        btnEsp.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnEsp.TextColor3 = Color3.fromRGB(200, 200, 200)
        for _, bb in pairs(activeNpcEsps) do if bb then bb:Destroy() end end
        activeNpcEsps = {}
    end
end)

local function getArmorData(model, humanoid)
    local armorVal, maxArmor = 0, 100
    local superArmorVal, maxSuperArmor = 0, 100

    local function checkVal(v)
        if type(v) == "number" then return v end
        if type(v) == "userdata" and v.Value then return tonumber(v.Value) end
        return tonumber(v)
    end

    local attrs = model:GetAttributes()
    for k, v in pairs(attrs) do
        local lk = string.lower(k)
        if lk == "armor" or lk == "shield" then armorVal = checkVal(v) or armorVal
        elseif lk == "maxarmor" or lk == "maxshield" then maxArmor = checkVal(v) or maxArmor
        elseif lk == "superarmor" or lk == "super armor" or lk == "super_armor" then superArmorVal = checkVal(v) or superArmorVal
        elseif lk == "maxsuperarmor" or lk == "maxsuper armor" or lk == "maxsuper_armor" then maxSuperArmor = checkVal(v) or maxSuperArmor
        end
    end

    if armorVal == 0 then
        local aObj = model:FindFirstChild("Armor") or model:FindFirstChild("Shield") or (humanoid and humanoid:FindFirstChild("Armor"))
        if aObj and aObj:IsA("ValueBase") then armorVal = checkVal(aObj.Value) or 0 end
    end
    if superArmorVal == 0 then
        local saObj = model:FindFirstChild("SuperArmor") or model:FindFirstChild("Super Armor") or model:FindFirstChild("super_armor")
        if saObj and saObj:IsA("ValueBase") then superArmorVal = checkVal(saObj.Value) or 0 end
    end

    maxArmor = math.max(maxArmor, armorVal, 1)
    maxSuperArmor = math.max(maxSuperArmor, superArmorVal, 1)

    local armorPct = math.clamp(math.floor((armorVal / maxArmor) * 100), 0, 100)
    local superArmorPct = math.clamp(math.floor((superArmorVal / maxSuperArmor) * 100), 0, 100)

    return armorPct, superArmorPct
end

local function createCustomNpcEsp(model, npcRoot)
    local bb = Instance.new("BillboardGui")
    bb.Name = "CustomNpcEsp"
    bb.Size = UDim2.new(4.5, 0, 5.5, 0)
    bb.StudsOffset = Vector3.new(0, 0, 0)
    bb.AlwaysOnTop = true
    bb.Adornee = npcRoot
    bb.Parent = model

    local mainBox = Instance.new("Frame")
    mainBox.Name = "MainBox"
    mainBox.Size = UDim2.new(1, 0, 1, 0)
    mainBox.BackgroundTransparency = 1
    mainBox.Parent = bb

    local boxColor = Color3.fromRGB(80, 240, 230)
    local cornerSize = 8
    local thickness = 2

    local innerFill = Instance.new("Frame")
    innerFill.Name = "InnerFill"
    innerFill.Size = UDim2.new(1, -(thickness * 2), 1, -(thickness * 2))
    innerFill.Position = UDim2.new(0, thickness, 0, thickness)
    innerFill.BackgroundColor3 = boxColor
    innerFill.BackgroundTransparency = 0.8
    innerFill.BorderSizePixel = 0
    innerFill.Parent = mainBox

    local function createLine(posX, posY, sizeX, sizeY)
        local line = Instance.new("Frame")
        line.BackgroundColor3 = boxColor
        line.BorderSizePixel = 0
        line.Position = UDim2.new(posX.Scale, posX.Offset, posY.Scale, posY.Offset)
        line.Size = UDim2.new(sizeX.Scale, sizeX.Offset, sizeY.Scale, sizeY.Offset)
        line.Parent = mainBox
    end

    createLine({Scale = 0, Offset = 0}, {Scale = 0, Offset = 0}, {Scale = 0, Offset = cornerSize}, {Scale = 0, Offset = thickness})
    createLine({Scale = 0, Offset = 0}, {Scale = 0, Offset = 0}, {Scale = 0, Offset = thickness}, {Scale = 0, Offset = cornerSize})
    createLine({Scale = 1, Offset = -cornerSize}, {Scale = 0, Offset = 0}, {Scale = 0, Offset = cornerSize}, {Scale = 0, Offset = thickness})
    createLine({Scale = 1, Offset = -thickness}, {Scale = 0, Offset = 0}, {Scale = 0, Offset = thickness}, {Scale = 0, Offset = cornerSize})
    createLine({Scale = 0, Offset = 0}, {Scale = 1, Offset = -thickness}, {Scale = 0, Offset = cornerSize}, {Scale = 0, Offset = thickness})
    createLine({Scale = 0, Offset = 0}, {Scale = 1, Offset = -cornerSize}, {Scale = 0, Offset = thickness}, {Scale = 0, Offset = cornerSize})
    createLine({Scale = 1, Offset = -cornerSize}, {Scale = 1, Offset = -thickness}, {Scale = 0, Offset = cornerSize}, {Scale = 0, Offset = thickness})
    createLine({Scale = 1, Offset = -thickness}, {Scale = 1, Offset = -cornerSize}, {Scale = 0, Offset = thickness}, {Scale = 0, Offset = cornerSize})

    local nameContainer = Instance.new("Frame")
    nameContainer.Name = "NameContainer"
    nameContainer.Size = UDim2.new(1, 10, 0, 12)
    nameContainer.Position = UDim2.new(0, -5, 0, -14)
    nameContainer.BackgroundColor3 = boxColor
    nameContainer.BackgroundTransparency = 0.65
    nameContainer.BorderSizePixel = 0
    nameContainer.Parent = mainBox

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "NameLabel"
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = string.lower(model.Name)
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.Font = Enum.Font.SourceSansItalic
    nameLabel.TextSize = 10
    nameLabel.TextStrokeTransparency = 0.4
    nameLabel.Parent = nameContainer

    local hpLabel = Instance.new("TextLabel")
    hpLabel.Name = "HpLabel"
    hpLabel.Size = UDim2.new(1, 0, 0.4, 0)
    hpLabel.Position = UDim2.new(0, 0, 0.3, 0)
    hpLabel.BackgroundTransparency = 1
    hpLabel.Text = "100%"
    hpLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    hpLabel.Font = Enum.Font.SourceSansItalic
    hpLabel.TextSize = 14
    hpLabel.TextStrokeTransparency = 0
    hpLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    hpLabel.Parent = mainBox

    local armorContainer = Instance.new("Frame")
    armorContainer.Name = "ArmorContainer"
    armorContainer.Size = UDim2.new(0, 22, 0, 48)
    armorContainer.Position = UDim2.new(1, 4, 0.1, 0)
    armorContainer.BackgroundTransparency = 1
    armorContainer.Parent = mainBox

    local regArmorBadge = Instance.new("Frame")
    regArmorBadge.Name = "RegArmorBadge"
    regArmorBadge.Size = UDim2.new(0, 20, 0, 20)
    regArmorBadge.BackgroundColor3 = Color3.fromRGB(25, 35, 45)
    regArmorBadge.BackgroundTransparency = 0.2
    regArmorBadge.BorderSizePixel = 1
    regArmorBadge.BorderColor3 = Color3.fromRGB(50, 220, 240)
    regArmorBadge.Parent = armorContainer

    local uicReg = Instance.new("UICorner")
    uicReg.CornerRadius = UDim.new(0.5, 0)
    uicReg.Parent = regArmorBadge

    local regArmorText = Instance.new("TextLabel")
    regArmorText.Name = "ArmorText"
    regArmorText.Size = UDim2.new(1, 0, 1, 0)
    regArmorText.BackgroundTransparency = 1
    regArmorText.Text = "80%"
    regArmorText.TextColor3 = Color3.fromRGB(255, 255, 255)
    regArmorText.Font = Enum.Font.SourceSansBold
    regArmorText.TextSize = 9
    regArmorText.Parent = regArmorBadge

    local superArmorBadge = Instance.new("Frame")
    superArmorBadge.Name = "SuperArmorBadge"
    superArmorBadge.Size = UDim2.new(0, 20, 0, 20)
    superArmorBadge.Position = UDim2.new(0, 0, 0, 22)
    superArmorBadge.BackgroundColor3 = Color3.fromRGB(20, 40, 25)
    superArmorBadge.BackgroundTransparency = 0.2
    superArmorBadge.BorderSizePixel = 1
    superArmorBadge.BorderColor3 = Color3.fromRGB(50, 255, 70)
    superArmorBadge.Parent = armorContainer

    local uicSuper = Instance.new("UICorner")
    uicSuper.CornerRadius = UDim.new(0.5, 0)
    uicSuper.Parent = superArmorBadge

    local superArmorText = Instance.new("TextLabel")
    superArmorText.Name = "ArmorText"
    superArmorText.Size = UDim2.new(1, 0, 1, 0)
    superArmorText.BackgroundTransparency = 1
    superArmorText.Text = "5%"
    superArmorText.TextColor3 = Color3.fromRGB(255, 255, 255)
    superArmorText.Font = Enum.Font.SourceSansBold
    superArmorText.TextSize = 9
    superArmorText.Parent = superArmorBadge

    return bb
end

task.spawn(function()
    while not isUnloaded do
        if espEnabled then
            local char = player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if root then
                overlapParams.FilterDescendantsInstances = {char}
                local partsInRadius = Workspace:GetPartBoundsInRadius(root.Position, searchRadius, overlapParams)
                local currentNpcs = {}

                for i = 1, #partsInRadius do
                    local part = partsInRadius[i]
                    local model = part.Parent

                    if model and model:IsA("Model") and not currentNpcs[model] then
                        local humanoid = model:FindFirstChildOfClass("Humanoid")
                        local npcRoot = model:FindFirstChild("HumanoidRootPart")

                        if humanoid and npcRoot and humanoid.Health > 0 then
                            local isPlayer = Players:GetPlayerFromCharacter(model) ~= nil

                            if not isPlayer then
                                currentNpcs[model] = true
                                local bb = activeNpcEsps[model]
                                if not bb or not bb.Parent then
                                    bb = createCustomNpcEsp(model, npcRoot)
                                    activeNpcEsps[model] = bb
                                end

                                local mainBox = bb:FindFirstChild("MainBox")
                                if mainBox then
                                    local hpLabel = mainBox:FindFirstChild("HpLabel")
                                    local armorContainer = mainBox:FindFirstChild("ArmorContainer")

                                    if hpLabel then
                                        local hpPct = math.clamp(math.floor((humanoid.Health / math.max(humanoid.MaxHealth, 1)) * 100), 0, 100)
                                        hpLabel.Text = hpPct .. "%"
                                    end

                                    local armorPct, superArmorPct = getArmorData(model, humanoid)

                                    if armorContainer then
                                        local regArmorBadge = armorContainer:FindFirstChild("RegArmorBadge")
                                        local superArmorBadge = armorContainer:FindFirstChild("SuperArmorBadge")

                                        if regArmorBadge then
                                            regArmorBadge.Visible = true
                                            local t = regArmorBadge:FindFirstChild("ArmorText")
                                            if t then t.Text = armorPct .. "%" end
                                        end

                                        if superArmorBadge then
                                            superArmorBadge.Visible = true
                                            local t = superArmorBadge:FindFirstChild("ArmorText")
                                            if t then t.Text = superArmorPct .. "%" end
                                        end
                                    end
                                end
                            end
                        end
                    end

                    if i % 20 == 0 then
                        task.wait()
                    end
                end

                for model, bb in pairs(activeNpcEsps) do
                    if not currentNpcs[model] or not model.Parent then
                        if bb then bb:Destroy() end
                        activeNpcEsps[model] = nil
                    end
                end
            end
        end
        task.wait(0.5)
    end
end)

local btnItemEsp = Instance.new("TextButton")
btnItemEsp.Size = UDim2.new(0, 190, 0, 30)
btnItemEsp.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnItemEsp.TextColor3 = Color3.fromRGB(200, 200, 200)
btnItemEsp.Font = Enum.Font.SourceSansBold
btnItemEsp.TextSize = 13
btnItemEsp.Text = "Item ESP: [ OFF ]"
btnItemEsp.LayoutOrder = 3
btnItemEsp.Parent = mainFrame

btnItemEsp.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    itemEspEnabled = not itemEspEnabled
    if itemEspEnabled then
        btnItemEsp.Text = "Item ESP: [ ON ]"
        btnItemEsp.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnItemEsp.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        btnItemEsp.Text = "Item ESP: [ OFF ]"
        btnItemEsp.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnItemEsp.TextColor3 = Color3.fromRGB(200, 200, 200)
        for _, bb in pairs(activeItemEsps) do if bb then bb:Destroy() end end
        activeItemEsps = {}
    end
end)

task.spawn(function()
    while not isUnloaded do
        if itemEspEnabled then
            local char = player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if root then
                overlapParams.FilterDescendantsInstances = {char}
                local partsInRadius = Workspace:GetPartBoundsInRadius(root.Position, searchRadius, overlapParams)
                local currentItems = {}

                for i = 1, #partsInRadius do
                    local part = partsInRadius[i]
                    local tool = getValidTool(part)

                    if tool and not currentItems[tool] then
                        local itemPart = tool:FindFirstChild("Handle") or tool:FindFirstChildWhichIsA("BasePart") or part
                        if itemPart then
                            currentItems[tool] = true
                            local bb = activeItemEsps[tool]

                            if not bb or not bb.Parent then
                                bb = Instance.new("BillboardGui")
                                bb.Name = "OptimizedItemEsp"
                                bb.Size = UDim2.new(0, 90, 0, 20)
                                bb.StudsOffset = Vector3.new(0, 1.5, 0)
                                bb.AlwaysOnTop = true
                                bb.Adornee = itemPart
                                bb.Parent = tool

                                local textLabel = Instance.new("TextLabel")
                                textLabel.Name = "Text"
                                textLabel.Size = UDim2.new(1, 0, 1, 0)
                                textLabel.BackgroundTransparency = 1
                                textLabel.TextColor3 = Color3.fromRGB(255, 255, 100)
                                textLabel.TextStrokeTransparency = 0
                                textLabel.Font = Enum.Font.SourceSansBold
                                textLabel.TextSize = 11
                                textLabel.Text = translateItemName(tool.Name)
                                textLabel.Parent = bb

                                activeItemEsps[tool] = bb
                            end
                        end
                    end

                    if i % 15 == 0 then
                        task.wait()
                    end
                end

                for tool, bb in pairs(activeItemEsps) do
                    if not currentItems[tool] or not tool.Parent then
                        if bb then bb:Destroy() end
                        activeItemEsps[tool] = nil
                    end
                end
            end
        end
        task.wait(1.0)
    end
end)

local btnCamNoclip = Instance.new("TextButton")
btnCamNoclip.Size = UDim2.new(0, 190, 0, 30)
btnCamNoclip.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnCamNoclip.TextColor3 = Color3.fromRGB(200, 200, 200)
btnCamNoclip.Font = Enum.Font.SourceSansBold
btnCamNoclip.TextSize = 13
btnCamNoclip.Text = "CamNoclip: [ OFF ]"
btnCamNoclip.LayoutOrder = 4
btnCamNoclip.Parent = mainFrame

btnCamNoclip.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    camNoclipEnabled = not camNoclipEnabled
    if camNoclipEnabled then
        btnCamNoclip.Text = "CamNoclip: [ ON ]"
        btnCamNoclip.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnCamNoclip.TextColor3 = Color3.fromRGB(255, 255, 255)
        if not camNoclipConn then
            camNoclipConn = RunService.RenderStepped:Connect(function()
                if camNoclipEnabled then
                    player.DevCameraOcclusionMode = Enum.DevCameraOcclusionMode.Invisicam
                end
            end)
        end
    else
        btnCamNoclip.Text = "CamNoclip: [ OFF ]"
        btnCamNoclip.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnCamNoclip.TextColor3 = Color3.fromRGB(200, 200, 200)
        player.DevCameraOcclusionMode = origOcclusion
        if camNoclipConn then
            camNoclipConn:Disconnect()
            camNoclipConn = nil
        end
    end
end)

local btnFullbright = Instance.new("TextButton")
btnFullbright.Size = UDim2.new(0, 190, 0, 30)
btnFullbright.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnFullbright.TextColor3 = Color3.fromRGB(200, 200, 200)
btnFullbright.Font = Enum.Font.SourceSansBold
btnFullbright.TextSize = 13
btnFullbright.Text = "Fullbright: [ OFF ]"
btnFullbright.LayoutOrder = 5
btnFullbright.Parent = mainFrame

local function enforceFullbright()
    if not fullbrightEnabled then return end
    Lighting.Brightness = 2
    Lighting.GlobalShadows = false
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.Ambient = Color3.fromRGB(255, 255, 255)
end

btnFullbright.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    fullbrightEnabled = not fullbrightEnabled
    if fullbrightEnabled then
        btnFullbright.Text = "Fullbright: [ ON ]"
        btnFullbright.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnFullbright.TextColor3 = Color3.fromRGB(255, 255, 255)
        enforceFullbright()
        if not fullbrightConn then
            fullbrightConn = RunService.RenderStepped:Connect(function()
                if fullbrightEnabled then
                    enforceFullbright()
                end
            end)
        end
    else
        btnFullbright.Text = "Fullbright: [ OFF ]"
        btnFullbright.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnFullbright.TextColor3 = Color3.fromRGB(200, 200, 200)
        if fullbrightConn then
            fullbrightConn:Disconnect()
            fullbrightConn = nil
        end
        Lighting.Brightness = origBrightness
        Lighting.GlobalShadows = origShadows
        Lighting.OutdoorAmbient = origOutdoorAmbient
        Lighting.Ambient = origAmbient
    end
end)

local btnMaxZoom = Instance.new("TextButton")
btnMaxZoom.Size = UDim2.new(0, 190, 0, 30)
btnMaxZoom.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnMaxZoom.TextColor3 = Color3.fromRGB(200, 200, 200)
btnMaxZoom.Font = Enum.Font.SourceSansBold
btnMaxZoom.TextSize = 13
btnMaxZoom.Text = "MaxZoom 9999: [ OFF ]"
btnMaxZoom.LayoutOrder = 6
btnMaxZoom.Parent = mainFrame

btnMaxZoom.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    maxZoomEnabled = not maxZoomEnabled
    if maxZoomEnabled then
        btnMaxZoom.Text = "MaxZoom 9999: [ ON ]"
        btnMaxZoom.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnMaxZoom.TextColor3 = Color3.fromRGB(255, 255, 255)
        player.CameraMaxZoomDistance = 9999
        if not maxZoomConn then
            maxZoomConn = RunService.RenderStepped:Connect(function()
                if maxZoomEnabled and player.CameraMaxZoomDistance ~= 9999 then
                    player.CameraMaxZoomDistance = 9999
                end
            end)
        end
    else
        btnMaxZoom.Text = "MaxZoom 9999: [ OFF ]"
        btnMaxZoom.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnMaxZoom.TextColor3 = Color3.fromRGB(200, 200, 200)
        player.CameraMaxZoomDistance = origMaxZoom
        if maxZoomConn then
            maxZoomConn:Disconnect()
            maxZoomConn = nil
        end
    end
end)

local btnGetTool = Instance.new("TextButton")
btnGetTool.Size = UDim2.new(0, 190, 0, 30)
btnGetTool.BackgroundColor3 = Color3.fromRGB(70, 70, 110)
btnGetTool.TextColor3 = Color3.fromRGB(255, 255, 255)
btnGetTool.Font = Enum.Font.SourceSansBold
btnGetTool.TextSize = 13
btnGetTool.Text = "Get Tool (Список лута)"
btnGetTool.LayoutOrder = 7
btnGetTool.Parent = mainFrame

local toolFrame = Instance.new("Frame")
toolFrame.Name = "ToolFrame"
toolFrame.Size = UDim2.new(0, 500, 0, 400)
toolFrame.Position = UDim2.new(0.5, -250, 0.5, -200)
toolFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
toolFrame.BorderSizePixel = 1
toolFrame.BorderColor3 = Color3.fromRGB(20, 20, 20)
toolFrame.Visible = false
toolFrame.Active = true
toolFrame.Draggable = true
toolFrame.Parent = screenGui

local toolTitle = Instance.new("TextLabel")
toolTitle.Size = UDim2.new(1, 0, 0, 25)
toolTitle.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toolTitle.Text = "Лут по дистанции + поиск"
toolTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
toolTitle.Font = Enum.Font.SourceSansBold
toolTitle.TextSize = 13
toolTitle.Parent = toolFrame

local refreshBtn = Instance.new("TextButton")
refreshBtn.Size = UDim2.new(0, 75, 0, 20)
refreshBtn.Position = UDim2.new(1, -80, 0, 2.5)
refreshBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
refreshBtn.Text = "Обновить"
refreshBtn.TextColor3 = Color3.fromRGB(220, 220, 220)
refreshBtn.Font = Enum.Font.SourceSans
refreshBtn.TextSize = 11
refreshBtn.Parent = toolTitle

local searchBox = Instance.new("TextBox")
searchBox.Size = UDim2.new(1, -10, 0, 22)
searchBox.Position = UDim2.new(0, 5, 0, 28)
searchBox.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
searchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
searchBox.PlaceholderText = "🔍 Поиск по названию..."
searchBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
searchBox.Font = Enum.Font.SourceSans
searchBox.TextSize = 12
searchBox.Text = ""
searchBox.ClearTextOnFocus = false
searchBox.Parent = toolFrame

local searchStatus = Instance.new("TextLabel")
searchStatus.Size = UDim2.new(1, -10, 0, 18)
searchStatus.Position = UDim2.new(0, 5, 0, 52)
searchStatus.BackgroundTransparency = 1
searchStatus.Text = "Найдено: 0"
searchStatus.TextColor3 = Color3.fromRGB(180, 220, 180)
searchStatus.Font = Enum.Font.SourceSans
searchStatus.TextSize = 12
searchStatus.TextXAlignment = Enum.TextXAlignment.Left
searchStatus.Parent = toolFrame

local mainScroll = Instance.new("ScrollingFrame")
mainScroll.Size = UDim2.new(1, -10, 1, -80)
mainScroll.Position = UDim2.new(0, 5, 0, 72)
mainScroll.BackgroundTransparency = 1
mainScroll.ScrollBarThickness = 6
mainScroll.Parent = toolFrame

local mainLayout = Instance.new("UIListLayout")
mainLayout.Parent = mainScroll
mainLayout.SortOrder = Enum.SortOrder.LayoutOrder
mainLayout.Padding = UDim.new(0, 3)

local isFetching = false

local function fetchTool(tool)
    if isFetching or not tool or not tool.Parent then return end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local camera = Workspace.CurrentCamera
    if not root or not camera then return end

    local targetPart = (tool:IsA("BasePart") and tool)
        or tool:FindFirstChild("Handle")
        or tool:FindFirstChildWhichIsA("BasePart", true)
        or (tool:IsA("Model") and tool.PrimaryPart)

    if not targetPart then return end

    isFetching = true

    local mainWasVisible = mainFrame.Visible
    local toolWasVisible = toolFrame.Visible
    mainFrame.Visible = false
    toolFrame.Visible = false

    task.spawn(function()
        local oldCFrame = root.CFrame
        local oldCamCFrame = camera.CFrame
        local oldCamType = camera.CameraType

        pcall(function()
            if firetouchinterest then
                local touchHandle = tool:FindFirstChild("Handle") or targetPart
                firetouchinterest(root, touchHandle, 0)
                task.wait(0.02)
                firetouchinterest(root, touchHandle, 1)
            end

            root.CFrame = targetPart.CFrame
            task.wait(0.08)

            if tool and tool.Parent and tool.Parent ~= char and tool.Parent ~= player:FindFirstChild("Backpack") then
                camera.CameraType = Enum.CameraType.Scriptable

                local camPos = targetPart.Position + Vector3.new(0, 1.2, 1.8)
                camera.CFrame = CFrame.new(camPos, targetPart.Position)
                root.CFrame = CFrame.new(targetPart.Position + Vector3.new(0, 0, 1.2), targetPart.Position)

                task.wait(0.06)

                local cd = tool:FindFirstChildWhichIsA("ClickDetector", true) or targetPart:FindFirstChildWhichIsA("ClickDetector", true)
                if cd and fireclickdetector then
                    fireclickdetector(cd)
                end

                local prompt = tool:FindFirstChildWhichIsA("ProximityPrompt", true) or targetPart:FindFirstChildWhichIsA("ProximityPrompt", true)
                if prompt then
                    if fireproximityprompt then
                        fireproximityprompt(prompt)
                    else
                        prompt:InputHoldBegin()
                        task.wait(prompt.HoldDuration > 0 and prompt.HoldDuration or 0.1)
                        prompt:InputHoldEnd()
                    end
                end

                local viewportSize = camera.ViewportSize
                local centerX, centerY = viewportSize.X / 2, viewportSize.Y / 2

                VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, true, nil, 0)
                task.wait(0.02)
                VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, false, nil, 0)

                if mouse1click then mouse1click() end

                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, nil)
                task.wait(0.02)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, nil)

                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.F, false, nil)
                task.wait(0.02)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.F, false, nil)
            end

            task.wait(0.08)
        end)

        local curChar = player.Character
        local curRoot = curChar and curChar:FindFirstChild("HumanoidRootPart")
        if curRoot then
            curRoot.CFrame = oldCFrame
        end

        camera.CFrame = oldCamCFrame
        camera.CameraType = oldCamType

        if mainFrame.Parent then mainFrame.Visible = mainWasVisible end
        if toolFrame.Parent then toolFrame.Visible = toolWasVisible end
        isFetching = false
    end)
end

local currentSearchQuery = ""

local function refreshToolList()
    for _, child in ipairs(mainScroll:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then
        searchStatus.Text = "Найдено: 0"
        return
    end

    overlapParams.FilterDescendantsInstances = {char}

    local parts = Workspace:GetPartBoundsInRadius(root.Position, searchRadius, overlapParams)
    local foundTools = {}
    local list = {}

    for _, part in ipairs(parts) do
        local tool = getValidTool(part)
        if tool and not foundTools[tool] then
            local targetPart = tool:FindFirstChild("Handle") or tool:FindFirstChildWhichIsA("BasePart", true) or part
            if targetPart then
                foundTools[tool] = true

                local dist = math.floor((targetPart.Position - root.Position).Magnitude)
                local rusName = translateItemName(tool.Name)

                if currentSearchQuery == "" or string.find(string.lower(rusName), currentSearchQuery, 1, true) or string.find(string.lower(tool.Name), currentSearchQuery, 1, true) then
                    table.insert(list, {
                        Tool = tool,
                        Name = rusName,
                        Distance = dist,
                        Category = getItemCategory(tool.Name)
                    })
                end
            end
        end
    end

    table.sort(list, function(a, b)
        return a.Distance < b.Distance
    end)

    for _, item in ipairs(list) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -6, 0, 26)
        btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        btn.TextColor3 = Color3.fromRGB(240, 240, 240)
        btn.Font = Enum.Font.SourceSans
        btn.TextSize = 12
        btn.TextXAlignment = Enum.TextXAlignment.Left
        btn.Text = string.format("  [%s] %s — %dm", item.Category, item.Name, item.Distance)
        btn.Parent = mainScroll

        btn.MouseButton1Click:Connect(function()
            fetchTool(item.Tool)
            task.wait(0.2)
            refreshToolList()
        end)
    end

    searchStatus.Text = "Найдено: " .. #list .. " (радиус " .. searchRadius .. "м)"

    task.defer(function()
        mainScroll.CanvasSize = UDim2.new(0, 0, 0, mainLayout.AbsoluteContentSize.Y)
    end)
end

searchBox:GetPropertyChangedSignal("Text"):Connect(function()
    currentSearchQuery = string.lower(searchBox.Text)
    refreshToolList()
end)

btnGetTool.MouseButton1Click:Connect(function()
    toolFrame.Visible = not toolFrame.Visible
    if toolFrame.Visible then
        refreshToolList()
    end
end)

refreshBtn.MouseButton1Click:Connect(refreshToolList)

local btnCollectGadgets = Instance.new("TextButton")
btnCollectGadgets.Size = UDim2.new(0, 190, 0, 30)
btnCollectGadgets.BackgroundColor3 = Color3.fromRGB(110, 70, 110)
btnCollectGadgets.TextColor3 = Color3.fromRGB(255, 255, 255)
btnCollectGadgets.Font = Enum.Font.SourceSansBold
btnCollectGadgets.TextSize = 12
btnCollectGadgets.Text = "Собрать тел./планш. (до 5 кг)"
btnCollectGadgets.LayoutOrder = 8
btnCollectGadgets.Parent = mainFrame

local btnCoord = Instance.new("TextButton")
btnCoord.Size = UDim2.new(0, 190, 0, 30)
btnCoord.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
btnCoord.TextColor3 = Color3.fromRGB(220, 220, 220)
btnCoord.Font = Enum.Font.SourceSansBold
btnCoord.TextSize = 11
btnCoord.Text = "Коорд: 0, 0, 0"
btnCoord.LayoutOrder = 9
btnCoord.Parent = mainFrame

local btnTpToggle = Instance.new("TextButton")
btnTpToggle.Size = UDim2.new(0, 190, 0, 30)
btnTpToggle.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnTpToggle.TextColor3 = Color3.fromRGB(200, 200, 200)
btnTpToggle.Font = Enum.Font.SourceSansBold
btnTpToggle.TextSize = 12
btnTpToggle.Text = "ТП после сбора: [ OFF ]"
btnTpToggle.LayoutOrder = 10
btnTpToggle.Parent = mainFrame

btnTpToggle.MouseButton1Click:Connect(function()
    tpAfterGatherEnabled = not tpAfterGatherEnabled
    if tpAfterGatherEnabled then
        btnTpToggle.Text = "ТП после сбора: [ ON ]"
        btnTpToggle.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnTpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        btnTpToggle.Text = "ТП после сбора: [ OFF ]"
        btnTpToggle.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnTpToggle.TextColor3 = Color3.fromRGB(200, 200, 200)
    end
end)

btnCoord.MouseButton1Click:Connect(function()
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        local pos = root.Position
        local coordStr = string.format("%.1f, %.1f, %.1f", pos.X, pos.Y, pos.Z)
        pcall(function()
            if setclipboard then
                setclipboard(coordStr)
            elseif toclipboard then
                toclipboard(coordStr)
            end
        end)
    end
end)

task.spawn(function()
    while not isUnloaded do
        local char = player.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if root then
            local pos = root.Position
            btnCoord.Text = string.format("Коорд: %.1f, %.1f, %.1f", pos.X, pos.Y, pos.Z)
        else
            btnCoord.Text = "Коорд: Н/Д"
        end
        task.wait(0.2)
    end
end)

local function getGadgetWeight(toolName)
    local lower = string.lower(toolName)
    if lower:find("сломанный телефон") or lower:find("broken phone") then
        return 0.25
    elseif lower:find("сломанный планшет") or lower:find("broken tablet") then
        return 0.5
    end
    return nil
end

btnCollectGadgets.MouseButton1Click:Connect(function()
    if isUnloaded or isFetching then return end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    overlapParams.FilterDescendantsInstances = {char}

    local parts = Workspace:GetPartBoundsInRadius(root.Position, searchRadius, overlapParams)
    local gadgetList = {}
    local visited = {}

    for _, part in ipairs(parts) do
        local tool = getValidTool(part)
        if tool and not visited[tool] then
            visited[tool] = true
            local weight = getGadgetWeight(tool.Name)
            if weight then
                table.insert(gadgetList, {Tool = tool, Weight = weight})
            end
        end
    end

    task.spawn(function()
        local currentWeight = 0.0
        local maxWeight = 5.0

        for _, gadget in ipairs(gadgetList) do
            if isUnloaded then break end
            if currentWeight + gadget.Weight > maxWeight + 0.001 then
                break
            end

            if gadget.Tool and gadget.Tool.Parent then
                fetchTool(gadget.Tool)
                task.wait(0.1)

                local timeout = 0
                while isFetching and timeout < 3 and not isUnloaded do
                    task.wait(0.1)
                    timeout = timeout + 0.1
                end

                currentWeight = currentWeight + gadget.Weight
                task.wait(0.4)
            end
        end

        if tpAfterGatherEnabled and customTpCoord and not isUnloaded then
            local charNow = player.Character
            local rootNow = charNow and charNow:FindFirstChild("HumanoidRootPart")
            if rootNow then
                rootNow.CFrame = CFrame.new(customTpCoord)
            end
        end
    end)
end)

local btnSavePoints = Instance.new("TextButton")
btnSavePoints.Size = UDim2.new(0, 190, 0, 30)
btnSavePoints.BackgroundColor3 = Color3.fromRGB(70, 90, 110)
btnSavePoints.TextColor3 = Color3.fromRGB(255, 255, 255)
btnSavePoints.Font = Enum.Font.SourceSansBold
btnSavePoints.TextSize = 13
btnSavePoints.Text = "Save Points (Точки)"
btnSavePoints.LayoutOrder = 11
btnSavePoints.Parent = mainFrame

local savePointsFrame = Instance.new("Frame")
savePointsFrame.Name = "SavePointsFrame"
savePointsFrame.Size = UDim2.new(0, 420, 0, 320)
savePointsFrame.Position = UDim2.new(0.5, -210, 0.5, -160)
savePointsFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
savePointsFrame.BorderSizePixel = 1
savePointsFrame.BorderColor3 = Color3.fromRGB(20, 20, 20)
savePointsFrame.Visible = false
savePointsFrame.Active = true
savePointsFrame.Draggable = true
savePointsFrame.Parent = screenGui

local savePointsTitle = Instance.new("TextLabel")
savePointsTitle.Size = UDim2.new(1, 0, 0, 25)
savePointsTitle.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
savePointsTitle.Text = "Точки сохранения / Save Points"
savePointsTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
savePointsTitle.Font = Enum.Font.SourceSansBold
savePointsTitle.TextSize = 13
savePointsTitle.Parent = savePointsFrame

local pointNameInput = Instance.new("TextBox")
pointNameInput.Size = UDim2.new(0, 240, 0, 26)
pointNameInput.Position = UDim2.new(0, 10, 0, 32)
pointNameInput.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
pointNameInput.TextColor3 = Color3.fromRGB(255, 255, 255)
pointNameInput.PlaceholderText = "Введите название точки..."
pointNameInput.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
pointNameInput.Font = Enum.Font.SourceSans
pointNameInput.TextSize = 12
pointNameInput.Parent = savePointsFrame

local btnAddPoint = Instance.new("TextButton")
btnAddPoint.Size = UDim2.new(0, 150, 0, 26)
btnAddPoint.Position = UDim2.new(0, 260, 0, 32)
btnAddPoint.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
btnAddPoint.TextColor3 = Color3.fromRGB(255, 255, 255)
btnAddPoint.Font = Enum.Font.SourceSansBold
btnAddPoint.TextSize = 12
btnAddPoint.Text = "Сохранить точку"
btnAddPoint.Parent = savePointsFrame

local spStatusLabel = Instance.new("TextLabel")
spStatusLabel.Size = UDim2.new(1, -20, 0, 18)
spStatusLabel.Position = UDim2.new(0, 10, 0, 60)
spStatusLabel.BackgroundTransparency = 1
spStatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
spStatusLabel.Font = Enum.Font.SourceSansBold
spStatusLabel.TextSize = 12
spStatusLabel.Text = ""
spStatusLabel.Parent = savePointsFrame

local pointsScroll = Instance.new("ScrollingFrame")
pointsScroll.Size = UDim2.new(1, -20, 1, -88)
pointsScroll.Position = UDim2.new(0, 10, 0, 80)
pointsScroll.BackgroundTransparency = 1
pointsScroll.ScrollBarThickness = 4
pointsScroll.Parent = savePointsFrame

local pointsLayout = Instance.new("UIListLayout")
pointsLayout.Parent = pointsScroll
pointsLayout.SortOrder = Enum.SortOrder.LayoutOrder
pointsLayout.Padding = UDim.new(0, 4)

local pointContextMenu = Instance.new("Frame")
pointContextMenu.Name = "PointContextMenu"
pointContextMenu.Size = UDim2.new(0, 400, 0, 130)
pointContextMenu.Position = UDim2.new(0.5, -200, 0.5, -65)
pointContextMenu.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
pointContextMenu.BorderSizePixel = 2
pointContextMenu.BorderColor3 = Color3.fromRGB(80, 80, 80)
pointContextMenu.Visible = false
pointContextMenu.Active = true
pointContextMenu.Draggable = true
pointContextMenu.Parent = screenGui

local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, -25, 0, 22)
menuTitle.Position = UDim2.new(0, 8, 0, 2)
menuTitle.BackgroundTransparency = 1
menuTitle.Text = "Меню точки / Point Menu"
menuTitle.TextColor3 = Color3.fromRGB(100, 200, 255)
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.TextSize = 12
menuTitle.TextXAlignment = Enum.TextXAlignment.Left
menuTitle.Parent = pointContextMenu

local closeMenuBtn = Instance.new("TextButton")
closeMenuBtn.Size = UDim2.new(0, 20, 0, 20)
closeMenuBtn.Position = UDim2.new(1, -22, 0, 2)
closeMenuBtn.BackgroundColor3 = Color3.fromRGB(150, 40, 40)
closeMenuBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeMenuBtn.Text = "X"
closeMenuBtn.Font = Enum.Font.SourceSansBold
closeMenuBtn.TextSize = 11
closeMenuBtn.Parent = pointContextMenu

closeMenuBtn.MouseButton1Click:Connect(function()
    pointContextMenu.Visible = false
end)

local infoText = Instance.new("TextLabel")
infoText.Size = UDim2.new(0, 220, 0, 65)
infoText.Position = UDim2.new(0, 8, 0, 26)
infoText.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
infoText.TextColor3 = Color3.fromRGB(230, 230, 230)
infoText.Font = Enum.Font.SourceSans
infoText.TextSize = 12
infoText.TextWrapped = true
infoText.TextYAlignment = Enum.TextYAlignment.Top
infoText.TextXAlignment = Enum.TextXAlignment.Left
infoText.Parent = pointContextMenu

local editBox = Instance.new("TextBox")
editBox.Size = UDim2.new(0, 220, 0, 24)
editBox.Position = UDim2.new(0, 8, 0, 96)
editBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
editBox.TextColor3 = Color3.fromRGB(255, 255, 255)
editBox.PlaceholderText = "Введите новое имя и Enter..."
editBox.Font = Enum.Font.SourceSans
editBox.TextSize = 11
editBox.Visible = false
editBox.Parent = pointContextMenu

local btnRightFrame = Instance.new("Frame")
btnRightFrame.Size = UDim2.new(0, 155, 0, 95)
btnRightFrame.Position = UDim2.new(0, 235, 0, 26)
btnRightFrame.BackgroundTransparency = 1
btnRightFrame.Parent = pointContextMenu

local btnRightLayout = Instance.new("UIListLayout")
btnRightLayout.Parent = btnRightFrame
btnRightLayout.SortOrder = Enum.SortOrder.LayoutOrder
btnRightLayout.Padding = UDim.new(0, 3)

local btnUpdate = Instance.new("TextButton")
btnUpdate.Size = UDim2.new(1, 0, 0, 21)
btnUpdate.BackgroundColor3 = Color3.fromRGB(60, 90, 130)
btnUpdate.TextColor3 = Color3.fromRGB(255, 255, 255)
btnUpdate.Font = Enum.Font.SourceSansBold
btnUpdate.TextSize = 11
btnUpdate.Text = "Update"
btnUpdate.LayoutOrder = 1
btnUpdate.Parent = btnRightFrame

local btnEditName = Instance.new("TextButton")
btnEditName.Size = UDim2.new(1, 0, 0, 21)
btnEditName.BackgroundColor3 = Color3.fromRGB(110, 90, 50)
btnEditName.TextColor3 = Color3.fromRGB(255, 255, 255)
btnEditName.Font = Enum.Font.SourceSansBold
btnEditName.TextSize = 11
btnEditName.Text = "Edit Name"
btnEditName.LayoutOrder = 2
btnEditName.Parent = btnRightFrame

local btnDelete = Instance.new("TextButton")
btnDelete.Size = UDim2.new(1, 0, 0, 21)
btnDelete.BackgroundColor3 = Color3.fromRGB(130, 50, 50)
btnDelete.TextColor3 = Color3.fromRGB(255, 255, 255)
btnDelete.Font = Enum.Font.SourceSansBold
btnDelete.TextSize = 11
btnDelete.Text = "Delete"
btnDelete.LayoutOrder = 3
btnDelete.Parent = btnRightFrame

local btnTeleport = Instance.new("TextButton")
btnTeleport.Size = UDim2.new(1, 0, 0, 21)
btnTeleport.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
btnTeleport.TextColor3 = Color3.fromRGB(255, 255, 255)
btnTeleport.Font = Enum.Font.SourceSansBold
btnTeleport.TextSize = 11
btnTeleport.Text = "Teleport"
btnTeleport.LayoutOrder = 4
btnTeleport.Parent = btnRightFrame

local function showSpStatus(msg, color)
    spStatusLabel.TextColor3 = color or Color3.fromRGB(255, 100, 100)
    spStatusLabel.Text = msg
    task.spawn(function()
        task.wait(3)
        if spStatusLabel.Text == msg then
            spStatusLabel.Text = ""
        end
    end)
end

local openContextMenu

local function refreshPointsList()
    for _, child in ipairs(pointsScroll:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    for _, pt in ipairs(savedPoints) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -6, 0, 26)
        btn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
        btn.TextColor3 = Color3.fromRGB(240, 240, 240)
        btn.Font = Enum.Font.SourceSansBold
        btn.TextSize = 12
        btn.Text = "📍 " .. pt.Name .. " [ПКМ / Right Click]"
        btn.Parent = pointsScroll

        btn.MouseButton1Click:Connect(function()
            openContextMenu(pt)
        end)

        btn.MouseButton2Click:Connect(function()
            openContextMenu(pt)
        end)
    end

    task.defer(function()
        pointsScroll.CanvasSize = UDim2.new(0, 0, 0, pointsLayout.AbsoluteContentSize.Y)
    end)
end

openContextMenu = function(pt)
    if not pt then return end
    selectedPoint = pt

    infoText.Text = string.format(" Имя: %s\n\n Координаты:\n X: %.1f | Y: %.1f | Z: %.1f",
        pt.Name, pt.Position.X, pt.Position.Y, pt.Position.Z)

    editBox.Visible = false
    editBox.Text = ""
    pointContextMenu.Visible = true
end

btnAddPoint.MouseButton1Click:Connect(function()
    local name = pointNameInput.Text:gsub("^%s*(.-)%s*$", "%1")

    if name == "" then
        showSpStatus("Ошибка: Укажите название точки!", Color3.fromRGB(255, 100, 100))
        return
    end

    for _, pt in ipairs(savedPoints) do
        if string.lower(pt.Name) == string.lower(name) then
            showSpStatus("Ошибка: Точка с таким именем уже есть!", Color3.fromRGB(255, 100, 100))
            return
        end
    end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then
        showSpStatus("Ошибка: Персонаж не найден!", Color3.fromRGB(255, 100, 100))
        return
    end

    table.insert(savedPoints, {
        Name = name,
        Position = root.Position
    })

    pointNameInput.Text = ""
    showSpStatus("Точка '" .. name .. "' сохранена!", Color3.fromRGB(100, 255, 100))
    refreshPointsList()
end)

btnUpdate.MouseButton1Click:Connect(function()
    if not selectedPoint then return end
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        selectedPoint.Position = root.Position
        openContextMenu(selectedPoint)
        showSpStatus("Координаты точки обновлены!", Color3.fromRGB(100, 255, 100))
    end
end)

local function processRename()
    if not selectedPoint then return end
    local newName = editBox.Text:gsub("^%s*(.-)%s*$", "%1")

    if newName == "" then
        showSpStatus("Имя не может быть пустым!", Color3.fromRGB(255, 100, 100))
        return
    end

    for _, pt in ipairs(savedPoints) do
        if pt ~= selectedPoint and string.lower(pt.Name) == string.lower(newName) then
            showSpStatus("Точка с таким именем уже есть!", Color3.fromRGB(255, 100, 100))
            return
        end
    end

    selectedPoint.Name = newName
    editBox.Visible = false
    refreshPointsList()
    openContextMenu(selectedPoint)
    showSpStatus("Имя точки успешно изменено!", Color3.fromRGB(100, 255, 100))
end

btnEditName.MouseButton1Click:Connect(function()
    if not selectedPoint then return end
    if not editBox.Visible then
        editBox.Visible = true
        editBox.Text = selectedPoint.Name
        editBox:CaptureFocus()
    else
        processRename()
    end
end)

editBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        processRename()
    end
end)

btnDelete.MouseButton1Click:Connect(function()
    if not selectedPoint then return end
    local ptName = selectedPoint.Name
    for idx, pt in ipairs(savedPoints) do
        if pt == selectedPoint then
            table.remove(savedPoints, idx)
            break
        end
    end
    selectedPoint = nil
    pointContextMenu.Visible = false
    refreshPointsList()
    showSpStatus("Точка '" .. ptName .. "' удалена.", Color3.fromRGB(255, 200, 100))
end)

btnTeleport.MouseButton1Click:Connect(function()
    if not selectedPoint then return end

    local now = tick()
    local elapsed = now - lastTeleportTime
    if elapsed < 5 then
        local remaining = math.ceil(5 - elapsed)
        showSpStatus("КД телепорта: подождите " .. remaining .. " сек.", Color3.fromRGB(255, 200, 80))
        return
    end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        lastTeleportTime = now
        root.CFrame = CFrame.new(selectedPoint.Position)
        showSpStatus("ТП на точку '" .. selectedPoint.Name .. "'!", Color3.fromRGB(100, 255, 100))
    end
end)

btnSavePoints.MouseButton1Click:Connect(function()
    savePointsFrame.Visible = not savePointsFrame.Visible
end)

local safeTpEnabled = false
local safeTpPoint = nil
local safeTpCooldown = 0

local btnSafeTpToggle = Instance.new("TextButton")
btnSafeTpToggle.Size = UDim2.new(0, 190, 0, 30)
btnSafeTpToggle.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
btnSafeTpToggle.TextColor3 = Color3.fromRGB(200, 200, 200)
btnSafeTpToggle.Font = Enum.Font.SourceSansBold
btnSafeTpToggle.TextSize = 12
btnSafeTpToggle.Text = "SafeTP: [ OFF ]"
btnSafeTpToggle.LayoutOrder = 12
btnSafeTpToggle.Parent = mainFrame

btnSafeTpToggle.MouseButton1Click:Connect(function()
    if isUnloaded then return end
    safeTpEnabled = not safeTpEnabled
    if safeTpEnabled then
        btnSafeTpToggle.Text = "SafeTP: [ ON ]"
        btnSafeTpToggle.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        btnSafeTpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        btnSafeTpToggle.Text = "SafeTP: [ OFF ]"
        btnSafeTpToggle.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
        btnSafeTpToggle.TextColor3 = Color3.fromRGB(200, 200, 200)
    end
end)

local btnSafeTpInfo = Instance.new("TextButton")
btnSafeTpInfo.Size = UDim2.new(0, 190, 0, 26)
btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(60, 80, 100)
btnSafeTpInfo.TextColor3 = Color3.fromRGB(230, 230, 230)
btnSafeTpInfo.Font = Enum.Font.SourceSansBold
btnSafeTpInfo.TextSize = 11
btnSafeTpInfo.Text = "SafeTP: Z=save / X=tp"
btnSafeTpInfo.LayoutOrder = 13
btnSafeTpInfo.Parent = mainFrame

local function updateSafeTpBtn()
    if safeTpPoint then
        btnSafeTpInfo.Text = string.format("SafeTP: %.0f,%.0f,%.0f", safeTpPoint.X, safeTpPoint.Y, safeTpPoint.Z)
    else
        btnSafeTpInfo.Text = "SafeTP: Z=save / X=tp"
    end
end

btnSafeTpInfo.MouseButton1Click:Connect(function()
    if not safeTpEnabled then return end
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        safeTpPoint = root.Position
        updateSafeTpBtn()
        btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        task.delay(0.5, function()
            if btnSafeTpInfo then btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(60, 80, 100) end
        end)
    end
end)

local function doSafeTeleport()
    if not safeTpEnabled then return end
    if not safeTpPoint then return end

    local isTabHeld = UserInputService:IsKeyDown(Enum.KeyCode.Tab)
    local isRHeld = UserInputService:IsKeyDown(Enum.KeyCode.R)
    if isTabHeld or isRHeld then return end

    local now = tick()
    if now - safeTpCooldown < 2 then return end
    safeTpCooldown = now

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        root.CFrame = CFrame.new(safeTpPoint)
        btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        task.delay(0.3, function()
            if btnSafeTpInfo then btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(60, 80, 100) end
        end)
    end
end

local function doSafeSave()
    if not safeTpEnabled then return end
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        safeTpPoint = root.Position
        updateSafeTpBtn()
        btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
        task.delay(0.3, function()
            if btnSafeTpInfo then btnSafeTpInfo.BackgroundColor3 = Color3.fromRGB(60, 80, 100) end
        end)
    end
end

local unloadButton = Instance.new("TextButton")
unloadButton.Name = "UnloadButton"
unloadButton.Size = UDim2.new(0, 190, 0, 26)
unloadButton.BackgroundColor3 = Color3.fromRGB(120, 40, 40)
unloadButton.TextColor3 = Color3.fromRGB(255, 255, 255)
unloadButton.Font = Enum.Font.SourceSansBold
unloadButton.TextSize = 13
unloadButton.Text = "Unload Script"
unloadButton.LayoutOrder = 14
unloadButton.Parent = mainFrame

unloadButton.MouseButton1Click:Connect(function()
    isUnloaded = true
    espEnabled = false
    itemEspEnabled = false
    camNoclipEnabled = false
    fullbrightEnabled = false
    maxZoomEnabled = false

    player.DevCameraOcclusionMode = origOcclusion
    player.CameraMaxZoomDistance = origMaxZoom
    player.CameraMinZoomDistance = origMinZoom

    if uisConn then uisConn:Disconnect() uisConn = nil end
    if camNoclipConn then camNoclipConn:Disconnect() camNoclipConn = nil end
    if fullbrightConn then fullbrightConn:Disconnect() fullbrightConn = nil end
    if maxZoomConn then maxZoomConn:Disconnect() maxZoomConn = nil end

    Lighting.Brightness = origBrightness
    Lighting.GlobalShadows = origShadows
    Lighting.OutdoorAmbient = origOutdoorAmbient
    Lighting.Ambient = origAmbient

    for _, bb in pairs(activeNpcEsps) do if bb then bb:Destroy() end end
    for _, bb in pairs(activeItemEsps) do if bb then bb:Destroy() end end

    if pointContextMenu then pointContextMenu:Destroy() end
    if savePointsFrame then savePointsFrame:Destroy() end

    screenGui:Destroy()
end)

uisConn = UserInputService.InputBegan:Connect(function(input, gpe)
    if isUnloaded then return end
    if gpe then return end

    if input.KeyCode == Enum.KeyCode.RightShift and mainFrame.Parent then
        mainFrame.Visible = not mainFrame.Visible
        toolFrame.Visible = false
        savePointsFrame.Visible = false
        pointContextMenu.Visible = false
    elseif input.KeyCode == Enum.KeyCode.Z then
        doSafeSave()
    elseif input.KeyCode == Enum.KeyCode.X then
        doSafeTeleport()
    end
end)
]==]

-- ==================== ЗАПУСК В ИЗОЛИРОВАННЫХ СРЕДАХ ====================
local function runIsolated(code, name)
    local env = setmetatable({}, {__index = _G})
    local func = loadstring or load
    local chunk = func(code, name)
    if not chunk then return end
    setfenv(chunk, env)
    local ok, err = pcall(chunk)
    if not ok then
        warn("[" .. name .. "] Ошибка: " .. tostring(err))
    end
end

runIsolated(SCRIPT_1, "ExtendedScanner")
runIsolated(SCRIPT_2, "UnifiedCityMenu")
