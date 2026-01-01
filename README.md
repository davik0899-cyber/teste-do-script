local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local nodes = {}

script.Parent.MouseButton1Click:Connect(function()
	local pos = mouse.Hit.Position
	
	local ball = Instance.new("Part")
	ball.Shape = Enum.PartType.Ball
	ball.Size = Vector3.new(1,1,1)
	ball.Position = pos + Vector3.new(0,0.5,0)
	ball.Anchored = true
	ball.CanCollide = false
	ball.Parent = workspace
	
	table.insert(nodes, ball)

	-- cria linha se houver outra bolinha antes
	if #nodes > 1 then
		local a = nodes[#nodes-1]
		local b = nodes[#nodes]

		local beam = Instance.new("Beam")
		local att0 = Instance.new("Attachment", a)
		local att1 = Instance.new("Attachment", b)

		beam.Attachment0 = att0
		beam.Attachment1 = att1
		beam.Width0 = 0.2
		beam.Width1 = 0.2
		beam.Parent = a
	end
end)

GoldOre:SetAttribute("Value", 180)

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoid = char:WaitForChild("Humanoid")
local root = char:WaitForChild("HumanoidRootPart")

local ore = workspace:WaitForChild("GoldOre")

script.Parent.MouseButton1Click:Connect(function()
	-- andar até o minério
	humanoid:MoveTo(ore.Position)
	humanoid.MoveToFinished:Wait()

	-- simular mineração
	wait(2)

	-- vender minério
	game.ReplicatedStorage.SellOre:FireServer(ore)
end)

local event = Instance.new("RemoteEvent")
event.Name = "SellOre"
event.Parent = game.ReplicatedStorage

event.OnServerEvent:Connect(function(player, ore)
	if ore and ore:GetAttribute("Value") then
		local leaderstats = player:FindFirstChild("leaderstats")
		if leaderstats then
			leaderstats.Money.Value += ore:GetAttribute("Value")
			ore:Destroy()
		end
	end
end)

game.Players.PlayerAdded:Connect(function(player)
	local stats = Instance.new("Folder", player)
	stats.Name = "leaderstats"

	local money = Instance.new("IntValue", stats)
	money.Name = "Money"
	money.Value = 0
end)
