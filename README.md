-- Float v6 GUI + Noclip + AntiBack
if game.CoreGui:FindFirstChild("FloatV6GUI") then
	game.CoreGui.FloatV6GUI:Destroy()
end

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")
local hum = char:WaitForChild("Humanoid")
local uis = game:GetService("UserInputService")
local rs = game:GetService("RunService")

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "FloatV6GUI"
gui.ResetOnSpawn = false
gui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,220,0,180)
frame.Position = UDim2.new(0.05,0,0.5,0)
frame.BackgroundColor3 = Color3.fromRGB(35,35,35)
frame.Active = true
frame.Draggable = true
frame.Parent = gui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,12)

-- Title
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-50,0,25)
title.Position = UDim2.new(0,10,0,5)
title.BackgroundTransparency = 1
title.Text = "Float v6"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = frame

-- Close & Mini buttons
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0,25,0,25)
closeBtn.Position = UDim2.new(1,-30,0,5)
closeBtn.BackgroundColor3 = Color3.fromRGB(255,60,60)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 14
closeBtn.Parent = frame
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0,8)

local miniBtn = Instance.new("TextButton")
miniBtn.Size = UDim2.new(0,25,0,25)
miniBtn.Position = UDim2.new(1,-60,0,5)
miniBtn.BackgroundColor3 = Color3.fromRGB(255,170,0)
miniBtn.Text = "-"
miniBtn.TextColor3 = Color3.new(1,1,1)
miniBtn.Font = Enum.Font.GothamBold
miniBtn.TextSize = 20
miniBtn.Parent = frame
Instance.new("UICorner", miniBtn).CornerRadius = UDim.new(0,8)

-- Float Button
local floatBtn = Instance.new("TextButton")
floatBtn.Size = UDim2.new(0,180,0,40)
floatBtn.Position = UDim2.new(0.5,-90,0,40)
floatBtn.BackgroundColor3 = Color3.fromRGB(0,170,255)
floatBtn.Text = "Float: OFF [F]"
floatBtn.TextColor3 = Color3.new(1,1,1)
floatBtn.Font = Enum.Font.GothamBold
floatBtn.TextSize = 16
floatBtn.Parent = frame
Instance.new("UICorner", floatBtn).CornerRadius = UDim.new(0,10)

-- Speed Input
local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(0,180,0,30)
speedBox.Position = UDim2.new(0.5,-90,0,90)
speedBox.PlaceholderText = "Move speed (default = 16)"
speedBox.Text = ""
speedBox.TextColor3 = Color3.fromRGB(255,255,255)
speedBox.BackgroundColor3 = Color3.fromRGB(50,50,50)
speedBox.Font = Enum.Font.Gotham
speedBox.TextSize = 14
speedBox.Parent = frame
Instance.new("UICorner", speedBox).CornerRadius = UDim.new(0,8)

local applyBtn = Instance.new("TextButton")
applyBtn.Size = UDim2.new(0,180,0,30)
applyBtn.Position = UDim2.new(0.5,-90,0,130)
applyBtn.BackgroundColor3 = Color3.fromRGB(0,200,100)
applyBtn.Text = "Apply Speed"
applyBtn.TextColor3 = Color3.new(1,1,1)
applyBtn.Font = Enum.Font.GothamBold
applyBtn.TextSize = 16
applyBtn.Parent = frame
Instance.new("UICorner", applyBtn).CornerRadius = UDim.new(0,8)

-- Logic
local isFloating = false
local floatSpeed = 16
local minimized = false
local oldGravity = workspace.Gravity

-- Noclip
local function noclip()
	for _, part in pairs(char:GetDescendants()) do
		if part:IsA("BasePart") then
			part.CanCollide = false
		end
	end
end

-- Float loop
local function floatLoop()
	rs.Heartbeat:Connect(function()
		if isFloating and hrp and hrp.Parent then
			-- Noclip liên tục
			noclip()
			-- Anti-back: giữ vị trí
			local cf = hrp.CFrame
			-- Di chuyển WASD + Space/Shift
			local moveVec = Vector3.new(0,0,0)
			if uis:IsKeyDown(Enum.KeyCode.W) then moveVec = moveVec + hrp.CFrame.LookVector end
			if uis:IsKeyDown(Enum.KeyCode.S) then moveVec = moveVec - hrp.CFrame.LookVector end
			if uis:IsKeyDown(Enum.KeyCode.A) then moveVec = moveVec - hrp.CFrame.RightVector end
			if uis:IsKeyDown(Enum.KeyCode.D) then moveVec = moveVec + hrp.CFrame.RightVector end
			if uis:IsKeyDown(Enum.KeyCode.Space) then moveVec = moveVec + Vector3.new(0,1,0) end
			if uis:IsKeyDown(Enum.KeyCode.LeftShift) then moveVec = moveVec - Vector3.new(0,1,0) end
			if moveVec.Magnitude > 0 then
				hrp.CFrame = hrp.CFrame + moveVec.Unit * floatSpeed * rs.Heartbeat:Wait()
			else
				hrp.CFrame = cf
			end
		end
	end)
end

floatLoop() -- chạy vòng lặp

local function toggleFloat(state)
	isFloating = state
	if isFloating then
		floatBtn.Text = "Float: ON [F]"
		floatBtn.BackgroundColor3 = Color3.fromRGB(255,100,100)
		oldGravity = workspace.Gravity
		workspace.Gravity = 0
	else
		floatBtn.Text = "Float: OFF [F]"
		floatBtn.BackgroundColor3 = Color3.fromRGB(0,170,255)
		workspace.Gravity = oldGravity
	end
end

floatBtn.MouseButton1Click:Connect(function()
	toggleFloat(not isFloating)
end)

applyBtn.MouseButton1Click:Connect(function()
	local val = tonumber(speedBox.Text)
	if val then floatSpeed = math.clamp(val,1,100) else floatSpeed = 16 end
end)

miniBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	if minimized then
		for _, child in ipairs(frame:GetChildren()) do
			if child ~= title and child ~= miniBtn and child ~= closeBtn then
				child.Visible = false
			end
		end
		frame.Size = UDim2.new(0,220,0,35)
	else
		for _, child in ipairs(frame:GetChildren()) do
			child.Visible = true
		end
		frame.Size = UDim2.new(0,220,0,180)
	end
end)

closeBtn.MouseButton1Click:Connect(function()
	gui:Destroy()
	if isFloating then toggleFloat(false) end
end)

-- Key F
uis.InputBegan:Connect(function(input,gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.F then toggleFloat(not isFloating) end
end)

-- Reset khi chết
player.CharacterAdded:Connect(function(newChar)
	char = newChar
	hrp = newChar:WaitForChild("HumanoidRootPart")
	hum = newChar:WaitForChild("Humanoid")
	if isFloating then toggleFloat(false) end
end)
