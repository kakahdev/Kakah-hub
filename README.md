if game.placeId == 4924922222 then
local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()
local Window = OrionLib:MakeWindow({Name = "kakah hub | Brookhaven RP | ", HidePremium = false, IntroText = "kakah hub  ", SaveConfig = true, ConfigFolder = "OrionTest"})

local Tab = Window:MakeTab({
	Name = "Informations",
	Icon = "rbxassetid://88478585503921",
	PremiumOnly = false
})
Tab:AddParagraph("bem vindo kakah Hub obrigado por usa aproveita segue no Tik Tok kaykaka2")
Tab:AddParagraph("Update nova versão","|1| bug repair |2| Adding Music id |3| 24 music added |4| added Home and information menu, Teleportation,ESP menu |5| Added view,Goto,Kill,Bring improvements |6| Added secrets to teleportation and much more |7| added more modifications to avatar") 

local Tab = Window:MakeTab({
	Name = "Home",
	Icon = "rbxassetid://88478585503921",
	PremiumOnly = false
})

Tab:AddSection({
    Name = "View/Goto"
})

-- Toggle para a função de visualização
local viewEnabled = false
local selectedViewPlayer = nil
local characterAddedConnection = nil

-- Função para ativar ou desativar a visualização do jogador
local function toggleView(enabled)
    if enabled then
        if selectedViewPlayer then
            local player = selectedViewPlayer
            if player then
                game.Workspace.CurrentCamera.CameraSubject = player.Character
                if characterAddedConnection then
                    characterAddedConnection:Disconnect()
                end
                characterAddedConnection = player.CharacterAdded:Connect(function(character)
                    game.Workspace.CurrentCamera.CameraSubject = character
                end)
                OrionLib:MakeNotification({
                    Name = "View",
                    Content = "You're watching: " .. player.Name,
                    Image = "rbxassetid://4483345998",
                    Time = 6
                })
            else
                print("Jogador não encontrado.")
                viewEnabled = false
            end
        else
            print("Nenhum jogador selecionado para a visualização.")
            viewEnabled = false
        end
    else
        if characterAddedConnection then
            characterAddedConnection:Disconnect()
            characterAddedConnection = nil
        end
        game.Workspace.CurrentCamera.CameraSubject = game.Players.LocalPlayer.Character
    end
end

-- Função para encontrar jogador por nome parcial ou apelido (usando apenas as 2 primeiras letras)
local function findPlayerByPartialNameOrNickname(partialName)
    local partial = partialName:sub(1, 2):lower()
    for _, player in ipairs(game.Players:GetPlayers()) do
        if player.Name:lower():sub(1, 2) == partial or (player.DisplayName and player.DisplayName:lower():sub(1, 2) == partial) then
            return player
        end
    end
    return nil
end

-- Adicionar o textbox para entrada do nome parcial ou apelido do jogador
Tab:AddTextbox({
    Name = "Player Name -->",
    Default = "",
    TextDisappear = true,
    Callback = function(value)
        selectedViewPlayer = findPlayerByPartialNameOrNickname(value)
        if selectedViewPlayer then
            print("Jogador encontrado: " .. selectedViewPlayer.Name)
            if viewEnabled then
                toggleView(false)
                toggleView(true)
            end
        else
            print("Nenhum jogador encontrado com esse nome ou apelido.")
            if viewEnabled then
                toggleView(false)
            end
        end
    end
})

-- Adicionar o toggle "View" à seção "Visualização"
Tab:AddToggle({
    Name = "View",
    Default = false,
    Callback = function(enabled)
        viewEnabled = enabled
        toggleView(enabled)
    end
})

-- Lista de seleção de jogadores para "Goto"
local gotoPlayerList = {}
for _, player in ipairs(game.Players:GetPlayers()) do
    table.insert(gotoPlayerList, player.Name)
end

local selectedGotoPlayer = nil

-- Adicionar um ListPlayer para selecionar o jogador alvo para "Goto"
Tab:AddDropdown({
    Name = "Choose who you want to Goto",
    Description = "Selecione o jogador alvo para o Goto (couch)",
    Options = gotoPlayerList,
    Callback = function(playerName)
        selectedGotoPlayer = playerName
    end
})

-- Adicionar o botão "Goto" à seção "View/Goto"
Tab:AddButton({
    Name = "Goto",
    Description = "This player is not on the list",
    Callback = function()
        if selectedGotoPlayer then
            local player = game.Players:FindFirstChild(selectedGotoPlayer)
            if player then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame
            else
                print("Jogador não encontrado.")
            end
        else
            print("Nenhum jogador selecionado para o Goto.")
        end
    end
})

-- Conectar eventos de jogador removido
game.Players.PlayerRemoving:Connect(function(player)
    if selectedViewPlayer == player then
        selectedViewPlayer = nil
        if viewEnabled then
            toggleView(false)
            OrionLib:MakeNotification({
                Name = "kakah hub",
                Content = player.Name .. " has left",
                Image = "rbxassetid://4483345998",
                Time = 5
            })
        end
    end
end)

-- Função para manter a câmera no jogador selecionado
local function maintainView()
    while wait() do
        if viewEnabled and selectedViewPlayer then
            local player = selectedViewPlayer
            if player and game.Workspace.CurrentCamera.CameraSubject ~= player.Character then
                game.Workspace.CurrentCamera.CameraSubject = player.Character
            end
        end
    end
end

-- Iniciar a função de manutenção da câmera
spawn(maintainView)

local Section = Tab:AddSection({
	Name = "Others Kill"
})
local selectedKillAdvancedPlayer = nil
local couchEquipped = false

local function killAdvancedPlayer()
    if selectedKillAdvancedPlayer then
        local player = game.Players:FindFirstChild(selectedKillAdvancedPlayer)
        if player then
            -- Equipa o item 'Couch' no inventário se ainda não estiver equipado
            local backpack = game.Players.LocalPlayer.Backpack
            if backpack and not couchEquipped then
                local couch = backpack:FindFirstChild("Couch")
                if couch then
                    game.Players.LocalPlayer.Character.Humanoid:EquipTool(couch)
                    couchEquipped = true
                else
                    print("O item 'Couch' não foi encontrado no seu inventário.")
                end
            end
            
            -- Looping de teleportes no jogador selecionado da lista
            while true do
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame
                wait(0.0) -- Intervalo entre cada teleporte, ajuste conforme necessário
                
                -- Verifica se o jogador sentou no 'Couch' e realiza o teleporte para o céu
                if player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.SeatPart then
                    player.Character.HumanoidRootPart.CFrame = CFrame.new(0, 0, 0) -- Teleporta para cima
                    wait(0.0) -- Espera um pouco antes de teleportar de volta para evitar bugs
                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0, 0, 0) -- Teleporta para cima novamente
                    wait(0.0) -- Espera um pouco antes de teleportar de volta para evitar bugs
                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(505, -75, 143) -- Teleporta de volta para a posição original
                    break -- Sai do loop após teleportar de volta
                end
            end
            
            -- Remove o item 'Couch' da mão do jogador após o teleporte para o céu
            if couchEquipped then
                local backpack = game.Players.LocalPlayer.Backpack
                if backpack then
                    local couch = backpack:FindFirstChild("Couch")
                    if couch then
                        couch.Parent = nil -- Remove o 'Couch' do inventário
                        couchEquipped = false
                    end
                end
            end
        else
            print("Jogador não encontrado.")
        end
    else
        print("Nenhum jogador selecionado para o Bring Avançado.")
    end
end

-- Lista de Players para Bring Avançado
local killAdvancedPlayerList = {}
for _, player in ipairs(game.Players:GetPlayers()) do
    table.insert(killAdvancedPlayerList, player.Name)
end

Tab:AddDropdown({
    Name = "Choose who you want to Bring (couch)",
    Description = "Selecione o jogador alvo para o Bring Avançado",
    Options = killAdvancedPlayerList,
    Callback = function(playerName)
        selectedKillAdvancedPlayer = playerName
  end
})

Tab:AddButton({
    Name = "Bring",
    Description = "Equipa o item 'Couch' e teleporta o jogador selecionado",
    Callback = function()
        killAdvancedPlayer()
    end
})
Tab:AddButton({
	Name = "Kill Player (use the sofa)",
	Callback = function()
--Sofá 
	local args = {
    [1] = "PickingTools",
    [2] = "Couch"
}
 
game:GetService("ReplicatedStorage").RE:FindFirstChild("1Too1l"):InvokeServer(unpack(args))

--kill

loadstring(game:HttpGet("https://pastebin.com/raw/yFP31TBf"))()
  	end    
})
Tab:AddButton({
	Name = "Void player (use the sofa)",
	Callback = function()
loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/main/Void'))()
  	end    
})
local Tab = Window:MakeTab({
	Name = "Avatar",
	Icon = "rbxassetid://7743876054",
	PremiumOnly = false
})
local Section = Tab:AddSection({
	Name = "Reset Avatar"
})
Tab:AddButton({
	Name = "Refresh Character",
	Callback = function()
local args = {
    [1] = "OCA"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
end
})
local Section = Tab:AddSection({
	Name = "Cheat Players (Troll)"
})

Tab:AddButton({
Name = "Assassin",
	Callback = function()
	--Assain
 
	local args = {
    [1] = "wear",
    [2] = 15133320948
}
 
game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end
})
Tab:AddButton({
Name = "Small Men",
	Callback = function()
	--Assain
 
	local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 14731377941,
        [2] = 14731377894,
        [3] = 14731377875,
        [4] = 14731384498,
        [5] = 14731377938,
        [6] = 0
    },
    [3] = "Stick Bug"
}
 
game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
 
--Man
 
local args = {
    [1] = "wear",
    [2] = 6564572490
}
 
game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
 
--speed
 
game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 90
end
})
Tab:AddButton({
Name = "See the entire city of Brookhaven",
     Callback = function()
     local plr = game.Players.LocalPlayer
local char = plr.Character
local hrp = char.HumanoidRootPart
 
hrp.CFrame = CFrame.new(-157.49581909179688, 136.7017364501953, 123.78034210205078) 
       --block
       -- Create a new part
local redBlock = Instance.new("Part")
 
-- Set the properties of the part
redBlock.Size = Vector3.new(4, 2, 3) -- Adjust the size as needed
redBlock.Color = Color3.fromRGB(255, 0, 0) -- Set the color to red
redBlock.Position = Vector3.new(0, 10, 0) -- Adjust the position as needed
 
-- Parent the part to the workspace so it appears in the game
redBlock.Parent = game.Workspace
end
})
    
local Section = Tab:AddSection({
	Name = "head"
})
Tab:AddButton({
	Name = "Headless",
	Callback = function()
      		local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 1,
        [2] = 1,
        [3] = 1,
        [4] = 1,
        [5] = 1,
        [6] = 134082579
    },
    [3] = "by:kakah"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
  	end    
})
local Section = Tab:AddSection({
	Name = "Characters"
})
Tab:AddButton({
	Name = "The Overseer",
	Callback = function()
	local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 81725326,
        [2] = 81725366,
        [3] = 81725392,
        [4] = 1,
        [5] = 1,
        [6] = 1
    },
    [3] = "by:kakah"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
end
})
Tab:AddButton({
	Name = "French fries gang",
	Callback = function()
	local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 5392155773,
        [2] = 5392150804,
        [3] = 5392146467,
        [4] = 5392152751,
        [5] = 5392148570,
        [6] = 1
    },
    [3] = "by:kakah"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
end
})    
Tab:AddButton({
	Name = "Korblox",
	Callback = function()
      		local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 139607770,
        [2] = 139607625,
        [3] = 139607570,
        [4] = 139607718,
        [5] = 139607673,
        [6] = 1
    },
    [3] = "by:kakah"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
  	end    
})
Tab:AddButton({
	Name = "Ice Knight",
	Callback = function()
      		local args = {
    [1] = "CharacterChange",
    [2] = {
        [1] = 1,
        [2] = 139572697,
        [3] = 139572600,
        [4] = 139572888,
        [5] = 139572789,
        [6] = 139572973
    },
    [3] = "by:kakah"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avata1rOrigina1l"):FireServer(unpack(args))
  	end    
})
local Section = Tab:AddSection({
	Name = "Dominus"
})
Tab:AddButton({
	Name = "Dominus Frigidus",
	Callback = function()
    local args = {
    [1] = "wear",
    [2] = 48545806
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
  	end    
})
Tab:AddButton({
Name = "Dominus infernus",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 31101391
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Dominus Empyreus",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 64444871
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Dominus Empyreus",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 21070012
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Dominus Astra",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 162067148
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddDropdown({
	Name = "Dominus Fake",
	Default = "1",
	Options = {"Dominus Chocolate",},
	Callback = function(Value)
local args = {
    [1] = "wear",
    [2] = 16392602102
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
	end    
})
local Section = Tab:AddSection({
	Name = "Valk"
})
Tab:AddButton({
Name = "Valk Violet",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 1402432199
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Valk Emerald",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 2830437685
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Valk Ice",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 4390891467
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})
Tab:AddButton({
Name = "Valk Shine Time",
	Callback = function()
	local args = {
    [1] = "wear",
    [2] = 1180433861
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end    
})

local Tab = Window:MakeTab({
	Name = "House",
	Icon = "rbxassetid://10723407389",
	PremiumOnly = false
})
local Section = Tab:AddSection({
	Name = "House"
})
Tab:AddButton({
Name = "Delete Home",
     Callback = function()
	local args = {
    [1] = "PlayerSellHouse"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Player1sHous1eChoic1e"):FireServer(unpack(args))
   end
})
local Section = Tab:AddSection({
	Name = "House Config"
})
Tab:AddButton({
Name = "Open/Close Windows",
     Callback = function()
	local args = {
    [1] = "Curtains"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Player1sHous1e"):FireServer(unpack(args))
   end
})
Tab:AddButton({
Name = "Lock/Unlock Doors",
     Callback = function()
	local args = {
    [1] = "LockDoors"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Player1sHous1e"):FireServer(unpack(args))
   end
})
Tab:AddButton({
Name = "Open/Close Garage",
     Callback = function()
	local args = {
    [1] = "GarageDoor"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Player1sHous1e"):FireServer(unpack(args))
   end
})
local Section = Tab:AddSection({
	Name = "Fire House"
})
Tab:AddButton({
Name = "FIRE ON",
     Callback = function()
	local args = {
    [1] = "PlayerWantsFireOnFirePassNotShowingAnyone"
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Player1sHous1e"):FireServer(unpack(args))
   end
})   
local Tab = Window:MakeTab({
	Name = "Troling",
	Icon = "rbxassetid://2005276185",
	PremiumOnly = false
})   

 local Section = Tab:AddSection({
	Name = "Super Small"
})
Tab:AddButton({
Name = "Stay Small",
     Callback = function()
	local args = {
    [1] = "CharacterSizeDown",
    [2] = 4
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Clothe1s"):FireServer(unpack(args))
end
})
Tab:AddButton({
Name = "Back to Size",
     Callback = function()
	local args = {
    [1] = "CharacterSizeUp",
    [2] = 1
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Clothe1s"):FireServer(unpack(args))
end
})  
Tab:AddButton({
Name = "Rainbow Ghost",
     Callback = function()
     --Size
     
     local args = {
    [1] = "CharacterSizeDown",
    [2] = 4
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Clothe1s"):FireServer(unpack(args))

--Item

local args = {
    [1] = "wear",
    [2] = 173624651
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))

--outro

local args = {
    [1] = "wear",
    [2] = 141742418
}

game:GetService("ReplicatedStorage").RE:FindFirstChild("1Updat1eAvata1r"):FireServer(unpack(args))
end
}) 
local Section = Tab:AddSection({
	Name = "Player"
})

Tab:AddButton({
	Name = "Reset (click K)",
	Callback = function()
      		--This script was made for you to be able to kill/"reset" your character if resetting your character is not an option, or has 
--been taken out. This script will also work as a suicide command.

-- Function to handle player's death
local function forceResetAction()
    local player = game.Players.LocalPlayer
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.Health = 0
    end
end

-- Bind the function to a keyboard input (e.g., press 'K' to kill)
game:GetService("UserInputService").InputBegan:Connect(function(input, isProcessed)
    if isProcessed then
        return
    end

    if input.KeyCode == Enum.KeyCode.K then
        forceResetAction()
    end
end)

  	end    
})
    
Tab:AddButton({
	Name = "FlingGUI",
	Callback = function()
      		loadstring(game:HttpGet("h
