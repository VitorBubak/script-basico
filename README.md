-- Cliente: detecta clique/touch e pede ao servidor para iniciar a missão.
-- Coloque este LocalScript em StarterPlayerScripts.

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- RemoteEvents (o servidor cria se não existir)
local StartEvent = ReplicatedStorage:WaitForChild("StartMission")
local CompleteEvent = ReplicatedStorage:WaitForChild("MissionComplete")

local busy = false

-- Enviasse pedido ao servidor ao clicar (Button1Down funciona no PC; funciona também em mobiles em muitos casos)
mouse.Button1Down:Connect(function()
	if busy then return end
	busy = true
	StartEvent:FireServer()
end)

-- Recebe notificação de resultado (inicia/desbloqueia clique novamente)
CompleteEvent.OnClientEvent:Connect(function(success, message)
	busy = false
	message = message or (success and "Missão concluída!" or "Falha/sem NPCs.")
	-- Notificação simples (funciona se StarterGui:SetCore estiver disponível)
	pcall(function()
		game.StarterGui:SetCore("SendNotification", {
			Title = "Missão",
			Text = message,
			Duration = 3
		})
	end)
end)
