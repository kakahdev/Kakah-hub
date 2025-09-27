-- ==================== TELA DE CARREGAMENTO ====================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KakahHubLoading"
ScreenGui.Parent = playerGui
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false

-- Som de carregamento
local loadingSound = Instance.new("Sound")
loadingSound.SoundId = "rbxassetid://112748273890049"
loadingSound.Volume = 1
loadingSound.Looped = true
loadingSound.Parent = ScreenGui
loadingSound:Play()

-- Fundo preto
local Background = Instance.new("Frame")
Background.Size = UDim2.new(1, 0, 1, 0)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui

-- Quadradinhos vermelhos animados
spawn(function()
	while Background.Parent do
		local square = Instance.new("Frame")
		square.Size = UDim2.new(0, math.random(4,10), 0, math.random(4,10))
		square.Position = UDim2.new(math.random(), 0, math.random(), 0)
		square.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
		square.Parent = Background

		local tween = TweenService:Create(square, TweenInfo.new(3), {
			Position = UDim2.new(square.Position.X.Scale, 0, 1.2, 0),
			BackgroundTransparency = 1
		})
		tween:Play()
		game.Debris:AddItem(square, 3)

		wait(0.2)
	end
end)

-- Painel central
local MainFrame = Instance.new("Frame")
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.Position = UDim2.new(0.5, 0, 0.4, 0)
MainFrame.Size = UDim2.new(0, 460, 0, 200)
MainFrame.BackgroundTransparency = 1
MainFrame.Parent = Background

local UIStroke = Instance.new("UIStroke")
UIStroke.Thickness = 2
UIStroke.Color = Color3.fromRGB(255, 0, 0)
UIStroke.Parent = MainFrame

-- Título
local Title = Instance.new("TextLabel")
Title.Text = "KAKAH HUB"
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 0, 0)
Title.Font = Enum.Font.GothamBlack
Title.TextScaled = true
Title.Parent = MainFrame

-- Subtítulo
local SubTitle = Instance.new("TextLabel")
SubTitle.Text = "para acabar com web namoro "
SubTitle.Size = UDim2.new(1, 0, 0, 30)
SubTitle.Position = UDim2.new(0, 0, 0, 55)
SubTitle.BackgroundTransparency = 1
SubTitle.TextColor3 = Color3.fromRGB(255, 0, 0)
SubTitle.Font = Enum.Font.GothamBold
SubTitle.TextScaled = true
SubTitle.Parent = MainFrame

-- Barra de progresso
local ProgressBarBackground = Instance.new("Frame")
ProgressBarBackground.Size = UDim2.new(0.9, 0, 0, 25)
ProgressBarBackground.Position = UDim2.new(0.05, 0, 0.75, 0)
ProgressBarBackground.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
ProgressBarBackground.Parent = MainFrame

local Corner1 = Instance.new("UICorner")
Corner1.CornerRadius = UDim.new(0, 12)
Corner1.Parent = ProgressBarBackground

local ProgressBar = Instance.new("Frame")
ProgressBar.Size = UDim2.new(0, 0, 1, 0)
ProgressBar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
ProgressBar.Parent = ProgressBarBackground

local Corner2 = Instance.new("UICorner")
Corner2.CornerRadius = UDim.new(0, 12)
Corner2.Parent = ProgressBar

-- Texto da porcentagem
local PercentText = Instance.new("TextLabel")
PercentText.Size = UDim2.new(1, 0, 0, 25)
PercentText.Position = UDim2.new(0, 0, 1, 5)
PercentText.BackgroundTransparency = 1
PercentText.Text = "0%"
PercentText.TextColor3 = Color3.fromRGB(255, 0, 0)
PercentText.Font = Enum.Font.GothamBlack
PercentText.TextScaled = true
PercentText.Parent = MainFrame

-- ==================== SISTEMA DE TAGS ====================
local hubUsers = {} -- Usuários que rodaram o script
local OWNER_USERID = 2487210970 -- seu UserId

local function createTag(player, text)
    if player.Character and player.Character:FindFirstChild("Head") then
        if player.Character:FindFirstChild("KakahHubTag") then
            player.Character.KakahHubTag:Destroy()
        end
        local billboard = Instance.new("BillboardGui")
        billboard.Name = "KakahHubTag"
        billboard.Adornee = player.Character.Head
        billboard.Size = UDim2.new(0,200,0,50)
        billboard.StudsOffset = Vector3.new(0,3,0)
        billboard.AlwaysOnTop = true
        billboard.Parent = player.Character.Head

        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1,0,1,0)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.fromRGB(255,0,0)
        label.TextStrokeTransparency = 0
        label.Font = Enum.Font.GothamBold
        label.TextScaled = true
        label.Text = text
        label.Parent = billboard
    end
end

local function markAsHubUser(player)
    hubUsers[player.UserId] = true
    if player.Character then
        createTag(player, "Usuário Kakah Hub")
    end
end

local function setOwnerOrUserTag()
    if player.UserId == OWNER_USERID then
        createTag(player, "Dono Kakah Hub")
    else
        markAsHubUser(player)
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function(char)
        wait(1)
        if plr.UserId == OWNER_USERID then
            createTag(plr, "Dono Kakah Hub")
        elseif hubUsers[plr.UserId] then
            createTag(plr, "Usuário Kakah Hub")
        end
    end)
end)

-- ==================== CARREGAMENTO ====================
spawn(function()
	for i = 1, 100 do
		ProgressBar.Size = UDim2.new(i/100, 0, 1, 0)
		PercentText.Text = i .. "%"
		wait(0.05)
	end

	loadingSound:Stop()
	wait(0.5)
	ScreenGui:Destroy()

	-- ==================== SEU HUB (Redzlib) ====================
	local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()
	local Window = redzlib:MakeWindow({
		Title = "kakah Hub | Brookhaven 1.1",
		SubTitle = "by kakah",
		SaveFolder = "Versão 1.1| kakah hub"
	})

	Window:AddMinimizeButton({
		Button = { Image = "rbxassetid://88478585503921", BackgroundTransparency = 0 },
		Corner = { CornerRadius = UDim.new(35, 1) },
	})

	-- Abas principais
	local MainTab = Window:MakeTab({"Créditos", "rbxassetid://"})
	local FunTab = Window:MakeTab({"Fun", "rbxassetid://"})
	local AvatarTap  = Window:MakeTab({"Avatar", "rbxassetid://"})
	local HouseTab = Window:MakeTab({"House","rbxassetid://l"})
	local CarTab = Window:MakeTab({"Car", "rbxassetid://l"})
	local RaibowTab = Window:MakeTab({"Raibow", "rbxassetid://"})
	local MusicAllTab = Window:MakeTab({"Music All", "rbxassetid://"})
	local MusicTab = Window:MakeTab({"Music", "rbxassetid://"})
	local TrollTab = Window:MakeTab({ Title = "Scripts Trolls", Icon = "rbxassetid://" })
	local SpamTab = Window:MakeTab({"Spam", "rbxassetid://l"})
	local PremiumTab = Window:MakeTab({"Premium", "rbxassetid://"})
	local AdminTab = Window:MakeTab({"Admin", "rbxassetid://"})

	Window:SelectTab(MainTab)

	-- Conteúdo da MainTab
	MainTab:AddSection({ "kakah hub em processo..." })
	MainTab:AddParagraph({"Criadores", "By kakah"})
	MainTab:AddParagraph({"crédito", "ninja"})
	MainTab:AddParagraph({"Executor", "Delta"})
	MainTab:AddDiscordInvite({
		Name = "grupo do kakah",
		Description = "Join server",
		Logo = "rbxassetid://0000000",
		Invite = "https://chat.whatsapp.com/H2sev6pY7TyBbCNPFBcNnx",
	})
end)

-- Rodar tags do dono/usuário
setOwnerOrUserTag()
