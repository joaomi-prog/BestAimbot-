local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui")
gui.Parent = player:WaitForChild("PlayerGui")
gui.ResetOnSpawn = false

-- BOTÃO TOGGLE
local toggle = Instance.new("TextButton")
toggle.Size = UDim2.new(0,120,0,40)
toggle.Position = UDim2.new(0,10,0,10)
toggle.Text = "Abrir"
toggle.Parent = gui

-- PAINEL
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,200,0,230)
frame.Position = UDim2.new(0,10,0,60)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.Visible = false
frame.Parent = gui

-- ESTADOS
local nomeOn = false
local distOn = false
local boxOn = false
local invisOn = false

-- TOGGLE MENU
toggle.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
	toggle.Text = frame.Visible and "Fechar" or "Abrir"
end)

-- FUNÇÃO BOTÃO
local function criar(texto, y, callback)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1,-20,0,40)
	b.Position = UDim2.new(0,10,0,y)
	b.Text = texto.." OFF"
	b.Parent = frame
	
	b.BackgroundColor3 = Color3.fromRGB(60,60,60)
	b.TextColor3 = Color3.new(1,1,1)

	local estado = false

	b.MouseButton1Click:Connect(function()
		estado = not estado
		b.Text = texto.." "..(estado and "ON" or "OFF")
		callback(estado)
	end)
end

-- BOTÕES
criar("Nome",10,function(v) nomeOn = v end)
criar("Distancia",60,function(v) distOn = v end)
criar("Caixa",110,function(v) boxOn = v end)
criar("Invisivel",160,function(v) invisOn = v end)

-- FUNÇÃO INVISIBILIDADE
local function setInvisible(state)
	local char = player.Character
	if not char then return end

	for _, obj in pairs(char:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.Transparency = state and 1 or 0
			obj.CanCollide = not state
		elseif obj:IsA("Decal") then
			obj.Transparency = state and 1 or 0
		end
	end
end

-- LOOP PRINCIPAL
RunService.RenderStepped:Connect(function()

	-- INVISIBILIDADE
	setInvisible(invisOn)

	-- ESP
	for _,p in pairs(Players:GetPlayers()) do
		if p ~= player then
			
			local char = p.Character
			if not char then continue end
			
			local head = char:FindFirstChild("Head")
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if not head or not hrp then continue end
			
			-- TEXTO
			local esp = head:FindFirstChild("ESP")
			
			if nomeOn or distOn then
				
				if not esp then
					esp = Instance.new("BillboardGui")
					esp.Name = "ESP"
					esp.Size = UDim2.new(0,100,0,40)
					esp.StudsOffset = Vector3.new(0,2,0)
					esp.AlwaysOnTop = true
					esp.Parent = head
					
					local txt = Instance.new("TextLabel")
					txt.Name = "TXT"
					txt.Size = UDim2.new(1,0,1,0)
					txt.BackgroundTransparency = 1
					txt.TextColor3 = Color3.new(1,1,1)
					txt.TextSize = 14
					txt.Parent = esp
				end
				
				local txt = esp:FindFirstChild("TXT")
				
				local t = ""
				
				if nomeOn then
					t = p.Name
				end
				
				if distOn then
					local myChar = player.Character
					if myChar and myChar:FindFirstChild("HumanoidRootPart") then
						local d = (myChar.HumanoidRootPart.Position - hrp.Position).Magnitude
						
						if t ~= "" then
							t = t.."\n"..math.floor(d)
						else
							t = math.floor(d)
						end
					end
				end
				
				txt.Text = t
				
			else
				if esp then
					esp:Destroy()
				end
			end
			
			-- CAIXA
			local hl = char:FindFirstChild("HL")
			
			if boxOn then
				if not hl then
					hl = Instance.new("Highlight")
					hl.Name = "HL"
					hl.FillColor = Color3.fromRGB(255,0,0)
					hl.FillTransparency = 0.6
					hl.Parent = char
				end
			else
				if hl then
					hl:Destroy()
				end
			end
			
		end
	end
end)# BestAimbot-
