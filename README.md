--██████╗░██╗░░░██╗  ██████╗░░█████╗░███╗░░██╗██╗░░██╗░█████╗░░██████╗
--██╔══██╗╚██╗░██╔╝  ██╔══██╗██╔══██╗████╗░██║██║░██╔╝██╔══██╗██╔════╝
--██████╦╝░╚████╔╝░  ██████╔╝███████║██╔██╗██║█████═╝░██║░░██║╚█████╗░
--██╔══██╗░░╚██╔╝░░  ██╔══██╗██╔══██║██║╚████║██╔═██╗░██║░░██║░╚═══██╗
--██████╦╝░░░██║░░░  ██║░░██║██║░░██║██║░╚███║██║░╚██╗╚█████╔╝██████╔╝
--╚═════╝░░░░╚═╝░░░  ╚═╝░░╚═╝╚═╝░░╚═╝╚═╝░░╚══╝╚═╝░░╚═╝░╚════╝░╚═════╝░

-- Chargement de la bibliothèque
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

-- Création de la fenêtre de l'interface utilisateur
local Window = Library.CreateLib("Rankos | BlockSpin 🔓 Hub, V1", "DarkTheme")

-- Tab ESP
local Tab = Window:NewTab("ESP")
local Section = Tab:NewSection("Enjoy your ESP -- By Rankos")

-- Section pour "Light ESP"
Section:NewButton("Light ESP", "ButtonInfo", function()
    -- Fonction pour ajouter un Highlight à un personnage
    local function addHighlight(char)
        local High = Instance.new("Highlight")
        High.Parent = char
    end

    -- Boucle à travers tous les joueurs déjà présents et ajoute un Highlight à leur personnage
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character then
            addHighlight(player.Character)
        end
        -- Lorsqu'un joueur rejoint, on lui ajoute un Highlight
        player.CharacterAdded:Connect(function(char)
            addHighlight(char)
        end)
    end

    -- Lorsque un nouveau joueur rejoint, on lui ajoute un Highlight
    game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(char)
            addHighlight(char)
        end)
    end)
end)

-- Section pour "Name ESP"
Section:NewButton("Name ESP", "ButtonInfo", function()
    -- Fonction pour ajouter un ESP avec le nom du joueur
    local function addNameESP(char, player)
        local billboard = Instance.new("BillboardGui")
        billboard.Adornee = char
        billboard.Size = UDim2.new(0, 100, 0, 50)
        billboard.StudsOffset = Vector3.new(0, 5, 0)  -- Positionne l'ESP au-dessus du personnage
        billboard.AlwaysOnTop = true  -- Rendre l'ESP visible à travers les murs
        billboard.Parent = char

        local label = Instance.new("TextLabel")
        label.BackgroundTransparency = 1
        label.Size = UDim2.new(1, 0, 1, 0)
        label.Text = player.Name
        label.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Couleur du texte (blanc)
        label.TextStrokeTransparency = 0.5  -- Opacité du contour (plus bas = contour plus visible)
        label.TextSize = 16
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)  -- Couleur du contour (noir)

        -- Changer la police du texte
        label.Font = Enum.Font.GothamBold  -- Choisissez la police ici (exemple : GothamBold)

        label.Parent = billboard
    end

    -- Fonction pour ajouter le Name ESP à tous les joueurs existants et nouveaux
    local function addESPToAllPlayers()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                addNameESP(player.Character, player)
            end
            -- Lorsqu'un joueur rejoint, on lui ajoute un Name ESP
            player.CharacterAdded:Connect(function(char)
                addNameESP(char, player)
            end)
        end
    end

    -- Ajout du Name ESP aux joueurs existants
    addESPToAllPlayers()

    -- Lorsqu'un joueur rejoint, on lui ajoute un Name ESP
    game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(char)
            addNameESP(char, player)
        end)
    end)
end)

-- Section pour "Tool ESP"
Section:NewButton("Tool ESP (OP)", "ButtonInfo", function()
    -- Fonction pour ajouter un ESP avec le tool du joueur
    local function addToolESP(char, player)
        -- Fonction pour mettre à jour l'ESP avec le tool du joueur
        local function updateToolESP()
            -- Retirer l'ESP s'il existe déjà
            if player.Character:FindFirstChild("ToolESP") then
                player.Character.ToolESP:Destroy()
            end
            
            -- Trouver l'outil que le joueur tient dans ses mains
            local tool = char:FindFirstChildOfClass("Tool")
            
            -- Si le joueur a un outil, afficher son nom
            if tool then
                local billboard = Instance.new("BillboardGui")
                billboard.Name = "ToolESP"
                billboard.Adornee = char
                billboard.Size = UDim2.new(0, 100, 0, 50)
                -- Positionner l'ESP en dessous des pieds du joueur
                billboard.StudsOffset = Vector3.new(0, -3, 0)  -- Décalage vers le bas (sous les pieds)
                billboard.AlwaysOnTop = true  -- Rendre l'ESP visible à travers les murs
                billboard.Parent = char

                local label = Instance.new("TextLabel")
                label.BackgroundTransparency = 1
                label.Size = UDim2.new(1, 0, 1, 0)
                label.Text = tool.Name  -- Affiche le nom du tool
                label.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Couleur du texte (blanc)
                label.TextStrokeTransparency = 0.5  -- Opacité du contour (plus bas = contour plus visible)
                label.TextSize = 17
                label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)  -- Couleur du contour (noir)

                -- Changer la police du texte
                label.Font = Enum.Font.Arial  -- Choisissez la police ici (exemple : GothamBold)

                label.Parent = billboard

                -- Assurer que l'ESP suit le joueur
                game:GetService("RunService").RenderStepped:Connect(function()
                    if billboard.Adornee and billboard.Adornee.Parent then
                        -- Mettre à jour la position de l'ESP
                        billboard.StudsOffset = Vector3.new(0, -3, 0)  -- Garder l'offset constant sous les pieds du joueur
                    end
                end)
            end
        end

        -- Observer les changements de tool
        player.Character.ChildAdded:Connect(function(child)
            if child:IsA("Tool") then
                updateToolESP()
            end
        end)

        player.Character.ChildRemoved:Connect(function(child)
            if child:IsA("Tool") then
                updateToolESP()
            end
        end)

        -- Mettre à jour l'ESP lorsque le personnage est déjà équipé d'un tool
        updateToolESP()
    end

    -- Fonction pour ajouter le Tool ESP à tous les joueurs existants et nouveaux
    local function addESPToAllPlayers()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                addToolESP(player.Character, player)
            end
            -- Lorsqu'un joueur rejoint, on lui ajoute un Tool ESP
            player.CharacterAdded:Connect(function(char)
                addToolESP(char, player)
            end)
        end
    end

    -- Ajout du Tool ESP aux joueurs existants
    addESPToAllPlayers()

    -- Lorsqu'un joueur rejoint, on lui ajoute un Tool ESP
    game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(char)
            addToolESP(char, player)
        end)
    end)
end)


-- Section pour "Health ESP" avec les couleurs modifiées en fonction de la santé
Section:NewButton("Health ESP", "ButtonInfo", function()
    -- Fonction pour ajouter un ESP avec la santé du joueur
    local function addHealthESP(char, player)
        -- Supprimer l'ancien Health ESP si présent
        local existingESP = char:FindFirstChild("HealthESP")
        if existingESP then
            existingESP:Destroy()
        end

        -- Création du BillboardGui pour afficher la santé
        local billboard = Instance.new("BillboardGui")
        billboard.Name = "HealthESP"  -- Nommer l'ESP pour pouvoir le supprimer facilement plus tard
        billboard.Adornee = char
        billboard.Size = UDim2.new(0, 90, 0, 40)
        billboard.AlwaysOnTop = true
        billboard.Parent = char

        -- Création du label pour afficher la santé
        local label = Instance.new("TextLabel")
        label.BackgroundTransparency = 1
        label.Size = UDim2.new(1, 0, 1, 0)
        label.Text = "Health: " .. math.floor(player.Character.Humanoid.Health)
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextStrokeTransparency = 0.5
        label.TextSize = 13
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        label.Font = Enum.Font.GothamBold
        label.Parent = billboard

        -- Fonction pour mettre à jour la couleur de la santé
        local function updateHealthColor()
            local health = player.Character.Humanoid.Health
            if health > 75 then
                label.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Vert si la santé est supérieure à 75
            elseif health >= 35 then
                label.TextColor3 = Color3.fromRGB(255, 165, 0)  -- Orange si la santé est entre 35 et 75
            else
                label.TextColor3 = Color3.fromRGB(255, 0, 0)  -- Rouge si la santé est entre 0 et 35
            end
        end

        -- Met à jour la couleur initiale
        updateHealthColor()

        -- Mettre à jour la santé si elle change
        player.Character.Humanoid.HealthChanged:Connect(function()
            label.Text = "Health: " .. math.floor(player.Character.Humanoid.Health)
            updateHealthColor()
        end)

        -- Assurer que l'ESP reste toujours au-dessus de la tête du joueur
        game:GetService("RunService").RenderStepped:Connect(function()
            if char and char:FindFirstChild("Head") then
                -- Positionner l'ESP au-dessus de la tête du joueur
                billboard.StudsOffset = Vector3.new(0, 3, 0)  -- Ajustez cette valeur pour définir la position (3 studs au-dessus de la tête)
                -- Assurez-vous que l'ESP reste en place même si la caméra se déplace
                local headPosition = char.Head.Position + Vector3.new(0, 3, 0)  -- Position du label 3 studs au-dessus de la tête
                billboard.CFrame = CFrame.new(headPosition)  -- Déplacez l'ESP à cette position
            end
        end)
    end

    -- Ajouter le Health ESP à tous les joueurs existants et nouveaux
    local function addESPToAllPlayers()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                addHealthESP(player.Character, player)
            end
            player.CharacterAdded:Connect(function(char)
                addHealthESP(char, player)
            end)
        end
    end

    addESPToAllPlayers()

    -- Lorsqu'un joueur rejoint, on lui ajoute un Health ESP
    game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(char)
            addHealthESP(char, player)
        end)
    end)
end)


Section:NewButton("Show BackPack (ULTRA OP!)", "ButtonInfo", function()
    -- Obtenez le joueur local
    local player = game.Players.LocalPlayer
    local mouse = player:GetMouse()  -- Obtenez l'objet Mouse du joueur local
    
    local screenGui = nil  -- Variable pour garder une référence à la GUI
    
    -- Fonction pour vérifier si le curseur est sur un joueur
    local function onHover()
        -- Vérifie si le curseur pointe sur un personnage
        local target = mouse.Target
        if target and target.Parent and target.Parent:FindFirstChild("Humanoid") then
            -- Si le curseur est sur un joueur et que la GUI n'est pas déjà ouverte
            if not screenGui then
                local character = target.Parent
                local playerOnHover = game.Players:GetPlayerFromCharacter(character)
                
                if playerOnHover then
                    -- Création de la GUI
                    screenGui = Instance.new("ScreenGui")
                    screenGui.Parent = player:WaitForChild("PlayerGui")
                    
                    local frame = Instance.new("Frame")
                    frame.Size = UDim2.new(0, 300, 0, 250)  -- Augmenter la taille pour les tools
                    frame.Position = UDim2.new(0, 50, 0, 0)  -- Décalage à droite de 50 pixels
                    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                    frame.Parent = screenGui
                    
                    -- Ajouter un titre à la GUI avec le nom du joueur + "BackPack"
                    local titleLabel = Instance.new("TextLabel")
                    titleLabel.Size = UDim2.new(1, 0, 0, 40)
                    titleLabel.Position = UDim2.new(0, 0, 0, 0)
                    titleLabel.Text = playerOnHover.Name .. "'s Backpack-ByRankos"  -- Afficher le nom du joueur suivi de "Backpack"
                    titleLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                    titleLabel.TextSize = 14  -- Réduire la taille du texte
                    titleLabel.Parent = frame

                    -- Fonction pour afficher les tools dans le Backpack
                    local function displayToolsInBackpack()
                        local yOffset = 45  -- Position de départ sous le titre
                        
                        -- Vider tout le contenu précédent avant de réafficher
                        for _, child in pairs(frame:GetChildren()) do
                            if child:IsA("TextLabel") and child ~= titleLabel then
                                child:Destroy()  -- Détruire les anciens labels des outils
                            end
                        end
                        
                        -- Parcourir le Backpack du joueur et ajouter chaque tool sous le titre
                        for _, tool in pairs(playerOnHover.Backpack:GetChildren()) do
                            if tool:IsA("Tool") then
                                local toolLabel = Instance.new("TextLabel")
                                toolLabel.Size = UDim2.new(1, 0, 0, 20)
                                toolLabel.Position = UDim2.new(0, 0, 0, yOffset)
                                toolLabel.Text = tool.Name
                                toolLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                                toolLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                                toolLabel.TextSize = 12
                                toolLabel.TextStrokeTransparency = 0.5
                                toolLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
                                toolLabel.Font = Enum.Font.GothamBold
                                toolLabel.Parent = frame
                                yOffset = yOffset + 25  -- Décalage pour le prochain outil
                            end
                        end
                    end

                    -- Afficher les tools au moment où la GUI est ouverte
                    displayToolsInBackpack()

                    -- Ajouter un événement pour mettre à jour la liste des tools en temps réel
                    playerOnHover.Backpack.ChildAdded:Connect(function(child)
                        if child:IsA("Tool") then
                            displayToolsInBackpack()  -- Mettre à jour l'affichage des tools
                        end
                    end)

                    -- Ajouter un événement pour supprimer un tool
                    playerOnHover.Backpack.ChildRemoved:Connect(function(child)
                        if child:IsA("Tool") then
                            displayToolsInBackpack()  -- Mettre à jour l'affichage des tools
                        end
                    end)
                end
            end
        else
            -- Si le curseur n'est pas sur un joueur, fermer la GUI immédiatement
            if screenGui then
                screenGui:Destroy()
                screenGui = nil  -- Réinitialiser la variable pour pouvoir recréer la GUI plus tard
            end
        end
    end

    -- Connecter la fonction à l'événement `MouseMoved`
    mouse.Move:Connect(onHover)
end)

