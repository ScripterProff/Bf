local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Botão e interface
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "ColetorFrutas"

local mainBtn = Instance.new("TextButton", gui)
mainBtn.Size = UDim2.new(0, 160, 0, 40)
mainBtn.Position = UDim2.new(0, 20, 0.45, 0)
mainBtn.Text = "Ativar Coletor de Frutas"
mainBtn.BackgroundColor3 = Color3.fromRGB(30, 160, 50)
mainBtn.TextColor3 = Color3.new(1, 1, 1)
mainBtn.Font = Enum.Font.SourceSansBold
mainBtn.TextSize = 14

local ativo = false

-- Função mover até fruta
local function moverAte(obj)
	local char = LocalPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return end

	local hrp = char.HumanoidRootPart
	local targetPos = obj.Position

	while ativo and (hrp.Position - targetPos).Magnitude > 5 do
		local dir = (targetPos - hrp.Position).Unit
		hrp.CFrame = hrp.CFrame + dir * 2
		task.wait(0.05)
	end
end

-- Detector de frutas
local function buscarFrutas()
	for _, obj in pairs(Workspace:GetChildren()) do
		if obj:IsA("Tool") and obj:FindFirstChild("Handle") and string.find(obj.Name:lower(), "fruit") then
			return obj.Handle
		end
	end
	return nil
end

-- Loop automático
RunService.RenderStepped:Connect(function()
	if not ativo then return end

	local fruta = buscarFrutas()
	if fruta then
		moverAte(fruta)
	end
end)

-- Botão toggle
mainBtn.MouseButton1Click:Connect(function()
	ativo = not ativo
	mainBtn.Text = ativo and "Desativar Coletor" or "Ativar Coletor de Frutas"
	mainBtn.BackgroundColor3 = ativo and Color3.fromRGB(200, 0, 0) or Color3.fromRGB(30, 160, 50)
end)
