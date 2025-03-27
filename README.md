--██████╗░██╗░░░██╗  ██████╗░░█████╗░███╗░░██╗██╗░░██╗░█████╗░░██████╗
--██╔══██╗╚██╗░██╔╝  ██╔══██╗██╔══██╗████╗░██║██║░██╔╝██╔══██╗██╔════╝
--██████╦╝░╚████╔╝░  ██████╔╝███████║██╔██╗██║█████═╝░██║░░██║╚█████╗░
--██╔══██╗░░╚██╔╝░░  ██╔══██╗██╔══██║██║╚████║██╔═██╗░██║░░██║░╚═══██╗
--██████╦╝░░░██║░░░  ██║░░██║██║░░██║██║░╚███║██║░╚██╗╚█████╔╝██████╔╝
--╚═════╝░░░░╚═╝░░░  ╚═╝░░╚═╝╚═╝░░╚═╝╚═╝░░╚══╝╚═╝░░╚═╝░╚════╝░╚═════╝░

-- Chargement de la bibliothèque
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

-- Création de la fenêtre de l'interface utilisateur
local Window = Library.CreateLib("Rankos | BlockSpin 🔓 Hub, V1", "BloodTheme")

-- Tab ESP
local Tab = Window:NewTab("ESP")
local Section = Tab:NewSection("Enjoy your ESP -- By Rankos")

Section:NewButton("BOX + NAME (ESP)", "Active l'ESP Box", function()
    local RunService = game:GetService("RunService")
    local Players = game:GetService("Players")
    local Camera = game.Workspace.CurrentCamera
    local LocalPlayer = Players.LocalPlayer

    local ESP_Boxes = {}

    -- Fonction pour créer une boîte et ajouter le pseudo avec des crochets
    local function createBox(player)
        if player == LocalPlayer then return end -- Ne pas dessiner la box pour soi-même

        local box = Drawing.new("Square")
        box.Thickness = 2
        box.Color = Color3.fromRGB(255, 255, 255) -- Blanc
        box.Filled = false
        box.Visible = false

        -- Ajouter le pseudo avec des crochets autour du nom
        local nameLabel = Drawing.new("Text")
        nameLabel.Text = "[" .. player.Name .. "]"  -- Ajouter des crochets autour du pseudo
        nameLabel.Size = 14  -- Taille du texte réduite à 14
        nameLabel.Color = Color3.fromRGB(0, 0, 0)  -- Couleur noire pour le texte
        nameLabel.Visible = false  -- Ne visible que lorsqu'il est nécessaire
        nameLabel.Font = 4  -- Police 'Comic' pour un style amusant

        ESP_Boxes[player] = { box = box, label = nameLabel }
    end

    -- Fonction pour mettre à jour l'ESP
    local function updateESP()
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = player.Character.HumanoidRootPart
                local screenPos, onScreen = Camera:WorldToViewportPoint(hrp.Position)

                local esp = ESP_Boxes[player]
                if esp then
                    local box = esp.box
                    local label = esp.label

                    if onScreen then
                        local head = player.Character:FindFirstChild("Head")
                        local rootPos, rootVisible = Camera:WorldToViewportPoint(hrp.Position)
                        local headPos, headVisible = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))

                        if rootVisible and headVisible then
                            local height = math.abs(headPos.Y - rootPos.Y)
                            local width = height * 0.40 -- Largeur du carré blanc réduite

                            -- Position et taille du carré blanc (agrandi)
                            box.Size = Vector2.new(width + 18, height + 18)  -- Carré blanc agrandi de 18 pixels
                            box.Position = Vector2.new(rootPos.X - (width + 18) / 2, rootPos.Y - (height + 18) / 2)
                            box.Visible = true

                            -- Afficher le pseudo avec des crochets autour et décaler un peu vers la gauche
                            local xOffset = -20  -- Déplacer le pseudo un peu plus à gauche (valeur négative)
                            label.Position = Vector2.new(rootPos.X - (width + 18) / 2 - 10 + xOffset, rootPos.Y - (height + 18) / 2 - 20)

                            label.Visible = true
                        else
                            box.Visible = false
                            label.Visible = false
                        end
                    else
                        box.Visible = false
                        label.Visible = false
                    end
                end
            end
        end
    end

    -- Nettoyage des ESP lorsque le joueur quitte
    local function removeESP(player)
        if ESP_Boxes[player] then
            ESP_Boxes[player].box:Remove()
            ESP_Boxes[player].label:Remove()  -- Enlever le label aussi
            ESP_Boxes[player] = nil
        end
    end

    -- Création des ESP pour les joueurs actuels
    for _, player in pairs(Players:GetPlayers()) do
        createBox(player)
    end

    -- Écoute les nouveaux joueurs qui rejoignent
    Players.PlayerAdded:Connect(createBox)
    Players.PlayerRemoving:Connect(removeESP)

    -- Mettre à jour l'ESP en permanence
    RunService.RenderStepped:Connect(updateESP)
end)



-- Section pour "Light ESP"
Section:NewButton("Light ESP", "ButtonInfo", function()
    -- Fonction pour ajouter un Highlight à un personnage
    local function addHighlight(char)
        local High = Instance.new("Highlight")
        High.Parent = char

        -- Définir la couleur du contour en noir
        High.OutlineColor = Color3.fromRGB(0, 0, 0)  -- Noir

        -- Rendre la partie cachée du personnage transparente et violette
        High.FillColor = Color3.fromRGB(255, 0, 255) -- Violet
        High.FillTransparency = 0.85 -- Rendre l'intérieur transparent
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
                -- Positionner l'ESP un peu plus bas sous les pieds du joueur
                billboard.StudsOffset = Vector3.new(0, -5, 0)  -- Décalage plus bas (sous les pieds)
                billboard.AlwaysOnTop = true  -- Rendre l'ESP visible à travers les murs
                billboard.Parent = char

                local label = Instance.new("TextLabel")
                label.BackgroundTransparency = 1
                label.Size = UDim2.new(1, 0, 1, 0)
                label.Text = "Holding: [" .. tool.Name .. "]"  -- Affiche le nom du tool
                label.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Couleur du texte (blanc)
                label.TextStrokeTransparency = 0.5  -- Opacité du contour (plus bas = contour plus visible)
                label.TextSize = 14  -- Taille réduite du texte
                label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)  -- Couleur du contour (noir)
                
                -- Choisir une police plus épaisse pour rendre le texte un peu plus gras
                label.Font = Enum.Font.SourceSansBold  -- Police en gras (SourceSansBold)

                label.Parent = billboard

                -- Assurer que l'ESP suit le joueur
                game:GetService("RunService").RenderStepped:Connect(function()
                    if billboard.Adornee and billboard.Adornee.Parent then
                        -- Garder l'offset constant sous les pieds du joueur
                        billboard.StudsOffset = Vector3.new(0, -5, 0)  -- Maintenir un décalage sous les pieds
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
        label.Text = "❤️ " .. math.floor(player.Character.Humanoid.Health)
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextStrokeTransparency = 0.5
        label.TextSize = 13  -- Taille réduite du texte
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        label.Font = Enum.Font.GothamBold
        label.ZIndex = 10  -- Assurer que le texte soit au-dessus de tout autre texte
        label.Parent = billboard

        -- Positionner le texte plus bas
        label.Position = UDim2.new(0, 0, 0, 30)  -- Ajuste cette valeur pour déplacer le texte plus bas si nécessaire

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

        -- Assurer que l'ESP reste toujours sous la boîte du joueur (Box ESP)
        game:GetService("RunService").RenderStepped:Connect(function()
            if char and char:FindFirstChild("HumanoidRootPart") then
                -- Récupérer la position de la HumanoidRootPart pour ajuster la position du label
                local rootPos, rootVisible = game.Workspace.CurrentCamera:WorldToViewportPoint(char.HumanoidRootPart.Position)
                
                if rootVisible then
                    -- Calculer la position de la boîte (l'ESP Box) et ajuster la position du Health ESP juste en dessous
                    local boxESP = ESP_Boxes[player] and ESP_Boxes[player].box
                    if boxESP then
                        -- Positionner le label juste en dessous de la box
                        label.Position = UDim2.new(0, boxESP.Position.X, 0, boxESP.Position.Y + boxESP.Size.Y + 20)  -- 20 pixels en dessous de la boîte
                        billboard.StudsOffset = Vector3.new(0, 0, 0)  -- Ne pas changer l'offset Z pour rester stable
                    end
                else
                    billboard.Visible = false  -- Cacher l'ESP si le joueur n'est pas visible
                end
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
                    
                    -- Création du Frame (avec un UI Gradient, une bordure grise et des coins arrondis)
                    local frame = Instance.new("Frame")
                    frame.Size = UDim2.new(0, 300, 0, 250)  -- Augmenter la taille pour les tools
                    frame.Position = UDim2.new(0, 50, 0, 0)  -- Décalage à droite de 50 pixels
                    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                    frame.Parent = screenGui

                    -- Ajouter un dégradé à l'arrière-plan du frame
                    local gradient = Instance.new("UIGradient")
                    gradient.Parent = frame
                    gradient.Color = ColorSequence.new({ColorSequenceKeypoint.new(0, Color3.fromRGB(100, 100, 100)), ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 50, 50))})
                    gradient.Rotation = 45  -- Inclinaison du gradient (tu peux changer l'angle ici)

                    -- Ajouter un UICorner pour arrondir les coins
                    local corner = Instance.new("UICorner")
                    corner.CornerRadius = UDim.new(0, 12)  -- Coins arrondis avec un rayon de 12 pixels
                    corner.Parent = frame

                    -- Ajouter un UIStroke pour la bordure grise
                    local stroke = Instance.new("UIStroke")
                    stroke.Color = Color3.fromRGB(150, 150, 150)  -- Bordure grise
                    stroke.Thickness = 2  -- Épaisseur de la bordure
                    stroke.Parent = frame
                    
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

    -- Connecter la fonction à l'événement MouseMoved
    mouse.Move:Connect(onHover)
end)


