local dwEntities = game:GetService("Players")
local dwLocalPlayer = dwEntities.LocalPlayer 
local dwRunService = game:GetService("RunService")

local settings_tbl = {
    ESP_Enabled = true,
    ESP_TeamCheck = true,
    Chams = true,
    Chams_Color = Color3.fromRGB(0, 255, 0),      -- Зеленая обводка
    Chams_Transparency = 0.1,
    Chams_Glow_Color = Color3.fromRGB(0, 100, 0), -- Темно-зеленое свечение
    Chams_Thickness = 0.02,                       -- Толщина основной обводки
    Glow_Thickness = 0.13                         -- Толщина свечения
}

function destroy_chams(char)
    if not char then return end
    for _, part in ipairs(char:GetChildren()) do 
        if part:IsA("BasePart") then
            if part:FindFirstChild("Chams") then
                part.Chams:Destroy()
            end
            if part:FindFirstChild("Glow") then
                part.Glow:Destroy()
            end
        end
    end 
end

dwRunService.Heartbeat:Connect(function()
    if settings_tbl.ESP_Enabled then
        for _, player in ipairs(dwEntities:GetPlayers()) do 
            if player ~= dwLocalPlayer and player.Character then
                local char = player.Character
                local humanoid = char:FindFirstChild("Humanoid")
                local root = char:FindFirstChild("HumanoidRootPart")
                
                if humanoid and humanoid.Health > 0 and root then
                    if not settings_tbl.ESP_TeamCheck or player.Team ~= dwLocalPlayer.Team then
                        if settings_tbl.Chams then
                            for _, part in ipairs(char:GetChildren()) do 
                                if part:IsA("BasePart") and part.Transparency < 1 then
                                    -- Создаем зеленую обводку
                                    if not part:FindFirstChild("Chams") then
                                        local chams_box = Instance.new("BoxHandleAdornment")
                                        chams_box.Name = "Chams"
                                        chams_box.AlwaysOnTop = true 
                                        chams_box.ZIndex = 4 
                                        chams_box.Adornee = part
                                        chams_box.Color3 = settings_tbl.Chams_Color
                                        chams_box.Transparency = settings_tbl.Chams_Transparency
                                        chams_box.Size = part.Size + Vector3.new(
                                            settings_tbl.Chams_Thickness,
                                            settings_tbl.Chams_Thickness,
                                            settings_tbl.Chams_Thickness
                                        )
                                        chams_box.Parent = part
                                    end
                                    
                                    -- Создаем темно-зеленое свечение
                                    if not part:FindFirstChild("Glow") then
                                        local glow_box = Instance.new("BoxHandleAdornment")
                                        glow_box.Name = "Glow"
                                        glow_box.AlwaysOnTop = false 
                                        glow_box.ZIndex = 3 
                                        glow_box.Adornee = part
                                        glow_box.Color3 = settings_tbl.Chams_Glow_Color
                                        glow_box.Size = part.Size + Vector3.new(
                                            settings_tbl.Glow_Thickness,
                                            settings_tbl.Glow_Thickness,
                                            settings_tbl.Glow_Thickness
                                        )
                                        glow_box.Parent = part
                                    end
                                end
                            end
                        else
                            destroy_chams(char)
                        end
                    else
                        destroy_chams(char)
                    end
                else
                    destroy_chams(char)
                end
            end
        end
    else 
        for _, player in ipairs(dwEntities:GetPlayers()) do 
            if player ~= dwLocalPlayer and player.Character then
                destroy_chams(player.Character)
            end
        end
    end
end)
