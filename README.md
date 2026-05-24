local ProxyLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/ProxyHubDev/ProxyLib/refs/heads/main/Documents/ProxyLibrary"))()
local Library  = ProxyLib.new()

-- Key System
local KS = Library:CreateKeySystem({
    Title = "ProxyHub",
    Theme = "Blue",
    Size  = Vector2.new(420, 265),
})

KS:CreateButton({
    Description = "Enter",
    Callback = function()
        if KS:GetText() == "proxy123" then
            KS:Destroy()

            -- Main Window
            local Window = Library:CreateWindow({
                Title    = "ProxyHub",
                Subtitle = "v2.0",
                Theme    = "Blue",
                TypeUI   = "Modern",
                ConfigPanel = {
                    Enabled  = true,
                    Theme    = true,
                    Fps      = true,
                    Ping     = true,
                    Profile  = true,
                    Language = true,
                },
            })

            -- VariÃ¡veis de Controle
            local SelectedPlayer = ""
            local AutoKill = false
            local AutoM1 = false
            local AutoSkills = {false, false, false, false}
            local AutoAwaken = false

            -- FunÃ§Ãµes Auxiliares
            local function GetPlayerList()
                local players = {}
                for _, p in pairs(game:GetService("Players"):GetPlayers()) do
                    if p ~= game.Players.LocalPlayer then
                        table.insert(players, p.Name)
                    end
                end
                return players
            end

            -- Aba Auto Farm
            Window:CreateSeparator({ Text = "Combat" })
            local TabFarm = Window:CreateTab({ Title = "Auto Farm", Icon = "rbxassetid://0" })

            TabFarm:CreateSection({ Text = "Player Farm" })

            local PlayerDropdown = TabFarm:CreateDropdown({
                Title    = "Select Player",
                Options  = GetPlayerList(),
                Default  = "",
                Callback = function(sel)
                    SelectedPlayer = sel
                end,
            })

            -- BotÃ£o para atualizar lista de jogadores
            TabFarm:CreateButton({
                Title = "Refresh Player List",
                Callback = function()
                    PlayerDropdown:UpdateOptions(GetPlayerList())
                end,
            })

            TabFarm:CreateToggle({
                Title    = "Auto Kill Player",
                Default  = false,
                Callback = function(v)
                    AutoKill = v
                    task.spawn(function()
                        while AutoKill do
                            pcall(function()
                                local target = game:GetService("Players"):FindFirstChild(SelectedPlayer)
                                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
                                end
                            end)
                            task.wait()
                        end
                    end)
                end,
            })

            -- Aba Settings
            Window:CreateSeparator({ Text = "Configuration" })
            local TabSettings = Window:CreateTab({ Title = "Settings", Icon = "rbxassetid://0" })

            TabSettings:CreateSection({ Text = "Abilities & Combat" })

            -- Loop genÃ©rico para Skills
            local function StartSkillLoop(index)
                task.spawn(function()
                    while AutoSkills[index] do
                        pcall(function()
                            local args = {{ Number = tostring(index), Request = "Skill" }}
                            game:GetService("Players").LocalPlayer.Character.Network:FireServer(unpack(args))
                        end)
                        task.wait(0.5) -- Delay preventivo
                    end
                end)
            end

            for i = 1, 4 do
                TabSettings:CreateToggle({
                    Title = "Auto Ability " .. i,
                    Default = false,
                    Callback = function(v)
                        AutoSkills[i] = v
                        if v then StartSkillLoop(i) end
                    end,
                })
            end

            TabSettings:CreateToggle({
                Title = "Auto Despertar",
                Default = false,
                Callback = function(v)
                    AutoAwaken = v
                    task.spawn(function()
                        while AutoAwaken do
                            pcall(function()
                                local args = {{ Request = "Awaken" }}
                                game:GetService("Players").LocalPlayer.Character.Network:FireServer(unpack(args))
                            end)
                            task.wait(1)
                        end
                    end)
                end,
            })

            TabSettings:CreateSeparatorLine()

            TabSettings:CreateToggle({
                Title = "Auto M1",
                Default = false,
                Callback = function(v)
                    AutoM1 = v
                    task.spawn(function()
                        while AutoM1 do
                            pcall(function()
                                local args = {{ Request = "M1Down" }}
                                game:GetService("Players").LocalPlayer.Character.Network:FireServer(unpack(args))
                            end)
                            task.wait(0.1)
                        end
                    end)
                end,
            })

        else
            KS:Notify({ Title = "Wrong Key", Text = "Try again.", Duration = 3 })
        end
    end,
})
